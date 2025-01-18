<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件 Logo

<link-summary>插件徽标要求和最佳实践。</link-summary>

从 2019.1 版本开始，IntelliJ 平台支持为插件提供徽标。  
_插件徽标_ 旨在成为插件功能、技术或公司的独特代表。

在编辑器中打开 <path>plugin.xml</path> 时，检查
<control>Plugin DevKit | Plugin descriptor | Plugin Logo check</control>（2024.3+，  
对于早期版本为 <control>Plugin DevKit | Plugin descriptor | Plugin.xml validity</control>）  
将突出显示缺少的插件图标。

**注意：** 插件中使用的图标和图像有不同的要求。  
有关更多信息，请参阅 [图标](icons.md)。

## 插件徽标用途 {id=plugin-logo-usages}
插件徽标显示在 [JetBrains Marketplace](https://plugins.jetbrains.com) 上。  
它们还出现在基于 IntelliJ 平台的 IDE 中的设置 [插件管理器](https://www.jetbrains.com/help/idea/managing-plugins.html) UI 中。  
无论是在线还是在产品 UI 中，插件徽标都可以帮助用户更快地在列表中识别插件，如下图所示：

![示例产品插件设置对话框](plugin_prefs.png){width="800"}

> 当浏览 [自定义插件仓库](custom_plugin_repository.md) 时，不支持显示托管在那里但尚未安装的插件的徽标。
>
{style="note"}

## 插件徽标要求 {id=plugin-logo-requirements}

> 请参阅 [JetBrains Marketplace 的重要要求](https://plugins.jetbrains.com/docs/marketplace/plugin-overview-page.html#plugin-logo)。
>
{style="note"}

为了在基于 IntelliJ 平台的 IDE 中正确显示插件徽标，它必须：
* 遵循最佳实践设计指南，
* 使用正确的文件格式，
* 符合文件名约定，
* 具有正确的尺寸，
* 位于插件分发文件的 <path>META-INF</path> 文件夹中。

### 插件徽标尺寸 {id=plugin-logo-size}
插件徽标应提供一种尺寸：40px × 40px。

插件徽标会以两种尺寸显示，并在每种上下文中自动缩放：
* 在插件管理器 UI 的插件列表中显示为 40px × 40px。
* 在插件管理器 UI 的插件详情屏幕和 JetBrains Marketplace 的插件页面上显示为 80px × 80px。

请确保插件徽标设计在两种尺寸和所有显示上下文中都有效。

### 插件徽标形状 {id=plugin-logo-shape}
插件徽标设计应在周围留出至少 2px 的透明填充，如下图所示：

![36px × 36px 是徽标可见部分应适应的区域](icon_size.png){width="225"}

确保插件徽标与以下示例中的徽标具有相同的视觉重量。  
插件徽标设计填充得越多，实际需要的空间就越少。  
有关更多 [视觉重量补偿](icons_style.md#basic-shapes) 的示例，请参阅图标 UI 指南。

对于基本形状，请使用以下尺寸。  
注意每种形状使用的透明填充区域不同：

|            ![正方形 32px × 32px](square_logo.png){width="225"}             |        ![圆形 直径 36px](circle_logo.png){width="225"}         |
|:---------------------------------------------------------------------------:|:-----------------------------------------------------------------------:|
|                         _正方形徽标 32px × 32px_                          |                    _圆形徽标 直径 36px_                     |
| ![水平矩形 36px × 26px](rectangle_horizontal.png){width="225"} | ![垂直矩形 26px × 36px](rectangle_vertical.png){width="225"} |
|                 _水平矩形徽标 36px × 26px_                  |                _垂直矩形徽标 26px × 36px_                 |
{style="none"}

### 插件徽标颜色 {id=plugin-logo-colors}
如果插件的技术已经有徽标，请使用其颜色。  
在使用徽标之前，请检查其许可条款。  
如果没有现有徽标，或者其使用被禁止，请根据图标 UI 指南中的 [操作颜色调色板](icons_style.md#action-icons) 创建自定义徽标。

| ![YouTrack 插件徽标使用了 YouTrack 产品徽标](yt_logo.png){height="200" width="200"} | ![Keymap 插件徽标使用了操作颜色调色板中的颜色](keymap_logo.png){:height="200" width="200"} |
|:--------------------------------------------------------------------------------------------------:|:-----------------------------------------------------------------------------------------------------------------:|
|                   _YouTrack 插件徽标使用了<br/>YouTrack 产品徽标_                    |                     _Keymap 插件徽标使用了<br/>操作颜色调色板中的颜色_                      |
{style="none"}

确保插件徽标在浅色和深色背景上都可见。  
如果一个插件徽标设计在浅色和深色背景上都不适用，请分别创建浅色和深色版本的插件徽标。  
以下示例说明了一个插件徽标设计可能在浅色背景上表现良好，但在深色背景上效果不佳。  
因此，需要为深色背景单独设计一个插件徽标。

| ![浅色主题上的插件徽标](light_version.png){width="225"} |     ![深色主题上的浅色插件徽标](dark_bad.png){width="225"}     |     ![深色主题上的插件徽标](dark_good.png){width="225"}      |
|:-------------------------------------------------------------:|:---------------------------------------------------------------------:|:------------------------------------------------------------------:|
| _浅色插件徽标设计<br/>在浅色主题上效果良好_  | _浅色插件徽标设计在<br/>深色主题上效果不佳_ | _单独的深色插件徽标设计<br/>在深色主题上效果良好_ |
{style="none"}

### 插件徽标文件格式 {id=plugin-logo-file-format}
所有插件徽标图像必须为 SVG 格式。  
之所以需要这种矢量图像格式，是因为插件徽标文件必须很小（理想情况下小于 2-3kB），并且图像必须能够缩放而不会损失质量。

> 强烈不建议使用自动将位图图形转换为 SVG 的方式，因为生成的文件会过大（100kB 或更大）。
>
{style="warning"}

### 插件徽标文件命名约定 {id=plugin-logo-file-naming-convention}
根据以下约定命名插件徽标文件：
* <path>pluginIcon.svg</path> 是默认的插件徽标。  
  如果插件中存在用于深色主题的单独徽标文件，则此文件仅用于浅色主题，  
* <path>pluginIcon_dark.svg</path> 是可选的替代插件徽标，仅用于深色 IDE 主题。  

## 将插件徽标文件添加到插件项目 {id=adding-plugin-logo-files-to-a-plugin-project}
插件徽标文件必须打包在 [插件主 JAR 文件](plugin_content.md) 的 <path>META-INF</path> 文件夹中。

要将插件徽标文件包含在内，请将它们放入插件项目的 <path>resources/META-INF</path> 文件夹中。  
例如：

![META-INF 文件夹中的插件徽标文件](resource_directory_structure.png){width="450"}
