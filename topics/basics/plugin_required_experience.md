<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 所需经验

<link-summary>所需的技术知识。</link-summary>

IntelliJ 平台是一个 JVM 应用，主要使用 [Java](https://www.oracle.com/java/) 和 [Kotlin](https://kotlinlang.org) 实现的。  
目前，无法使用非 JVM 语言为 IntelliJ 平台开发插件。

<include from="intellij_platform.md" element-id="pluginAlternatives"/>

为 IntelliJ 平台开发插件需要具备以下技术和概念的知识和经验：

- Java、Kotlin 或任何其他 JVM 语言，以及其标准库和第三方库
- [Gradle](https://gradle.org/) 或类似的构建系统（例如，Maven）
- 用于构建用户界面的 [Swing](https://en.wikipedia.org/wiki/Swing_(Java))
- [Java 并发模型](https://docs.oracle.com/javase/tutorial/essential/concurrency/index.html)
- 具有基于 IntelliJ 平台的 IDE 使用经验（例如，[IntelliJ IDEA](https://www.jetbrains.com/idea/)）

请记住，IntelliJ 平台是一个大型项目，尽管我们尽力覆盖尽可能多的主题，但文档中不可能包含每一个功能和使用场景。  
开发插件有时需要深入研究 [IntelliJ 平台代码](https://github.com/JetBrains/intellij-community) 并分析 [其他插件中的示例实现](https://jb.gg/ipe)。

> 强烈建议在开始插件实现之前，先熟悉 [](explore_api.md) 部分。
