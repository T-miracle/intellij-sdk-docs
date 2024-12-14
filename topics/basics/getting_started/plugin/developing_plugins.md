<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 开发插件

<link-summary>使用 Gradle 和 Gradle IntelliJ 插件开发 IntelliJ 平台插件。</link-summary>

IntelliJ 平台插件可以使用 [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/) 或 [IntelliJ IDEA Ultimate](https://www.jetbrains.com/idea/download/) 作为您的集成开发环境（IDE）来开发。
强烈建议始终使用最新可用版本，因为从 _Plugin DevKit_ 提供的插件开发工具支持新功能的持续更新。

在开始实际开发之前，请确保了解实现最佳效果的所有要求[](plugin_user_experience.md)。

<include from="intellij_platform.md" element-id="pluginAlternatives"/>

## Gradle Plugin

The recommended solution for building IntelliJ Platform plugins is using [Gradle](https://www.gradle.org) with
a dedicated Gradle plugin:
[](tools_intellij_platform_gradle_plugin.md) or
[](tools_gradle_intellij_plugin.md).

<include from="snippets.md" element-id="gradlePluginVersion"/>

The IntelliJ IDEA Ultimate 和 Community 版本提供必要的插件来支持 **Gradle** 和 **Plugin DevKit** 的插件开发。
要验证这些插件是否已安装并启用，请参阅关于 [管理插件](https://www.jetbrains.com/help/idea/managing-plugins.html) 的帮助部分。

<include from="snippets.md" element-id="pluginDevKitAvailability"/>

The Gradle plugin manages the dependencies of a plugin project – both the base IDE and other [plugin dependencies](plugin_dependencies.md).
It provides tasks to run the IDE with your plugin and to package and [publish](publishing_plugin.md#publishing-plugin-with-gradle) your plugin to the [JetBrains Marketplace](https://plugins.jetbrains.com).
To make sure that a plugin is not affected by [API changes](api_changes_list.md), which may happen between major releases of the platform, you can quickly verify your plugin against other IDEs and releases.

There are two main ways of creating a new Gradle-based IntelliJ Platform plugin project:
- dedicated generator available in the [New Project Wizard](https://www.jetbrains.com/help/idea/new-project-wizard.html) – it creates a minimal plugin project with all the required files
- [](plugin_github_template.md) available on GitHub – in addition to the required project files, it includes configuration of the GitHub Actions CI workflows

This documentation section describes the plugin structure generated with the <control>New Project</control> wizard, but the project generated with _IntelliJ Platform Plugin Template_ covers all the described files and directories.
See [](plugin_github_template.md) for more information about the advantages of this approach and instructions on how to use it.

### 备择方案 {id=alternatives}

旧的 **DevKit** 项目模型和工作流仍然支持现有项目，并建议用于 [creating theme plugins](developing_themes.md)。查看如何将 **DevKit** 插件 [migrate a DevKit plugin to Gradle](migrating_plugin_devkit_to_gradle.md)。

一个专门的 [SBT plugin](https://github.com/JetBrains/sbt-idea-plugin) 可用于用 **Scala** 实现的插件。

## 插件开发工作流程

* [](creating_plugin_project.md)
  * [](plugin_github_template.md)
* [](configuring_plugin_project.md)
* [](using_kotlin.md)
* [](plugin_signing.md)
* [](publishing_plugin.md)
