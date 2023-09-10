<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# IntelliJ 平台

<link-summary>介绍 IntelliJ 平台、插件以及基于该平台的 IDE。</link-summary>

IntelliJ 平台本身并不是一个产品，而是提供了一个用于构建 IDE 的平台。
它用于为 JetBrains 产品提供支持，例如 [IntelliJ IDEA](https://www.jetbrains.com/idea/)。
它也是开源的，可以被第三方用来构建 IDE，例如 Google 的 [Android Studio](https://developer.android.com/studio/index.html)。

IntelliJ 平台提供了这些 IDE 提供丰富语言工具支持所需的所有基础设施。
它是一个组件驱动的、跨平台的基于 JVM 的应用程序主机，具有用于创建工具窗口、树视图和列表(支持快速搜索)以及弹出式菜单和对话框的高级用户界面工具包。

IntelliJ 平台有一个全文编辑器，具有[语法突出显示](analyzing.md)、代码折叠、代码完成和其他富文本[编辑功能](editing.md) 的抽象实现。
还包括图像编辑器。

此外，它还包括用于构建标准 IDE 功能的开放 API，例如项目模型和构建系统。
它还提供了丰富的调试体验的基础设施，以及与语言无关的高级断点支持、调用堆栈、监视窗口和表达式评估。

但 IntelliJ 平台的真正厉害的地方是因为程序结构接口 (PSI)。
它是一组用于解析文件、构建丰富代码语法和语义模型以及根据该数据构建索引的功能。
PSI 提供许多功能，从快速导航到文件、类型和符号，到代码完成窗口的内容并查找用法、代码检查和代码重写，以进行快速修复或重构，以及许多其他功能。

IntelliJ 平台包括适用于多种语言的解析器和 PSI 模型，其可扩展性意味着可以添加对其他语言的支持。

## 插件

基于 IntelliJ 平台构建的产品是可扩展的应用程序，该平台负责创建组件并将依赖项注入到类中。
IntelliJ 平台完全支持插件，JetBrains 托管 [JetBrains Marketplace](https://plugins.jetbrains.com)，可用于分发支持一种或多种产品的插件。
还可以使用 [](custom_plugin_repository.md) 分发插件。

插件可以通过多种方式扩展平台，从添加简单的菜单项到添加对完整语言、构建系统和调试器的支持。
许多现有的 IntelliJ 平台功能都是作为插件实现的，可以根据最终产品的需求包含或排除这些插件。
请参阅 [](plugins_quick_start.md) 了解更多详细信息。

> 在某些情况下，可能不需要实现实际的 IntelliJ 平台插件，因为存在[替代解决方案](plugin_alternatives.md)。
>

## 开源（open source）

IntelliJ 平台是开源的，遵循 [Apache 许可证](%gh-ic%/LICENSE.txt)，并且[托管在 GitHub 上](https://github.com/JetBrains/intellij-community)。

虽然本指南将 IntelliJ 平台作为一个独立的实体，但 GitHub 中并没有 “IntelliJ 平台” 存储库。
相反，该平台被认为与 IntelliJ IDEA 社区版几乎完全重叠，后者是 IntelliJ IDEA Ultimate 的免费开源版本(上面链接的GitHub存储库是[JetBrains/ IntelliJ - 社区](https://github.com/JetBrains/intellij-community)存储库)。
请注意：从 2021.1 版本开始，IntelliJ IDEA 社区版捆绑的一些插件不是开源的。

IntelliJ 平台的版本由相应的 IntelliJ IDEA 社区版 发行版的版本定义。
例如，要针对 IntelliJ IDEA (2019.1.1) 构建插件，那么构建 #191.6707.61 意味着必需要从指定相同的构建号标签的 “intellij-community” 存储库获取正确的 Intellij 平台文件。
有关与版本编号对应的内部版本号的更多信息，请参阅 [](build_number_ranges.md) 页面。

通常，基于 IntelliJ 平台的 IDE 将包含 “intellij-community” 存储库作为 Git 子模块，并提供配置来描述 “intellij-community” 中的哪些插件以及哪些自定义插件将构成产品。
这就是 IDEA Ultimate 团队的工作方式，他们为自定义插件和 IntelliJ 平台本身贡献代码。

## 基于 IntelliJ 平台的 IDE

IntelliJ 平台是许多 JetBrains IDE 的基础。
IntelliJ IDEA Ultimate 是 IntelliJ IDEA 社区版 的超集，但包含闭源插件（[请参阅此功能比较](https://www.jetbrains.com/idea/features/editions_comparison_matrix.html)）。
同样，其他产品（例如 WebStorm 和 DataGrip）基于 IntelliJ IDEA 社区版，它们分别包含一组不同的插件，但不包括其他默认插件。
这允许插件针对多个产品，因为每个产品都将包含基本功能和来自 IntelliJ IDEA 社区版 存储库的精选插件。

<include from="snippets.md" element-id="jetbrainsProductOpenSourceLicense"/>

以下 IDE 基于 IntelliJ 平台：
* JetBrains IDEs:
  * [AppCode](https://www.jetbrains.com/objc/)
  * [CLion](https://www.jetbrains.com/clion/)
  * [DataGrip](https://www.jetbrains.com/datagrip/)
  * [GoLand](https://www.jetbrains.com/go/)
  * [IntelliJ IDEA](https://www.jetbrains.com/idea/)
  * [MPS](https://www.jetbrains.com/mps/)
  * [PhpStorm](https://www.jetbrains.com/phpstorm/)
  * [PyCharm](https://www.jetbrains.com/pycharm/)
  * [Rider](#rider)
  * [RubyMine](https://www.jetbrains.com/ruby/)
  * [WebStorm](https://www.jetbrains.com/webstorm/)
* [Android Studio](https://developer.android.com/studio/index.html) IDE 出自 Google
* [Comma](https://commaide.com/) Raku IDE（以前称为 Perl 6）
* [Jmix Studio](https://www.jmix.io/tools/)

有关 IDE 特定详细信息，请参阅*第八部分 — 产品特定*。

### Rider

JetBrains [Rider](https://www.jetbrains.com/rider/) 使用 IntelliJ 平台的方式与其他基于 IntelliJ 的 IDE 不同。
它使用 IntelliJ 平台为 C# 和 .NET IDE 提供用户界面，以及标准的 IntelliJ 编辑器、工具窗口、调试体验等。
它还集成到标准的 “Find Usages（查找用法）” 和 “Search Everywhere（到处搜索）” UI中，并使用代码补全、语法高亮等功能。

但是，Rider 并不会为 C# 文件创建完整的 [PSI](psi.md)（语法和语义）模型。
相反，它重用 [ReSharper](https://www.jetbrains.com/resharper/) 来提供语言功能。
所有 C# PSI 的模型、检查、代码重写，比如快速修复和重构，都会在 ReSharper 的命令行版本中运行。
这意味着为 Rider 创建一个插件包括两个部分—  —  一个位于 IntelliJ “前端” 的插件，用于显示用户界面；另一个位于 ReSharper “后端” 的插件，用于分析和使用 C# PSI。

幸运的是，许多插件可以简单地与 ReSharper 后端配合使用。
Rider 负责显示检查和代码完成的结果，并且不需要 IntelliJ UI 组件即可实现许多插件。
更多细节可以在[](rider.md)中找到。
