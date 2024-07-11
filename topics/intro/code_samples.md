<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# Code Samples

<link-summary>Accessing SDK Docs code samples.</link-summary>

本指南附带了许多示例插件，可从专用的 [intellij-sdk-code-samples](https://github.com/JetBrains/intellij-sdk-code-samples) GitHub 存储库中获取。

请参阅 <path>README.md</path>，其中列出了所有可用的代码示例以及简短说明。

每个示例都存储在专用文件夹中，并附有其自己的 <path>README.md</path>。
本教程中提供了相应教程或参考页面的链接，以及相关展示元素的列表。

## 使用 Gradle

所有示例插件均基于 Gradle，请参阅 [](creating_plugin_project.md) 以开始使用。

此外，[在 IntelliJ IDEA 中使用 Gradle](https://youtu.be/6V6G3RyxEMk) 录屏视频全面介绍了 IntelliJ IDEA 中的 Gradle 功能。

## 设置代码示例

确保插件 _Git_、_Gradle_ 和 _Plugin DevKit_ 已启用。

<include from="snippets.md" element-id="pluginDevKitAvailability"/>

Clone the [intellij-sdk-code-samples](https://github.com/JetBrains/intellij-sdk-code-samples) GitHub repository via <control>Git | Clone...</control>.
After successful cloning, the IDE suggests opening the project.

通过 [Gradle 工具窗口](https://www.jetbrains.com/help/idea/gradle.html#link_gradle_project) 选择要导入的代码示例。

或者，通过选择 <path>_gradleCompositeBuild</path> 导入可用的所有代码示例，这会链接复合构建中的所有 Gradle 项目。

导入成功后，项目将作为新节点出现在 <control>Gradle</control> 工具窗口树中。

在 <ui-path>Settings（设置） | Build, Execution, Deployment（构建、执行、部署） | Build Tools（构建工具） | Gradle</ui-path> 中为 <control>Gradle JVM</control> 分配 Java 17 SDK。

如有必要，从 Gradle 工具窗口工具栏调用 <control>Reload All Gradle Projects（重新加载所有 Gradle 项目）</control>。

## 运行代码示例

使用 <control>Gradle</control> 工具窗口中相应项目的 <control>Tasks（任务）</control> 节点下显示的 Gradle [`runIde`](creating_plugin_project.md#执行插件) 任务运行插件。
