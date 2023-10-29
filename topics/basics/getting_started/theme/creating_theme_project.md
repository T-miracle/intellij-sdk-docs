<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 创建主题项目

<link-summary>使用 Plugin DevKit 向导创建主题插件项目并生成项目概述。</link-summary>

本文档页面描述了使用[新建项目向导](https://www.jetbrains.com/help/idea/new-project-wizard.html)生成的基于 DevKit 的主题项目。

## 使用新建项目向导创建主题

> 在创建主题项目之前，请确保[已设置好开发环境](setting_up_theme_environment.md)。

<procedure title="创建主题插件" id="create-theme">

通过 <control>新项目</control> 向导 <ui-path>File | New | Project...</ui-path> 操作并提供以下信息:
1. 从左侧列表中选择 <control>IDE 插件</control> 生成器类型。
2. 指定项目 <control>名称</control> 和 <control>位置</control> 。
3. 在项目 <control>类型</control> 中选择 <control>主题</control> 选项。
4. 作为 <control>JDK</control>，选择[配置的SDK](setting_up_theme_environment.md#intellij-sdk)。
5. 点击 <control>创建</control> 按钮来生成项目。

</procedure>

### 根据向导生成的 Gradle IntelliJ 平台主题的组件

使用上述步骤创建的示例 “my_theme”，_IDE 插件_ 将生成以下目录内容：

```text
my_theme
├── resources // 资源
│   ├── META-INF
│   │   └── plugin.xml // 插件主配置文件
│   │   └── pluginIcon.svg // 插件图标
│   └── theme
│       └── my_theme.theme.json // 主题配色文件
└── my_theme.iml
```

- <path>META-INF</path> 目录：
  - <path>[plugin.xml](plugin_configuration_file.md)</path> 包含预配置主题提供程序的配置文件
  - <path>pluginIcon.svg</path> 文件是[插件 logo](plugin_icon_file.md)

  建议将其替换为自定义图标。
- <path>my_theme.theme.json</path> - 一个简单的[主题描述文件](themes_customize.md#introduction-to-theme-description-file-syntax)
- <path>my_theme.iml</path> - [IntelliJ IDEA 模块](https://www.jetbrains.com/help/idea/creating-and-managing-modules.html)配置文件

See [](theme_structure.md) for more details.

## 额外的插件主题

主题插件项目[使用新建项目向导创建](#create-theme)默认包含一个主题描述文件。
如果插件需要提供多个主题（例如，深色和浅色变体），可以使用专用操作添加它们。

可以使用 _Plugin DevKit_ 插件中的 DevKit 主题向导来添加额外的插件主题。
该向导可用于基于 DevKit 和基于 Gradle 的插件。

<procedure title="添加主题" id="add-theme">

1. 在<control>Project</control>工具窗口中，选择<path>resources</path>目录，并右键单击以调出上下文菜单。
2. 选择<ui-path>New | Plugin DevKit | Theme</ui-path>操作。

   在创建的主题名称中包含插件名称是一种良好的做法。
3. 如果创建的主题应该基于 IntelliJ IDEA _Darcula_ 主题，请勾选<control>Dark theme</control>复选框。否则，将使用 _Light_ 主题作为基础。
4. 单击<control>OK</control>按钮生成以<path>$THEME_NAME$.theme.json</path>命名的[主题描述文件](themes_customize.md#introduction-to-theme-description-file-syntax)。生成的主题将自动配置在<path>plugin.xml</path>文件中。

</procedure>
