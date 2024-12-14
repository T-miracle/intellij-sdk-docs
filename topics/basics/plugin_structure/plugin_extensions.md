<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 扩展

<link-summary>扩展是 IDE 中自定义功能的最常见方式。</link-summary>

_扩展_ 是插件以不像将操作添加到菜单或工具栏那样直接的方式，来扩展 IntelliJ 平台功能的最常见方式。

The following are some of the most common tasks achieved using extensions:

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

- Extension implementation must be stateless. Use explicit [services](plugin_services.md) for managing (runtime) data.
- Avoid any initialization in the constructor, see also notes for [services](plugin_services.md#ctor).
- Do not perform any static initialization. Use inspection <control>Plugin DevKit | Code | Static initialization in extension point implementations</control> (2023.3).
- An extension implementation must not be registered as a [service](plugin_services.md) additionally. Use inspection <control>Plugin DevKit | Code | Extension registered as service/component</control> (2023.3).

在使用 [Kotlin](using_kotlin.md) 时：

- 不要使用 `object`，而应使用 `class` 进行实现。[更多详细信息](using_kotlin.md#object-vs-class)
- 不要使用 `companion object`，以避免在加载扩展类时进行过多的类加载/初始化。
  而应改用顶层声明或对象。[更多详细信息](using_kotlin.md#companion-object-extensions)

</procedure>

### 扩展默认属性

`id`
: Unique ID. Consider prepending ID with the prefix related to the plugin name or ID to not clash with other plugins defining extensions with the same ID, for example, `com.example.myplugin.myExtension`.

`order` : 允许使用 `first`、`last` 或 `before|after [id]` 来排序所有定义的扩展。

`os`
: Allows restricting an extension to a given OS, for example, `os="windows"` registers the extension on Windows only

如果扩展实例需要在某些场景下“不适用”，则可以在其构造函数中抛出 [`ExtensionNotApplicableException`](%gh-ic%/platform/extensions/src/com/intellij/openapi/extensions/ExtensionNotApplicableException.java)。

### 扩展属性代码洞察 {id=扩展属性代码洞察}

有几个工具功能可帮助在 <path>plugin.xml</path> 中配置 bean 类扩展点。

#### Required Properties
<primary-label ref="2019.3"/>

Properties annotated with [`RequiredElement`](%gh-ic%/platform/core-api/src/com/intellij/openapi/extensions/RequiredElement.java) are inserted automatically and validated.

If the given property is allowed to have an explicit empty value, set `allowEmpty` to `true` (2020.3+).

#### Class names

Property names matching the following list will resolve to a fully qualified class name:

- `implementation`
- `className`
- ending with `Class` (case-sensitive)
- `serviceInterface`/`serviceImplementation`

A required parent type can be specified in the [extension point declaration](plugin_extension_points.md) via [`<with>`](plugin_configuration_file.md#idea-plugin__extensionPoints__extensionPoint__with):

```xml
<extensionPoint name="myExtension" beanClass="MyExtensionBean">
  <with
      attribute="psiElementClass"
      implements="com.intellij.psi.PsiElement"/>
</extensionPoint>
```

#### Custom resolve

Property name `language` (or ending in `*Language`, 2020.2+) resolves to all present [`Language`](%gh-ic%/platform/core-api/src/com/intellij/lang/Language.java) IDs.

Similarly, `action` and `actionId` (2024.3+) resolve to all registered [`<action>`](plugin_configuration_file.md#idea-plugin__actions__action) IDs.

#### Deprecation/ApiStatus

Properties marked as `@Deprecated` or annotated with any of [`ApiStatus`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) `@Internal`, `@Experimental`, `@ScheduledForRemoval`, or `@Obsolete` will be highlighted accordingly.

#### Enum properties
<primary-label ref="2020.1"/>

Attributes with `Enum` type support code insight with _lowerCamelCased_ notation. Note: Enum implementation must not override `toString()`.

#### I18n
<primary-label ref="2019.2"/>

Annotating with [`@Nls`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/Nls.java) validates a UI `String` capitalization according to the text property `Capitalization` enum value.
