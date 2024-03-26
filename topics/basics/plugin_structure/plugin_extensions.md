# 扩展

<!-- Copyright 2000-2023 JetBrains s.r.o. and other contributors. Use of this source code is governed by the Apache 2.0 license that can be found in the LICENSE file. -->

<link-summary>扩展是 IDE 中自定义功能的最常见方式。</link-summary>

_扩展_ 是插件以不像将操作添加到菜单或工具栏那样直接的方式，来扩展 IntelliJ 平台功能的最常见方式。

以下是使用扩展完成的一些最常见任务：

* `com.intellij.toolWindow` 扩展点允许插件添加 [工具窗口](tool_windows.md)（显示在IDE用户界面两侧的面板）；
* `com.intellij.applicationConfigurable` 和 `com.intellij.projectConfigurable` 扩展点允许插件向 [设置对话框](settings.md) 添加页面；
* [自定义语言插件](custom_language_support.md) 使用多个扩展点来扩展 IDE 中各种语言支持功能。

在平台和捆绑插件中提供了1500多个扩展点，允许自定义 IDE 行为的不同部分。

## 探索可用的扩展点

[](extension_point_list.md) 列出了 IntelliJ Platform 和 IntelliJ IDEA 中捆绑插件中所有可用的扩展点。
此外，专用于IDE的扩展点和监听器列表可在 _Part VIII — Product Specific_ 下找到。
通过 [IntelliJ Platform Explorer](https://jb.gg/ipe) 浏览开源 IntelliJ Platform 插件的现有实现中的用法。

或者（或者在使用第三方扩展点时），可以在 <path>[plugin.xml](plugin_configuration_file.md)</path> 中的 [`<extensions>`](plugin_configuration_file.md#idea-plugin__extensions) 块中使用自动完成列出指定命名空间 (`defaultExtensionNs`) 的所有可用扩展点。
在查找列表中使用 <ui-path>View | Quick Documentation</ui-path> 可以访问有关扩展点和实现的更多信息（如果适用）。
有关更多信息和策略，请参见 [](explore_api.md)。

## 声明扩展 {id=声明扩展}

> 在 <path>plugin.xml</path> 文件的扩展点标签和属性上提供自动完成、快速文档和其他代码洞察功能。

<procedure title="声明扩展">

1. 如果尚未在 <path>plugin.xml</path> 中添加 [`<extensions>`](plugin_configuration_file.md#idea-plugin__extensions) 元素，请添加一个。
   将 `defaultExtensionNs` 属性设置为以下值之一：
   * `com.intellij`，如果您的插件扩展了 IntelliJ 平台的核心功能。
   * `{插件的 ID}`，如果您的插件扩展了另一个插件的功能（必须配置 [插件依赖项](plugin_dependencies.md)）。
2. 在 `<extensions>` 元素中添加一个新的子元素。
   子元素的名称必须与要扩展访问的扩展点的名称匹配。
3. 根据扩展点的类型，执行以下操作之一：
   * 如果使用 `interface` 属性声明了扩展点，请将 `implementation` 属性设置为实现指定接口的类的名称。
   * 如果使用 `beanClass` 属性声明了扩展点，请设置指定 bean 类中带有 [`@Attribute`](%gh-ic%/platform/util/src/com/intellij/util/xmlb/annotations/Attribute.java) 和 [`Tag`](%gh-ic%/platform/util/src/com/intellij/util/xmlb/annotations/Tag.java) 注解的所有属性。

   有关详细信息，请参见 [](plugin_extension_points.md#declaring-extension-points) 部分。
4. 根据需要实现扩展 API（请参见 [](#implementing-extension)）。

</procedure>

为了阐明这个过程，考虑以下示例 <path>plugin.xml</path> 文件的部分，定义了两个扩展，用于访问 IntelliJ 平台中的 `com.intellij.appStarter` 和 `com.intellij.projectTemplatesFactory` 扩展点，
以及一个扩展，用于访问另一个插件 `another.plugin` 中的 `another.plugin.myExtensionPoint` 扩展点：

```xml
<!--
  声明扩展以访问 IntelliJ 平台中的扩展点。
  这些扩展点已使用 "interface" 进行声明。
-->
<extensions defaultExtensionNs="com.intellij">
   <appStarter
           implementation="com.example.MyAppStarter"/>
   <projectTemplatesFactory
           implementation="com.example.MyProjectTemplatesFactory"/>
</extensions>

        <!--
          声明扩展以访问自定义插件 "another.plugin" 中的扩展点。
          "myExtensionPoint" 扩展点已使用 "beanClass" 进行声明，
          并公开自定义属性 "key" 和 "implementationClass"。
        -->
<extensions defaultExtensionNs="another.plugin">
<myExtensionPoint
        key="keyValue"
        implementationClass="com.example.MyExtensionPointImpl"/>
</extensions>
```

<procedure id="implementing-extension" title="实现扩展">

请注意以下重要点：

- 扩展实现必须是无状态的。使用显式的 [](plugin_services.md) 来管理（运行时）数据。
- 避免在构造函数中进行任何初始化，另请参阅 [服务](plugin_services.md#构造函数) 的注意事项。
- 不要执行任何静态初始化。使用检查 <control>Plugin DevKit | Code | Static initialization in extension point implementations</control>（2023.3）。
- 扩展实现不能额外注册为 [服务](plugin_services.md)。使用检查 <control>Plugin DevKit | Code | Extension registered as service/component</control>（2023.3）。

在使用 [Kotlin](using_kotlin.md) 时：

- 不要使用 `object`，而应使用 `class` 进行实现。[更多详细信息](using_kotlin.md#object-vs-class)
- 不要使用 `companion object`，以避免在加载扩展类时进行过多的类加载/初始化。
  而应改用顶层声明或对象。[更多详细信息](using_kotlin.md#companion-object-extensions)

</procedure>

### 扩展默认属性

`id` : 唯一标识符。考虑在 ID 前添加与插件名称或 ID 相关的前缀，以避免与定义相同 ID 的其他插件发生冲突，例如 `com.example.myplugin.myExtension`。

`order` : 允许使用 `first`、`last` 或 `before|after [id]` 来排序所有定义的扩展。

`os` : 允许将扩展限制为给定的操作系统，例如 `os="windows"` 仅在 Windows 上注册扩展。

如果扩展实例需要在某些场景下“不适用”，则可以在其构造函数中抛出 [`ExtensionNotApplicableException`](%gh-ic%/platform/extensions/src/com/intellij/openapi/extensions/ExtensionNotApplicableException.java)。

### 扩展属性代码洞察 {id=扩展属性代码洞察}

有几个工具功能可帮助在 <path>plugin.xml</path> 中配置 bean 类扩展点。

带有 [`RequiredElement`](%gh-ic%/platform/core-api/src/com/intellij/openapi/extensions/RequiredElement.java) 注解的属性会自动插入并进行验证（2019.3 及更高版本）。
如果给定的属性允许具有显式的空值，则将 `allowEmpty` 设置为 `true`（2020.3 及更高版本）。

与以下列表匹配的属性名称将解析为完全限定的类名：

- `implementation`
- `className`
- `serviceInterface` / `serviceImplementation`
- 以 `Class` 结尾的属性名称（区分大小写）

可以通过嵌套的 [`<with>`](plugin_configuration_file.md#idea-plugin__extensionPoints__extensionPoint__with) 在扩展点声明中指定所需的父类型：

```xml
<extensionPoint name="myExtension" beanClass="MyExtensionBean">
  <with
      attribute="psiElementClass"
      implements="com.intellij.psi.PsiElement"/>
</extensionPoint>
```

属性名称 `language`（或以 `*Language` 结尾，2020.2 及更高版本）将解析为所有存在的 `Language` ID。

类似地，`action` 解析为所有已注册的 [`<action>`](plugin_configuration_file.md#idea-plugin__actions__action) ID。

指定 `@org.jetbrains.annotations.Nls` 将根据文本属性 `Capitalization` 枚举值验证 UI `String` 的大小写（2019.2 及更高版本）。

标记为 `@Deprecated` 或带有任何 [`ApiStatus`](https://github.com/JetBrains/java-annotations/blob/master/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) 的注解，如 `@Internal`、`@Experimental`、`@ScheduledForRemoval` 或 `@Obsolete` 的属性将相应地突出显示。

具有 `Enum` 类型的属性支持 _lowerCamelCased_ 标记的代码洞察（2020.1 及更高版本）。注意：这些属性不能覆盖 `toString()`。
