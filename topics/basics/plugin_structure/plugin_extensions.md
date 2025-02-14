<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 扩展

<link-summary>扩展是 IDE 中自定义功能的最常见方式。</link-summary>

_扩展_ 是插件以不像将操作添加到菜单或工具栏那样直接的方式，来扩展 IntelliJ 平台功能的最常见方式。

以下是一些使用扩展实现的最常见任务：

* `com.intellij.toolWindow` 扩展点允许插件添加 [工具窗口](tool_windows.md)（显示在IDE用户界面两侧的面板）；
* `com.intellij.applicationConfigurable` 和 `com.intellij.projectConfigurable` 扩展点允许插件向 [设置对话框](settings.md) 添加页面；
* [自定义语言插件](custom_language_support.md) 使用多个扩展点来扩展 IDE 中各种语言支持功能。

在平台和捆绑插件中提供了1500多个扩展点，允许自定义 IDE 行为的不同部分。

## 探索可用的扩展点

[](intellij_platform_extension_point_list.md) 和 [](intellij_community_plugins_extension_point_list.md) 列出了 IntelliJ 平台及 IntelliJ IDEA 中捆绑插件的所有可用扩展点。
此外，特定于 IDE 的专用扩展点和监听器列表可在 _Product Specific_ 下找到。
通过 [IntelliJ Platform Explorer](https://jb.gg/ipe) 浏览开源 IntelliJ 平台插件的现有实现中的用法。

或者（或者在使用第三方扩展点时），可以在 <path>[plugin.xml](plugin_configuration_file.md)</path> 中的 [`<extensions>`](plugin_configuration_file.md#idea-plugin__extensions) 块中使用自动完成列出指定命名空间 (`defaultExtensionNs`) 的所有可用扩展点。
在查找列表中使用 <ui-path>View | Quick Documentation</ui-path> 可以访问有关扩展点和实现的更多信息（如果适用）。
有关更多信息和策略，请参见 [](explore_api.md)。

## 声明扩展 {id=declaring-extensions}

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

- 扩展实现必须是无状态的。使用显式的[服务](plugin_services.md)来管理（运行时）数据。
- 避免在构造函数中进行任何初始化，另请参阅[服务](plugin_services.md#ctor)的说明。
- 不要执行任何静态初始化。使用检查 <control>Plugin DevKit | Code | Static initialization in extension point implementations(扩展点实现中的静态初始化)</control>（2023.3）。
- 扩展实现不得额外注册为[服务](plugin_services.md)。使用检查 <control>Plugin DevKit | Code | Extension registered as service/component(扩展注册为服务/组件)</control>（2023.3）。

在使用 [Kotlin](using_kotlin.md) 时：

- 不要使用 `object`，而应使用 `class` 进行实现。[更多详细信息](using_kotlin.md#object-vs-class)
- 不要使用 `companion object`，以避免在加载扩展类时进行过多的类加载/初始化。
  而应改用顶层声明或对象。[更多详细信息](using_kotlin.md#companion-object-extensions)

</procedure>

### 扩展默认属性

`id`  
: 唯一标识符。考虑在 ID 前添加与插件名称或 ID 相关的前缀，以避免与其他插件定义具有相同 ID 的扩展发生冲突，例如 `com.example.myplugin.myExtension`。

`order` :
允许使用 `first`、`last` 或 `before|after [id]` 来排序所有定义的扩展。

`os`
: 允许将扩展限制为特定操作系统，例如，`os="windows"` 仅在 Windows 上注册该扩展。

如果扩展实例需要在某些场景下“不适用”，则可以在其构造函数中抛出 [`ExtensionNotApplicableException`](%gh-ic%/platform/extensions/src/com/intellij/openapi/extensions/ExtensionNotApplicableException.java)。

### 扩展属性代码洞察 {id=扩展属性代码洞察}

有几个工具功能可帮助在 <path>plugin.xml</path> 中配置 bean 类扩展点。

#### 必需属性
<primary-label ref="2019.3"/>

使用 [`RequiredElement`](%gh-ic%/platform/core-api/src/com/intellij/openapi/extensions/RequiredElement.java) 注解的属性会自动插入并进行验证。

如果允许该属性具有显式的空值，可以将 `allowEmpty` 设置为 `true`（2020.3+）。

#### 类名 {id=class-names}

匹配以下列表的属性名将解析为一个完全限定的类名：

- `implementation`
- `className`
- 以 `Class` 结尾（区分大小写）
- `serviceInterface` / `serviceImplementation`

可以通过在 [扩展点声明](plugin_extension_points.md) 中使用 [`<with>`](plugin_configuration_file.md#idea-plugin__extensionPoints__extensionPoint__with) 来指定所需的父类型。

```xml
<extensionPoint name="myExtension" beanClass="MyExtensionBean">
  <with
      attribute="psiElementClass"
      implements="com.intellij.psi.PsiElement"/>
</extensionPoint>
```

#### 自定义解析 {id=custom-resolve}

属性名 `language`（或以 `*Language` 结尾，2020.2+）解析为所有当前存在的 [`Language`](%gh-ic%/platform/core-api/src/com/intellij/lang/Language.java) ID。

类似地，`action` 和 `actionId`（2024.3+）解析为所有注册的 [`<action>`](plugin_configuration_file.md#idea-plugin__actions__action) ID。

#### 废弃/ApiStatus {id=deprecation-apistatus}

标记为 `@Deprecated` 或使用任何以下 [`ApiStatus`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) 注解的属性，如 `@Internal`、`@Experimental`、`@ScheduledForRemoval` 或 `@Obsolete`，将会被相应地高亮显示。

#### 枚举属性 {id=enum-properties}

具有 `Enum` 类型的属性支持使用 _lowerCamelCased_ 命名法的代码提示。注意：枚举实现不得重写 `toString()` 方法。

#### 国际化 (I18n) {id=i18n}

使用 [`@Nls`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/Nls.java) 注解可以根据文本属性 `Capitalization` 枚举值验证 UI `String` 的大小写。
