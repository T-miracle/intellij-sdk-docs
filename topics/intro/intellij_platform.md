# IntelliJ 平台

<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>介绍 IntelliJ 平台、插件以及基于该平台的 IDE。</link-summary>

IntelliJ 平台本身并不是一个产品，而是提供了一个用于构建 IDE 的平台。
它用于为 JetBrains 产品提供支持，例如 [IntelliJ IDEA](https://www.jetbrains.com/idea/)。
它也是开源的，可以被第三方用来构建 IDE，例如 Google 的 [Android Studio](https://developer.android.com/studio/index.html)。

IntelliJ 平台提供了这些 IDE 提供丰富语言工具支持所需的所有基础设施。
它是一个组件驱动的、基于跨平台 JVM 的应用程序主机，具有高级用户界面工具包，
用于创建[工具窗口](tool_windows.md)、树视图和列表（支持快速搜索）以及弹出菜单和[对话框](dialog_wrapper.md)。

IntelliJ 平台有一个全文[编辑器](editors.md)，其中包含[语法突出显示](analyzing.md)、[代码折叠](folding_builder.md)、[代码补全](code_completion.md)、和其他富文本[编辑功能](editing.md)。
还包括图像编辑器。

此外，它还包括用于构建标准 IDE 功能的开放 API，例如 [项目模型](project.md) 和 [构建系统](external_system_integration.md)。
它还提供了丰富的调试体验的基础设施，以及与语言无关的高级断点支持、调用堆栈、监视窗口和表达式评估。

但 IntelliJ 平台的真正厉害的地方来自程序结构接口 ([PSI](psi.md))。
它是一组用于解析文件、构建丰富的代码语法和语义模型以及根据这些数据构建[索引](indexing_and_psi_stubs.md)的功能。
PSI 提供许多功能，从快速导航到[文件](psi_files.md)、类型和[符号](symbols.md)，
到[代码完成](code_completion.md) 弹出窗口和[查找用法] 的内容](find_usages.md)、[代码检查](code_inspections.md) 和代码重写，
用于快速修复或[重构](rename_refactoring.md) 以及许多其他功能。

IntelliJ 平台包括许多语言的[解析器](implementing_parser_and_psi.md) 和 PSI 模型，其可扩展性意味着可以[添加对其他语言的支持](custom_language_support.md)。

## 插件

在 IntelliJ 平台上构建的产品是可扩展的应用程序，该平台负责创建 [](plugin_extensions.md)。
IntelliJ 平台完全支持 [plugins](developing_plugins.md)，JetBrains 托管 [JetBrains Marketplace](https://plugins.jetbrains.com)，可用于分发支持一种或多种产品的插件。
还可以使用 [](custom_plugin_repository.md) 分发插件。

插件可以通过多种方式扩展平台，从添加简单的菜单项到添加对完整语言、构建系统和调试器的支持。
许多现有的 IntelliJ 平台功能都是作为插件实现的，可以根据最终产品的需求包含或排除这些插件。
请参阅 [](plugins_quick_start.md) 了解更多详细信息。

> 在某些情况下，可能不需要实现实际的 IntelliJ 平台插件，因为存在[替代解决方案](plugin_alternatives.md)。
>
{style="note"}

## 开源 { id="开源" }

IntelliJ 平台是开源的，遵循 [Apache 许可证](%gh-ic%/LICENSE.txt)，并且[托管在 GitHub 上](https://github.com/JetBrains/intellij-community)。

虽然本指南将 IntelliJ 平台称为单独的实体，但没有“IntelliJ 平台” 的 GitHub 存储库。
相反，该平台被认为与[IntelliJ IDEA Community Edition](idea.md)几乎完全重叠，后者是IntelliJ IDEA Ultimate的免费开源版本(上面链接的GitHub存储库是[JetBrains/ IntelliJ - Community](https://github.com/JetBrains/intellij-community)存储库)。
请注意：从 2021.1 版本开始，IntelliJ IDEA 社区版捆绑的一些插件不是开源的。

IntelliJ 平台的版本由相应的 IntelliJ IDEA 社区版 发行版的版本定义。
例如，要针对 IntelliJ IDEA (2019.1.1) 构建插件，那么构建 #191.6707.61 意味着需要从指定相同的构建号标签的 “intellij-community” 存储库获取正确的 Intellij 平台文件。
有关与版本编号对应的内部版本号的更多信息，请参阅 [](build_number_ranges.md) 页面。

通常，基于 IntelliJ 平台的 IDE 将包含 “intellij-community” 存储库作为 Git 子模块，并提供配置来描述 “intellij-community” 中的哪些插件以及哪些自定义插件将构成产品。

## 基于 IntelliJ 平台的 IDE { id="基于IntelliJ平台的IDE" }

IntelliJ 平台是许多 JetBrains IDE 的基础。
[IntelliJ IDEA Ultimate](idea_ultimate.md) 是 IntelliJ IDEA Community Edition 的超集，
但包含闭源插件（[请参阅此功能比较](https://www.jetbrains.com/idea/features/editions_comparison_matrix.html)）。
同样，其他产品，例如 [WebStorm](webstorm.md) 和 [DataGrip](data_grip.md) 基于 IntelliJ IDEA 社区版，它包含一组不同的插件，但不包括其他默认插件。
这允许插件针对多个产品，因为每个产品都将包含基本功能和来自 IntelliJ IDEA Community Edition 存储库的精选插件。

<include from="snippets.md" element-id="jetbrainsProductOpenSourceLicense"/>

以下 IDE 基于 IntelliJ 平台：

* [JetBrains](https://www.jetbrains.com) IDEs:
    * [AppCode](app_code.md)
    * [CLion](clion.md)
    * [DataGrip](data_grip.md)
    * [GoLand](goland.md)
    * [IntelliJ IDEA](idea.md)
    * [MPS](https://www.jetbrains.com/mps/)
    * [PhpStorm](phpstorm.md)
    * [PyCharm](pycharm.md)
    * [Rider](#rider)
    * [RubyMine](rubymine.md)
    * [WebStorm](webstorm.md)
* [Android Studio](android_studio.md) IDE 出自 Google
* [Comma](https://commaide.com/) IDE 为 Raku (以前称为 Perl 6) 构建
* [Jmix Studio](https://www.jmix.io/tools/)

### Rider

JetBrains [Rider](rider.md) 使用 IntelliJ 平台的方式与其他基于 IntelliJ 的 IDE 不同。
它使用 IntelliJ 平台为 C# 和 .NET IDE 提供用户界面，以及标准的 IntelliJ 编辑器、工具窗口、调试体验等。
它还集成到标准的 “查找用法（Find）” 和 “搜索各处（everywhere）” UI 中，并使用代码完成、语法突出显示等。

但是，Rider 不会为 C# 文件创建完整的 [PSI](psi.md)（语法和语义）模型。
相反，它重用 [ReSharper](https://www.jetbrains.com/resharper/) 来提供语言功能。
所有 C# PSI 模型、检查、代码重写（例如快速修复和重构）都在 ReSharper 的命令行版本中在流程之外运行。
这意味着为 Rider 创建插件涉及两个部分 - 一个位于 IntelliJ “前端” 以显示用户界面的插件，以及一个位于 ReSharper “后端” 以分析和使用 C# PSI 的插件。

幸运的是，许多插件可以简单地与 ReSharper 后端配合使用。
Rider 负责显示检查和代码完成的结果，并且不需要 IntelliJ UI 组件即可实现许多插件。
