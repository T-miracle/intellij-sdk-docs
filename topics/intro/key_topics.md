<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 重点专题

<link-summary>IntelliJ 平台关键概念和扩展点概述。</link-summary>

IntelliJ 平台广泛且功能强大，其规模和范围最初可能非常令人害怕。
此页面旨在列出插件作者感兴趣的关键专题，并提供最常见扩展点的快速链接。

## 基本概念

- [](developing_plugins.md).
- [](testing_plugins.md).
- 组件模型 - IntelliJ 平台是一个基于组件的应用程序，负责创建组件和注入依赖项。
  理解这一点对于构建插件是必要的。
- [扩展点](plugin_extensions.md) - 如何使用扩展点注册组件，以及如何查找可用的扩展点。
- [](virtual_file.md) - 所有文件访问都应通过虚拟文件系统，该系统抽象并缓存文件系统。
  这意味着您可以使用本地文件系统上的文件、zip 文件中的文件或版本控制中的旧版本文件。

> 另请参阅 [](glossary.md) 以获取常用术语的便捷参考。
>

## 代码模型

IntelliJ 平台的代码模型称为 PSI - [程序结构接口](psi.md)。
PSI 用于解析代码、构建索引并创建语义模型。

## 常用扩展点

IntelliJ 平台的可扩展性极强，大多数功能和服务都可以扩展。
一些常见的扩展点有以下几点：

* [](basic_action_system.md) - 菜单和工具栏项
* [](code_inspections.md) - 代码分析，查看语法树和语义模型并在编辑器中突出显示问题。
* [](code_intentions.md) - 当文本插入符号位于特定位置时，可在 <shortcut>Alt+Enter</shortcut> 菜单中执行特定于上下文的操作。
* [](code_completion.md).
