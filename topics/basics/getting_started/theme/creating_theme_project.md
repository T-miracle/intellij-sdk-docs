<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 创建主题项目

<link-summary>使用 Plugin DevKit 向导创建主题插件项目并生成项目概述。</link-summary>

本文档页面描述了使用[新建项目向导](https://www.jetbrains.com/help/idea/new-project-wizard.html)生成的基于 DevKit 的主题项目。

## 使用新建项目向导创建主题

创建主题项目前，请确保[开发环境已搭建](setting_up_theme_environment.md)。

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

    It is recommended to replace it with a custom icon.
- <path>my_theme.theme.json</path> - a minimal [theme description file](themes_customize.md#introduction-to-theme-description-file-syntax)
- <path>my_theme.iml</path> - [IntelliJ IDEA Module](https://www.jetbrains.com/help/idea/creating-and-managing-modules.html) configuration file

See [](theme_structure.md) for more details.

## Additional Plugin Themes

Theme plugin project [created with the new project wizard](#create-theme) contains a single theme description file by default.
In case a plugin needs to provide multiple themes (e.g., dark and light variants), it is possible to add them with the dedicated action.

Additional plugin themes can be added using the DevKit Theme Wizard, which is a part of the Plugin DevKit bundled in IntelliJ IDEA.
The wizard can be used for both DevKit-based and Gradle-based plugins.

<procedure title="Add Theme" id="add-theme">

1. In the <control>Project</control> tool window, select the <path>resources</path> directory and invoke the context menu with the right click.
2. Select the <ui-path>New | Plugin DevKit | Theme</ui-path> action.

   It is a good practice to include the name of the plugin in the created theme name.
3. Check the <control>Dark theme</control> checkbox if the created theme should be based on IntelliJ IDEA _Darcula_ theme.
   Otherwise, the _Light_ theme will be used as the base.
4. Click <control>OK</control> button to generate the [theme description file](themes_customize.md#introduction-to-theme-description-file-syntax) with <path>$THEME_NAME$.theme.json</path> name. The generated theme will be automatically configured in the <path>plugin.xml</path> file.

</procedure>
