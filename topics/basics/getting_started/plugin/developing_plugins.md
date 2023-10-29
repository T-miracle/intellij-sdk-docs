<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 开发插件

<link-summary>使用 Gradle 和 Gradle IntelliJ 插件开发 IntelliJ 平台插件。</link-summary>

IntelliJ 平台插件可以使用 [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/) 或 [IntelliJ IDEA Ultimate](https://www.jetbrains.com/idea/download/) 作为您的集成开发环境（IDE）来开发。
强烈建议始终使用最新可用版本，因为从 _Plugin DevKit_ 提供的插件开发工具支持新功能的持续更新。

在开始实际开发之前，请确保了解实现最佳效果的所有要求[](plugin_user_experience.md)。

> 在某些情况下，可能不需要实现实际的 IntelliJ 平台插件，因为存在[替代解决方案](plugin_alternatives.md)。
>
{title="Plugin Alternatives"}

## Gradle IntelliJ 插件

构建 IntelliJ 平台插件的推荐解决方案可在 [](tools_gradle_intellij_plugin.md) 中找到。

IntelliJ IDEA Ultimate 和 Community 版本提供了必要的插件来支持基于 Gradle 的插件开发：_Gradle_ 和 _Plugin DevKit_。
要验证这些插件是否已安装并启用，请参见关于 [管理插件](https://www.jetbrains.com/help/idea/managing-plugins.html) 的帮助部分。

<include from="snippets.md" element-id="pluginDevKitAvailability"/>

Gradle IntelliJ 插件管理插件项目的依赖关系 - 包括基本 IDE 和其他[插件依赖项](plugin_dependencies.md)。
它提供了运行带有您的插件的 IDE 以及打包和[发布](publishing_plugin.md#使用Gradle发布插件)插件到 [JetBrains Marketplace](https://plugins.jetbrains.com) 的任务。
为了确保插件不受[API 更改](api_changes_list.md)的影响，这些更改可能在平台的主要版本发布之间发生，您可以快速验证您的插件与其他 IDE 和版本的兼容性。

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
