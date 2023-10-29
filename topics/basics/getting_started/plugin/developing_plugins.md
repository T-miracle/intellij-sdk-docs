<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 开发插件

<link-summary>使用 Gradle 和 Gradle IntelliJ 插件开发 IntelliJ 平台插件。</link-summary>

IntelliJ Platform plugins can be developed by using either [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/) or [IntelliJ IDEA Ultimate](https://www.jetbrains.com/idea/download/) as your IDE.
It is highly recommended to always use the latest available version, as the plugin development tooling support from _Plugin DevKit_ continues supporting new features.

在开始实际开发之前，请确保了解实现最佳效果的所有要求[](plugin_user_experience.md)。

> 在某些情况下，可能不需要实现实际的 IntelliJ 平台插件，因为存在[替代解决方案](plugin_alternatives.md)。
>
{title="Plugin Alternatives"}

## Gradle IntelliJ 插件

The recommended solution for building IntelliJ Platform plugins is [](tools_gradle_intellij_plugin.md).

The IntelliJ IDEA Ultimate and Community editions provide the necessary plugins to support Gradle-based plugin development: _Gradle_ and _Plugin DevKit_.
To verify these plugins are installed and enabled, see the help section about [Managing Plugins](https://www.jetbrains.com/help/idea/managing-plugins.html).

<include from="snippets.md" element-id="pluginDevKitAvailability"/>

Gradle IntelliJ Plugin manages the dependencies of a plugin project - both the base IDE and other [plugin dependencies](plugin_dependencies.md).
It provides tasks to run the IDE with your plugin and to package and [publish](publishing_plugin.md#publishing-plugin-with-gradle) your plugin to the [JetBrains Marketplace](https://plugins.jetbrains.com).
To make sure that a plugin is not affected by [API changes](api_changes_list.md), which may happen between major releases of the platform, you can quickly verify your plugin against other IDEs and releases.

创建新的基于 Gradle 的 IntelliJ 平台插件项目主要有两种方法：
- [新建项目向导](https://www.jetbrains.com/help/idea/new-project-wizard.html) 中提供了专用生成器 - 它创建一个包含所有必需文件的最小插件项目
- [](plugin_github_template.md) 可在 GitHub 上获取 - 除了所需的项目文件之外，它还包括 GitHub Actions CI 工作流程的配置

本文档部分描述了使用 <control>New Project（新建项目）</control> 向导生成的插件结构，但使用 _IntelliJ Platform Plugin Template_ 生成的项目涵盖了所有描述的文件和目录。
请参阅[](plugin_github_template.md)小节，了解有关该方法优点的更多信息以及如何使用它的说明。

> 现有项目仍然支持旧的 DevKit 项目模型和工作流程，并推荐用于[创建主题插件](developing_themes.md)。
> 了解如何 [将 DevKit 插件迁移到 Gradle](migrating_plugin_devkit_to_gradle.md)。
>

> 专用的 [SBT 插件](https://github.com/JetBrains/sbt-idea-plugin) 可用于在 Scala 中实现的插件。
>

## 插件开发工作流程

* [创建基于 Gradle 的插件项目](creating_plugin_project.md)
* [配置 Gradle IntelliJ 插件](configuring_plugin_project.md)
  * [添加 Kotlin 支持](using_kotlin.md) (optional)
* [发布插件](publishing_plugin.md)
