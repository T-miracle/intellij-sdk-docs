# 为 IntelliJ 平台做出贡献

<!-- Copyright 2000-2023 JetBrains s.r.o. and other contributors. Use of this source code is governed by the Apache 2.0 license that can be found in the LICENSE file. -->

<link-summary>如何参与 IntelliJ 平台开发并做出贡献。</link-summary>

请务必阅读[行为准则](intellij-sdk-docs-original_CODE_OF_CONDUCT.md)。

## 参与社区

### 加入新闻组和论坛

您可以加入多个社区[论坛和新闻组](https://intellij-support.jetbrains.com/hc/en-us/community/topics) 来讨论 IntelliJ 平台和 IDE。
对于有兴趣进行技术讨论、回答问题或解决新人潜在问题的用户和贡献者来说，论坛是一个极好的地方。

### 链接至 IntelliJ 平台主页

任何开源项目的成功取决于使用该产品并为该项目做出贡献的人数。
通过链接至 [https://www.jetbrains.com/opensource/idea](https://www.jetbrains.com/opensource/idea)，
新用户或贡献者能有机会了解该项目并加入社区。

如果您和我们一样对 IntelliJ IDEA 社区版十分感兴趣，
您可以通过链接到我们项目logo和[其他资源](https://www.jetbrains.com/company/brand/logos/)来展示它。

### 推广 IntelliJ 平台 和 IntelliJ IDEA 社区版

通过您的博客、Twitter、Facebook 或向您最喜爱的本地杂志提交文章来帮助推广平台和 IDE。
如果您是其他开源社区的成员，为什么不在他们的讨论论坛或会议上提及 IntelliJ IDEA 呢？
如果您喜欢 IntelliJ IDEA，请不要犹豫 — 大声说出来！
使用 IntelliJ IDEA 的开发人员越多，捕获的 bug 就越多，编写的插件就越多，项目就越引人注目，社区获得的好处就越多！

## 帮助他人学习

### 编写文档

我们一直在寻找有关 IntelliJ IDEA 功能的新文章以及 IntelliJ 平台的文档。
您可以编写教程、操作方法、示例应用程序，或分享您使用 IntelliJ 平台的经验。
您可以在网站或博客上发布您的文档，或向 SDK 文档提交 [拉取请求](intellij-sdk-docs-original_CONTRIBUTING.md)。

### 制作截屏视频

截屏视频最近变得非常流行，作为向其他开发人员展示如何有效使用该工具的一种方式。
您可以录制有关您发现的特定功能或用例的截屏视频，并希望与社区分享。

## 贡献代码

### 提交错误报告

错误报告只需很少的时间即可归档，并且对开发人员非常有帮助。
这是您可以做出的最简单的贡献之一。
当您发现 IDE 或平台存在问题时，请报告给我们。
确保提供有关您运行环境的信息（可从<ui-path>关于</ui-path>菜单获取）、重现问题的步骤以及问题的书面描述。
您可以在我们的 [YouTrack 问题跟踪器](https://youtrack.jetbrains.com/issues/IDEA) 中提交错误。
提交问题之前，请先通过关键字搜索相同问题的已提交的帖子 — 如果您找到了，请投上一票。

### 帮助分类现有的错误报告

多年来，用户已向 IntelliJ IDEA 问题跟踪器提交了数千个问题。
许多未解决的问题不再适用于最新版本的 IntelliJ IDEA、重复问题或需要其他信息才能解决。
留下评论来通知此类问题的状态有助于团队保持问题跟踪器的干净，这对每个人都有用。

### 编写一个插件

为 IntelliJ IDEA 或任何其他基于 IntelliJ 平台的 IDE 贡献大量代码、添加额外功能的最佳方法之一是编写插件。
您可以向 [JetBrains Marketplace](https://plugins.jetbrains.com/) 提交插件，这样所有用户都能使用。
在编写插件时，您可以自由控制您自己的代码，而不需要签署贡献协议。

### 提交补丁（submit-a-patch）

如果您想改进 IntelliJ 平台 中的代码或 IntelliJ IDEA 的核心功能，您可以向 [GitHub 上的 IntelliJ IDEA 社区版 存储库](https://github.com/JetBrains/intellij-community)。
准备更改时，请确保遵循 [](intellij_coding_guidelines.md)。
开发维护者将审查您的贡献，如果它符合质量标准并且与其余代码完美契合，您将收到有关补丁已被接受的通知。

正在寻找需要解决的问题？标有 [#patch_welcome](https://youtrack.jetbrains.com/issues/IDEA?q=%23patch_welcome%20%23unresolved) 标签的问题正在寻找外部贡献者。

或者，您可以将补丁附加到 [YouTrack bug 数据库](https://youtrack.jetbrains.com/issues/IDEA) 中的通知单中。
您可以提交带有附加补丁的新问题，也可以将补丁附加到其他用户提交的问题中。
在这种情况下，您还需要签署 [JetBrains 贡献者许可协议 (CLA)](https://www.jetbrains.com/agreements/cla/) 才能完成您的贡献。

### 成为提交者

具有长期提交高质量补丁历史的开发者可以获得直接提交权。
