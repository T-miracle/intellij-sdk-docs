<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件用户体验 (UX)

<link-summary>确保您的插件具有良好的用户体验。</link-summary>

_用户体验 (UX)_ 是一个描述用户在使用产品时感受的术语。
它不仅涵盖了视觉呈现、性能、稳定性或易用性等方面，还包括任何影响用户满意度的因素。
它是用户对使用产品的整体印象，强烈表明用户是否喜欢该产品。

基于 IntelliJ 平台的插件也是产品，提供良好的用户体验对于留住用户并让他们向他人推荐你的插件非常重要。
糟糕的插件用户体验可能会对用户群产生负面影响，因为用户往往会放弃那些带来挫败感的插件，例如由于不稳定、性能差或复杂性。

以下部分解释了选定的插件用户体验领域。

## 一般建议 {id=general-advice}

优秀的 IntelliJ 平台插件，像任何其他产品一样，应该为用户带来显著的价值。
在规划工作时，尝试了解用户最需要什么，并优先考虑关键功能。

如果你对实现的解决方案不确定，可以考虑与有限的用户群体（例如你的同事或活跃的社区成员）分享正在进行的工作，并收集反馈以帮助你改进最终结果。
有关如何自动化分享预发布插件版本的信息，请参阅 [自定义发布渠道](publishing_plugin.md#specifying-a-release-channel) 部分。

### 反馈 {id=feedback}

收集现有功能的反馈可以帮助识别哪些方面做得好，哪些方面需要改进，从而提高插件的整体质量。

<video src="https://www.youtube.com/watch?v=J3VEfTUX6Bs"/>

_本次会议涵盖了处理功能请求、处理错误报告、应对不同的沟通方式以及促进用户参与的最佳实践。_

## 易用性 {id=ease-of-use}

插件应该易于使用。
理想情况下，所有功能在安装后应该开箱即用，无需任何特殊的用户交互，例如手动启用关键的插件功能。
默认设置应反映标准项目中的典型插件使用情况。

所有设置和操作应易于找到，并放置在适当的 [设置](settings.md) 或 [操作组](grouping_action.md) 中，例如：

* 框架插件设置应放置在 <ui-path>设置 | 语言和框架</ui-path> 下
* 将目录标记为插件特定根类型的操作应添加到 <ui-path>标记目录为...</ui-path> 组中

难以配置且功能难以找到的插件可能会因挫败感而被迅速放弃。

## 稳定性 {id=stability}

在消息面板中显示大量错误并执行导致不正确行为的操作（例如生成错误代码）的插件被认为是不稳定和不可靠的。

为了提高整体稳定性并最小化引入回归问题的风险，实施维护成本低的 [功能测试](testing_plugins.md) 至关重要。
这是一个很好的安全网，从长远来看，它将加快你的开发速度，并帮助你在不破坏现有插件功能的情况下发布新版本。

开发没有错误的软件几乎是不可能的，因此建议设置一个问题跟踪器，用户可以在其中报告错误。
此外，值得实施 [错误报告](ide_infrastructure.md#error-reporting)，允许直接从 IDE 内部发送报告，并附带所有必要的信息。
为了重现和理解生产中的问题，请一致使用 [日志记录](ide_infrastructure.md#logging)。

## 性能 {id=performance}

即使插件工作正常且外观令人满意，性能不佳也会影响用户满意度。
缓慢的高亮显示、代码补全、代码生成和其他功能可能会破坏用户的工作流程，并导致挫败感，从而导致插件被卸载。
始终尝试遵循相关主题页面上描述的性能提示，例如 [](psi_performance.md)、[](threading_model.md#avoiding-ui-freezes)、[](indexing_and_psi_stubs.md#improving-indexing-performance)。

尽可能使功能在 [哑模式](indexing_and_psi_stubs.md#dumb-mode) 下工作，也可以提高感知性能。

## 分发大小 {id=distribution-size}

除了插件执行性能外，还建议优化用户从 JetBrains Marketplace 下载的插件分发大小。
网络连接不佳的用户可能会在意识到需要等待太长时间才能下载一个他们不确定是否满足期望的插件时取消下载。

考虑以下优化插件分发大小的技术：

* 减少依赖项的数量。检查目标平台是否包含你需要的实用程序（如 [](ui_faq.md) 中提到的）或 [库](api_changes_list.md#bundled-library-updates)，并重用它们。
* 确保插件分发中没有打包不需要的或同一依赖项的多个版本。
* 优化图标、图像、视频等资源。
* 如果仅在特定设置中需要大型资源（例如 SDK、参考文档），请考虑由插件按需下载它们，而不是将它们捆绑在插件分发中。

[混淆](https://plugins.jetbrains.com/docs/marketplace/obfuscate-the-plugin.html) 也可能有助于减少分发文件的大小。

## 一致的行为 {id=consistent-behavior}

在设计和实现功能时，请查看 IDE 和插件的现有功能，并以类似和一致的方式设计你的功能。
一致的方法将使用户更容易使用你的插件，从而提高整体满意度。

如果你实现了 [自定义语言支持](custom_language_support.md)，请查看并考虑实现 [](additional_minor_features.md) 列表中的扩展点，以便你的插件提供与其他插件一致的体验。

## 一致且美观的 UI {id=consistent-and-good-looking-ui}

如果插件的 UI 与其他应用程序部分显著不同，可能会让人感到陌生，并可能被认为是低质量或被忽视的。
查看并遵循 [](ui_guidelines_welcome.topic) 中描述的规则。
注意 [图标](icons_style.md) 并使它们与 IDE 风格匹配。
使用推荐用于特定用例的 UI 控件。
使用 [UI 检查器](internal_ui_inspector.md) 查看现有 UI 的实现方式。

## 高质量的文本 {id=high-quality-texts}

质量差的标签或带有拼写和语法错误的文本会给人留下不好的印象。
所有 UI 文本应遵循 [](ui_guidelines_welcome.topic) 中 _文本_ 部分描述的规则。
建议校对文本或使用 [拼写检查插件](https://plugins.jetbrains.com/search?tags=Spellcheck) 之一。

## 插件描述和展示 {id=plugin-description-and-presentation}

插件页面上的描述是用户与插件的第一次接触，其内容帮助他们决定这是否是他们正在寻找的插件。
描述应清晰、精炼，并遵循 JetBrains Marketplace [文档](https://plugins.jetbrains.com/docs/marketplace/plugin-overview-page.html#plugin-description) 中描述的规则。
另请参阅关于 [优化 JetBrains Marketplace 插件页面的 5 个技巧](https://youtu.be/oB1GA9JeeiY?t=52) 的网络研讨会。

## 提供翻译 {id=providing-translations}

[翻译](providing_translations.md) 插件可以改善用户体验并增加非英语用户的使用率，例如在快速发展的中国市场。
即使近期没有翻译计划，也建议通过遵循 [](internationalization.md) 部分的建议，使插件准备好进行翻译。
