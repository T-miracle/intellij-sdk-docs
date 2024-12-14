<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 扩展点

<link-summary>允许通过其他插件扩展插件的功能。</link-summary>

> 请参阅 [插件扩展](plugin_extensions.md) 了解插件中_使用_扩展点。
>
{style="note"}

通过在插件中定义 _扩展点_，你可以允许其他插件扩展你的插件功能。扩展点有两种类型：

* _接口_ 扩展点允许其他插件用 _代码_ 扩展你的插件。
  定义接口扩展点时，指定一个接口，其他插件将提供实现该接口的类。
  提供插件随后可以调用该接口上的方法。
  在大多数情况下，接口可以使用 `@ApiStatus.OverrideOnly` 注解（见 [](verifying_plugin_compatibility.md#override-only-api)）。

* _Bean_ 扩展点允许其他插件用 _数据_ 扩展插件。
  指定扩展类的全限定名，其他插件将提供数据，这些数据会被转换为该类的实例。

## 声明扩展点 {id=declaring-extension-points}

你可以在插件配置文件 <path>[plugin.xml](plugin_configuration_file.md)</path> 中声明扩展和扩展点，位于 [`<extensions>`](plugin_configuration_file.md#idea-plugin__extensions) 和 [`<extensionPoints>`](plugin_configuration_file.md#idea-plugin__extensionPoints) 部分。

要在插件中声明扩展点，向 <path>plugin.xml</path> 中添加一个 `<extensionPoints>` 部分。
然后插入一个子元素 [`<extensionPoint>`](plugin_configuration_file.md#idea-plugin__extensionPoints__extensionPoint)，该元素定义扩展点的名称，以及允许扩展插件功能的 bean 类或接口的名称，分别在 `name`、`beanClass` 和 `interface` 属性中指定。

<path>myPlugin/META-INF/plugin.xml</path>

```xml
<idea-plugin>
  <id>my.plugin</id>

  <extensionPoints>
    <extensionPoint
            name="myExtensionPoint1"
            beanClass="com.example.MyBeanClass"/>

    <extensionPoint
            name="myExtensionPoint2"
            interface="com.example.MyInterface"/>
  </extensionPoints>

</idea-plugin>
```

`name` 属性为此扩展点分配一个唯一名称。  
其完全限定名（在 [使用扩展点](#using-extension-points) 中需要）通过在插件的 [`<id>`](plugin_configuration_file.md#idea-plugin__id) 前加上 "namespace" 前缀，并后跟 `.` 分隔符构成：`my.plugin.myExtensionPoint1` 和 `my.plugin.myExtensionPoint2`。

`beanClass` 属性设置一个 bean 类，该类指定一个或多个使用 [`@Attribute`](%gh-ic%/platform/util/src/com/intellij/util/xmlb/annotations/Attribute.java) 注解标记的属性。  
需要注意的是，bean 类不遵循 JavaBean 标准。  
实现 [`PluginAware`](%gh-ic%/platform/extensions/src/com/intellij/openapi/extensions/PluginAware.java) 可以获取提供实际扩展的插件信息（见 [](#error-handling)）。

另外，`interface` 属性设置插件必须实现的接口，以便为扩展点贡献扩展。

`area` 属性确定扩展实例化的范围。  
由于扩展应该是无状态的，因此**不建议**使用非默认值。  
该值必须是 `IDEA_APPLICATION` 表示应用级别（默认），`IDEA_PROJECT` 表示项目级别，或 `IDEA_MODULE` 表示模块级别。

贡献扩展点的插件将从 <path>plugin.xml</path> 文件中读取这些属性。

如果扩展实现根据 [愚蠢模式](indexing_and_psi_stubs.md#dumb-mode) 进行过滤，则基类应标记为 [`PossiblyDumbAware`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/PossiblyDumbAware.java) 以突出显示这一点。  
使用 [`DumbService.getDumbAwareExtensions()`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/DumbService.kt) 来检索支持愚蠢模式的实现。

需要键的扩展的基类：

- [`LanguageExtension`](%gh-ic%/platform/core-api/src/com/intellij/lang/LanguageExtension.java)
- [`FileTypeExtension`](%gh-ic%/platform/core-api/src/com/intellij/openapi/fileTypes/FileTypeExtension.java)
- [`ClassExtension`](%gh-ic%/platform/core-api/src/com/intellij/openapi/util/ClassExtension.java)
- [`KeyedExtensionCollector`](%gh-ic%/platform/core-api/src/com/intellij/openapi/util/KeyedExtensionCollector.java)

> 参见 [](bundling_plugin_openapi_sources.md) 部分，解释如何将扩展点源暴露给其他插件。
>
{style="note"}

### 示例 {id=sample}

为了说明这一点，考虑以下示例 `MyBeanClass` bean 类，它在上述 <path>plugin.xml</path> 文件中使用：

<path>myPlugin/src/com/myplugin/MyBeanClass.java</path>

```java
public final class MyBeanClass extends AbstractExtensionPointBean {

  @Attribute("key")
  public String key;

  @Attribute("implementationClass")
  public String implementationClass;

  public String getKey() {
    return key;
  }

  public String getClass() {
    return implementationClass;
  }

}
```

> 请参见 [扩展属性代码洞察](plugin_extensions.md#扩展属性代码洞察)，了解如何提供智能补全/验证。
>

对于上述扩展点，在 _anotherPlugin_ 中的使用方式如下（另见 [声明扩展](plugin_extensions.md#declaring-extensions)）：

<path>anotherPlugin/META-INF/plugin.xml</path>

```xml
<idea-plugin>
  <id>another.plugin</id>

  <!-- 声明对定义扩展点插件的依赖： -->
  <depends>my.plugin</depends>

  <!-- 使用 "my.plugin" 命名空间： -->
  <extensions defaultExtensionNs="my.plugin">
    <myExtensionPoint1
            key="someKey"
            implementationClass="another.some.implementation.class"/>

    <myExtensionPoint2
            implementation="another.MyInterfaceImpl"/>
  </extensions>

</idea-plugin>
```

## 使用扩展点 {id=using-extension-points}

要在运行时引用所有已注册的扩展实例，请声明一个具有私有可见性的 [`ExtensionPointName`](%gh-ic%/platform/extensions/src/com/intellij/openapi/extensions/ExtensionPointName.kt)，并传入与其 [在 plugin.xml 中的声明](#declaring-extension-points) 匹配的完全限定名。  
如果需要，提供一个公共方法来查询已注册的扩展（示例：[`TestSourcesFilter.isTestSources()`](%gh-ic%/platform/projectModel-api/src/com/intellij/openapi/roots/TestSourcesFilter.java)）。

<path>myPlugin/src/com/myplugin/MyExtensionUsingService.java</path>

```java
@Service
public final class MyExtensionUsingService {

  private static final ExtensionPointName<MyBeanClass> EP_NAME =
      ExtensionPointName.create("my.plugin.myExtensionPoint1");

  public void useRegisteredExtensions() {
    for (MyBeanClass extension : EP_NAME.getExtensionList()) {
      String key = extension.getKey();
      String clazz = extension.getClass();
      // ...
    }
  }

}
```

`ExtensionPointName` 声明的代码边栏图标允许导航到 <path>plugin.xml</path> 中相应的 [`<extensionPoint>`](plugin_configuration_file.md#idea-plugin__extensionPoints__extensionPoint) 声明。  
对于扩展点名称的字符串字面量（2022.3版本），可以提供代码洞察。

### 错误处理 {id=error-handling}

在处理扩展实现或注册时，可能会遇到错误、兼容性和配置问题。  
使用 [`PluginException`](%gh-ic%/platform/core-api/src/com/intellij/diagnostic/PluginException.java) 来记录并正确归因于导致问题的插件，以便进行 [内建错误报告](ide_infrastructure.md#error-reporting)。

要报告使用了已弃用的 API，请使用 `PluginException.reportDeprecatedUsage()` 方法。

**示例：**
- [`CompositeFoldingBuilder.assertSameFile()`](%gh-ic%/platform/core-api/src/com/intellij/lang/folding/CompositeFoldingBuilder.java)
- [`InspectionProfileEntry.getDisplayName()`](%gh-ic%/platform/analysis-api/src/com/intellij/codeInspection/InspectionProfileEntry.java)

## 动态扩展点 {id=dynamic-extension-points}

为了支持 [动态插件](dynamic_plugins.md)，扩展点必须遵循特定的使用规则：

- 每次使用时都会枚举扩展，扩展实例不会存储在任何地方
- 或者，可以使用 [`ExtensionPointListener`](%gh-ic%/platform/extensions/src/com/intellij/openapi/extensions/ExtensionPointListener.kt) 来执行任何必要的数据结构更新（通过 `ExtensionPointName.addExtensionPointListener()` 注册）

符合这些条件的扩展点可以通过在其声明中添加 `dynamic="true"` 来标记为 _动态_：

```xml
<extensionPoints>
  <extensionPoint
          name="myDynamicExtensionPoint"
          beanClass="com.example.MyBeanClass"
          dynamic="true"/>
</extensionPoints>
```

所有非动态扩展点将通过 <control>Plugin DevKit | Plugin descriptor | Plugin.xml dynamic plugin verification</control> 检查进行高亮显示。
