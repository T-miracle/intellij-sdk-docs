<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 所需经验

<link-summary>所需的技术知识。</link-summary>

IntelliJ 平台是一个 JVM 应用程序，主要用 Java 和 [Kotlin](https://kotlinlang.org) 实现。
目前，无法使用非 JVM 语言为 IntelliJ 平台开发插件。

为 IntelliJ 平台开发插件需要具备以下技术和概念的知识和经验：

- Java、Kotlin 或其他 JVM 语言及其标准库和第三方库
- [Gradle](https://gradle.org/) 或类似构建系统的程序（例如 Maven）
- [Swing](https://en.wikipedia.org/wiki/Swing_(Java)) 用于构建用户界面
- [Java并发模型](https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html)
- 使用基于 IntelliJ 平台的 IDE 的经验（例如 [IntelliJ IDEA](https://www.jetbrains.com/idea/)）

请记住，IntelliJ 平台是一个大型项目，虽然我们尽力涵盖尽可能多的主题，但不可能在文档中包含所有功能和用例。
开发插件有时需要深入研究 [IntelliJ 平台代码](https://github.com/JetBrains/intellij-community) 并分析[其他插件中的示例实现](https://jb.gg/ipe) 。

强烈建议您在开始插件实现之前熟悉 [](explore_api.md) 部分。

<include from="intellij_platform.md" element-id="pluginAlternatives"/>
