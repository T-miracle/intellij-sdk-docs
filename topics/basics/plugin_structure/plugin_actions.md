<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 行为

<link-summary>介绍允许调用插件功能的操作。</link-summary>

IntelliJ 平台提供了 _操作_ 的概念。
一个操作是从 [`AnAction`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java) 派生的类，当选择其菜单项或工具栏按钮时，将调用其 `actionPerformed()` 方法。

操作是用户调用插件功能的最常见方式。
可以通过菜单或工具栏、使用键盘快捷键或 <ui-path>Help | Find Action...</ui-path> 查找来调用操作。

操作组织成组，而组又可以包含其他组。
操作组可以形成工具栏或菜单。
组的子组可以形成菜单的子菜单。

用户可以通过 [菜单和工具栏](https://www.jetbrains.com/help/idea/customize-actions-menus-and-toolbars.html) 设置自定义所有已注册的操作。

请参见 [操作系统](basic_action_system.md) 以了解如何在 IDE 中创建和注册操作。
