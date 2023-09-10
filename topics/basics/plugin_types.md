<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件类型

<link-summary>不同类型插件的概述和示例。</link-summary>

基于 IntelliJ 平台的产品可以通过添加插件进行修改和调整以实现自定义目的。
所有可下载的插件均可从 [JetBrains Marketplace](https://plugins.jetbrains.com/) 获取。

最常见的插件类型包括：

* 自定义语言支持
* 框架整合
* 工具集成
* 用户界面附加组件
* 主题

> 在某些情况下，可能不需要实现实际的 IntelliJ 平台插件，因为存在[替代解决方案](plugin_alternatives.md)。
>

## 自定义语言支持

自定义语言支持提供了使用特定编程语言的基本功能，其中包括：

* 文件类型识别
* 词法分析
* 语法高亮
* 格式化
* 代码洞察和代码补全
* 检查和快速修复
* 意图行动

插件还可以增强现有（捆绑）的自定义语言，例如，通过提供额外的检查、意图或任何其他功能。

请参阅[自定义语言支持教程](custom_language_support_tutorial.md) 以了解有关该主题的更多信息。

## 框架集成

框架集成包括改进的代码洞察功能（这是给定框架的典型功能），以及直接从 IDE 使用特定于框架的功能的选项。
有时它还包括自定义语法或 DSL 的语言支持元素。

* 具体代码洞察
* 直接访问特定于框架的功能

框架集成示例请参考[IntelliJ-HCL](%gh-ij-plugins%/terraform)。
更多参考插件可以在 [JetBrains Marketplace](https://plugins.jetbrains.com/search?orderBy=update%20date&shouldHaveSource=true&tags=Framework) 上找到。

## 工具集成

工具集成使得可以直接从 IDE 操作第三方工具和组件，而无需切换上下文，这意味着：

* 实施额外行动
* 相关UI组件
* 访问外部资源

请参考 [Gerrit 集成](https://plugins.jetbrains.com/plugin/7272) 插件作为示例。

## 用户界面附加组件

此类别中的插件对 IDE 的标准用户界面进行了各种更改。
一些新添加的组件是交互式的，并提供了新功能，而有些组件则仅限于视觉修改。
[Foldable ProjectView](https://plugins.jetbrains.com/plugin/17288-foldable-projectview) 插件可以作为示例。

## 主题 (themes)

[主题](themes_getting_started.md) 使设计人员能够自定义内置 IDE UI 元素的外观。
