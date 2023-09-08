# 关于本指南

<!-- Copyright 2000-2023 JetBrains s.r.o. and other contributors. Use of this source code is governed by the Apache 2.0 license that can be found in the LICENSE file. -->

<link-summary>内容的介绍和概要概述。</link-summary>

本指南分为几个部分，类似于教科书。
每一部分都建立在上一节内容的基础上，但没有必要按顺序阅读指南。
[关键专题](key_topics.md) 页面旨在链接到能够理解架构并开始构建插件所需的页面。

所有源链接和参考列表都针对 IntelliJ 平台 %ijPlatform%。

> 浏览本指南时，您会注意到有些专题呈灰色。
> 不幸的是，该指南并不完整，并且包含特定专题的占位符。
> 我们正在努力扩大覆盖范围，但如果您因缺少内容而陷入困境，请参阅 [](getting_help.topic) 部分，了解如何重新开始的详细信息。
>
> 该指南也是 [GitHub 上的开源](https://github.com/JetBrains/intellij-sdk-docs)，并且我们始终会感激地收到新内容、更正或更新的 Pull 请求。
> 有关详细信息，请参阅 [贡献](intellij-sdk-docs-original_CONTRIBUTING.md) 页面。
>
{style="note"}

> 另请参阅 [](glossary.md) 以获取常用术语的便捷参考。
>

#### 第一部分 —— 插件

描述如何创建可以扩展 IntelliJ 平台的插件。
包括有关如何设置项目、注册扩展点、针对 IntelliJ 平台的特定版本以及如何打包、部署和测试插件的详细信息。

#### 第二部分 —— 基础平台

描述架构的基础层，它提供许多功能和实用程序，例如组件模型、用户界面、文档和编辑器、虚拟文件系统、设置、线程和后台任务。
基础平台层主要包含 IntelliJ 平台的功能，不针对语言功能或解析。

#### 第三部分 —— 项目模型

记录项目模型，它表示当前加载项目的文件和配置，以及用于构建项目的构建系统。

#### 第四部分 —— PSI

程序结构接口 (PSI) 为许多不同的文件类型构建语法和语义模型。
本节介绍如何使用 PSI，导航和操作语法树，并介绍强大的引用系统，该系统允许语法树节点引用语义模型中的项目。
它还详细介绍了 PSI 如何创建和使用索引。

#### 第五部分 —— 特点

描述如何扩展使用 PSI 层的各种功能并与之交互，例如代码完成、导航、<shortcut>Alt+Enter</shortcut> 项、意图、重构等。
另请参阅下面有关自定义语言的部分，了解仅在添加对新语言的支持时才适用的特定于语言的功能。

#### 第六部分 —— 测试

描述用于编写涵盖插件功能的自动化测试的可用基础设施。

#### 第七部分 —— 自定义语言

插件通常扩展对现有语言的支持，例如添加对 Java 文件的检查。
本节介绍如何向 IntelliJ 平台添加对默认情况下不支持的新语言的支持、创建解析器、语法和语义模型以及构建于其之上的所有功能。

#### 第八部分 —— 产品特定

IntelliJ 平台中的许多功能与语言和产品无关。
例如，代码检查在 Java 中的工作方式与在 Ruby 中的工作方式相同；只是语法树和语义信息不同。
本节介绍特定于产品的功能，例如特定项目模型的差异以及如何在插件中定位它们。

#### 第九部分 —— 自定义 IDE

记录如何使用 IntelliJ 平台创建新的自定义 IDE，而不是现有产品（例如 WebStorm 或 Android Studio）的插件。

#### 第十部分 —— 主题

描述如何为基于 IntelliJ 平台的 IDE 创建主题。
包含有关如何设置主题项目、自定义、构建以及在 JetBrains Marketplace 上发布主题项目的详细信息。

#### 附录一 —— 资源

[实用资源](useful_links.md)、[](glossary.md)、[](extension_point_list.md)、有关如何使用 [](explore_api.md) 和 [](learning_resources.md) 的提示的链接。

#### 附录二 —— API 和兼容性

有关 IntelliJ 平台每个主要版本中的 [](verifying_plugin_compatibility.md) 和 [向后不兼容](api_changes_list.md) API 更改列表以及[显着更改和新功能](api_notable.md) 的信息。

#### 附录三 —— 工具

常用工具的参考和使用指南，例如 [](tools_gradle_intellij_plugin.md)。
