<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 入门

<link-summary>通过开发自定义主题来自定义 IDE UI。</link-summary>

从 2019.1 版本开始，支持自定义主题。
自定义主题使设计人员能够控制内置 UI 元素的外观。
定制选项包括：

- 替代图标,
- 更改图标和 UI 控件的颜色,
- 更改 UI 控件的边框和插入,
- 提供自定义编辑器方案,
- 添加背景图片.

[可供下载的主题](https://plugins.jetbrains.com/search?headline=164-theme&tags=Theme)说明了创意的可能性。

> 请参阅[基于 IntelliJ 的 IDE 中的主题](https://blog.jetbrains.com/platform/2021/10/themes-in-intellij-based-ides/) 博客文章了解概述（提供多种语言版本）。
>

## 主题插件开发

可以使用 [IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/) 或 [IntelliJ IDEA Ultimate](https://www.jetbrains.com/idea/download/) 开发主题）作为您的 IDE（强烈建议使用最新的可用版本）。
两者都包含开发主题插件所需的全套开发工具。
要更熟悉 IntelliJ IDEA，请参阅 [IntelliJ IDEA Web 帮助](https://www.jetbrains.com/idea/help/)。

主题是[插件类型](plugin_types.md#themes) 之一。
它的结构与扩展 IDE 行为的插件没有显著的差异，并且可以使用 _DevKit_ 或 _Gradle_ 这两种支持的方法之一来实现。
开发方法的选择取决于项目需求和开发人员的经验。

### 基于 DevKit 的主题项目

使用 DevKit 开发主题插件是最简单的解决方案，不需要 Gradle 或类似构建工具的经验。
使用新建项目向导创建 IDE 插件主题项目时，默认会生成 DevKit 项目结构。

有关开发说明，请参阅 [](developing_themes.md) 部分。

### 基于 Gradle 的主题项目

使用 Gradle 开发主题插件需要使用 Gradle 或类似构建工具的经验。
它提供了自动化开发过程的某些部分的可能性，例如使用主题插件版本和其他数据修补 <path>[plugin.xml](plugin_configuration_file.md)</path> 文件，以及在CI 服务器并将其发布到 [JetBrains 插件市场](https://plugins.jetbrains.com)。

如果您的项目需要任何上述功能，请参阅[使用 Gradle 开发插件](developing_plugins.md) 了解更多详细信息。

> _忙碌的插件开发人员 - 第3集_ 的录制展示了如何使用 Gradle 方法[创建新主题](https://youtu.be/9J0j-90dC60?t=582)。
>
{style="note"}
