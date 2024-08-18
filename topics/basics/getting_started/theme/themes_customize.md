<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 自定义主题 - 图标和 UI 控件

<link-summary>自定义主题插件中的 UI 控件和图标。</link-summary>

<tldr>

**UI 指南:** [](icons_style.md), [](platform_theme_colors.md#ui-components)

</tldr>

通过向主题描述文件中添加信息，可以自定义主题，这些信息会覆盖基础主题（_Light_ 或 _Darcula_）。

对于插件开发者，[暴露主题元数据](themes_metadata.md) 讨论了自定义键的格式以及如何提供给主题作者的信息。

> [颜色高亮插件](https://plugins.jetbrains.com/plugin/13309-color-highlighter) 在编辑器内部添加了额外的颜色预览。
>

<include from="developing_themes.md" element-id="themeSamplePlugin"/>

## 主题描述文件语法简介 {id=introduction-to-theme-description-file-syntax}

主题描述文件的语法遵循 JSON 开放标准的键值对文件格式。
最小有效的默认文件是 [DevKit 主题向导](creating_theme_project.md) 的输出。
通过添加键值对来定制主题。

主题的键值对通常使用颜色作为 `value`。
颜色的定义采用六位 RGB 或八位 RGBA 十六进制表示法。

### 主题参考实现 {id=theme-reference-implementations}

在学习新语法时，查看一些现有的实现通常是很有帮助的。
例如，可以参考 [IntelliJ IDEA _High Contrast_ 主题的主题描述文件](%gh-ic%/platform/platform-resources/src/themes/HighContrast.theme.json)。
还可以查看 [JetBrains Marketplace 上可用的主题](https://plugins.jetbrains.com/search?headline=164-theme&tags=Theme)。

## 定义命名颜色 {id=defining-named-colors}

颜色可以始终以六位 RGB 或八位 RGBA 十六进制表示法单独定义。
然而，主题定义中通常会在多个地方使用相同的颜色。
如果在 `colors {}` 块中全局定义 _命名颜色_，管理主题会更为方便，作为 <path>*.theme.json</path> 文件的一部分。
之后，可以使用命名颜色来代替颜色的十六进制描述。
例如，定义命名颜色 `basicBackground`，然后使用它来设置面板的背景颜色。
（下面的例子中的 `"ui"` 语法暂时不需要关注；将在 [自定义 UI 控件颜色](#custom-ui-control-colors) 中讨论。）

```json
{
  "name": "theme_basics",
  "dark": false,
  "author": "IntelliJ Platform SDK",
  "editorScheme": "/Lightning.xml",

  "colors": {
    "basicBackground": "#E1E1E1"
  },
  "ui": {
    "Panel.background": "basicBackground"
  }
}
```

## 自定义图标 {id=customizing-icons}

主题可以自定义默认 IntelliJ Platform UI 图标的颜色，或者用自定义图标替代默认图标。
自定义通过在主题描述文件中添加 `"icons": {}` 部分来实现。

### 覆盖图标的全局颜色 {id=overriding-the-global-color-of-icons}

通过在 `"icons": {}` 部分添加键值对，可以自定义默认的全局图标颜色。
`ColorPalette` 必须插入到 `icons` 部分中。

在以下示例中，`key` - 默认的红色 (`#DB5860`)，用于 _Light_ 主题中的 `Action` 图标 - 被覆盖为 `value` 的不同颜色 (`#D61A26`):

```json
{
  "icons": {
    "ColorPalette": {
      "#DB5860": "#D61A26"
    }
  }
}
```

这种颜色替换会应用到整个 IDE UI 中。

### 自定义图标调色板颜色 {id=custom-icon-palette-colors}

图标调色板是预定义的主题颜色键，用于描述 `Actions` 或 `Objects` 上下文中的单一颜色。

#### "Actions" 和 "Objects" 上下文中的图标颜色 {id=icon-colors-in-actions-and-objects-contexts}

IntelliJ Platform 为 `Actions` 和 `Objects` 上下文定义了默认的图标颜色。
* `Actions` 是指在工具栏上下文中出现的图标的主题键，表示如 _Compile_、_Run_ 或 _Debug_ 等操作。
* `Objects` 是指在列表和树状结构上下文中出现的图标的主题键，表示如文件、符号或运行和调试配置等实体。

[图标 UI 指南](icons_style.md) 定义了 `Actions` 和 `Objects` 键的默认十六进制 RGB 颜色值。
请注意，这份文档将 `Objects` 键称为“名词图标”。

#### 自定义 "Actions" 和 "Objects" 图标颜色 {id=customizing-actions-and-objects-icon-colors}

通过在主题描述文件中的 `"ColorPalette": {}` 部分添加 `Actions` 或 `Objects` 的 `key` 和自定义颜色的 `value` 来定制图标调色板颜色。
可用的图标 `Actions` 和 `Objects` 键的列表由编辑器的自动补全弹出窗口提供：

![Color Palette Popup](theme_colorpalette_popup.png){width="600"}

例如，以下键值对将工具栏上所有蓝色图标的颜色更改为颜色 `#5BC0DE`：

```json
{
  "icons": {
    "ColorPalette": {
      "Actions.Blue": "#5BC0DE"
    }
  }
}
```

此更具体的更改会覆盖默认定义。在蓝色 `Actions` 图标的狭窄上下文中，它还会取代任何全局的默认蓝色图标颜色覆盖。

#### 自定义图标 {id=custom-icons}

默认的 IntelliJ Platform UI 图标可以被自定义图标替换。
图标的文件格式为 SVG。
有关图标的详细规格，请参阅 [UI Guideline for Icons](icons_style.md)。

图标替换在主题描述文件的 `icon {}` 部分中描述。
请注意，图标替换的键值对出现在 `ColorPalette` 部分之外。

对于图标替换，`key` 是默认图标图像的路径。
此路径源自图标部分中报告的 `AllIcons.[Group].[IconName]` 路径，由 [UI Inspector](internal_ui_inspector.md) 报告。

例如，工具栏上的 _Build_ (锤子) 图标的路径为 `Allcons.Actions.Compile`，由 UI Inspector 报告。
因此，_Build_ 图标的 `key` 是 `/actions/compile.svg`。
`value` 是替换图标的文件名，位于主题插件项目的 `resources` 文件夹中：

```json
{
  "icons": {
    "/actions/compile.svg": "/factory.svg"
  }
}
```

替换图标的颜色优先于任何 `ColorPalette` 覆盖。

> 要生成适用于基于 IntelliJ 的 IDE 的 SVG 图标，您还可以使用第三方 Web 工具：[IntelliJ Icon Generator](https://bjansen.github.io/intellij-icon-generator/)。
>

## 自定义 UI 控件 {id=customizing-ui-controls}

主题可以改变 IntelliJ 平台 UI 中更通用控件的外观。
这些控件的示例包括标签、按钮、复选框、树、列表和菜单。

另见 [平台主题颜色](platform_theme_colors.md#ui-components) 在 UI 指南中。

### 自定义 UI 控件颜色 {id=custom-ui-control-colors}

UI 控件的自定义颜色通过向主题描述文件的 `"ui": {}` 部分添加键值对来指定。

UI 控件的 `key` 采用复合格式 `element.property`，其中：
* `element` 是 UI 控件的类型（如标签、复选框等）。
* `property` 是颜色的应用方式。
  示例包括 `foreground`、`background` 和 `errorForeground`。

请注意，一些 UI 控件键有多个部分，例如 `Popup.Advertiser.foreground` 或 `ScrollBar.Mac.Transparent.thumbColor`。
必须使用完整的键来定制特定的按钮控件。
然而，对于其他目的，首部分可以视为 `element`，最后部分视为 `property`。

查找 UI 控件键的方法见 [查找 UI 控件属性键](#finding-attribute-keys-for-ui-controls) 部分。

#### 使用相同属性定制所有 UI 控件颜色 {id=customizing-all-ui-control-colors-with-the-same-property}

所有具有相同 `property` 部分的 UI 控件可以设置为相同的颜色。
这种自定义通过在主题描述文件的通配符 `"*": {}` 部分中完成。
在该部分中插入一个键值对，但仅指定键的 `property` 部分。
`value` 是自定义颜色。

以下示例将所有 UI 控件的默认背景色更改为 `#AED7E3`：

```json
{
  "ui": {
    "*": {
      "background": "#AED7E3"
     }
  }
}
```

请注意，通配符 `"*": {}` 部分必须位于 `"ui": {}` 部分中。

#### 定制特定 UI 控件类型的颜色 {id=customizing-the-color-of-specific-ui-control-types}

通过向主题描述文件的 `"ui": {}` 部分添加键值对，可以改变特定 UI 控件类型的颜色。
`key` 是完整的 `element.property` 格式，`value` 是自定义颜色。

以下示例将所有标签的背景颜色设置为 `#F6E9C9`：

```json
{
  "ui": {
    "*": {
      "background": "#AED7E3"
     },
    "Label.background": "#F6E9C9"
  }
}
```

`Label.background` 条目在标签背景的较窄上下文中会覆盖任何默认颜色以及分配给背景的任何通配符颜色。

#### 定制 UI 标签的颜色 {id=customizing-the-color-of-ui-tabs}

UI 标签的颜色通过主题描述文件中的 [键值对](#custom-ui-control-colors) 来改变。

IntelliJ 平台中有两种标签实现：
* 编辑器标签，代表 [编辑器窗口](https://www.jetbrains.com/help/idea/using-code-editor.html) 和 [工具窗口栏](https://www.jetbrains.com/help/idea/tool-windows.html#bars_and_buttons) 中的打开文件。
* 标签面板，用于 [运行/调试配置对话框](https://www.jetbrains.com/help/idea/run-debug-configurations-dialog.html)。

UI 标签的控件键从 IntelliJ 平台 2019.1 版本扩展到 2019.2 版本。
2019.1 版本的控件键与 2019.2 及更高版本的 IntelliJ 平台兼容。

| 2019.1 版本元素            | 2019.2 版本元素            | 2019.2 版本元素描述                                                            |
|------------------------|------------------------|--------------------------------------------------------------------------|
| N/A                    | `DefaultTabs`          | 应用于所有标签，除了 `TabbedPane`，_除非_ 被更具体的标签控件元素覆盖。                              |
| `EditorTabs`           | `EditorTabs`           | 仅应用于编辑器标签。覆盖任何 `DefaultTab` 设置。192 版本比 191 版本有更多的 `property` 设置。         |
| `ToolWindow.HeaderTab` | `ToolWindow.HeaderTab` | 仅应用于工具窗口标签。覆盖任何 `DefaultTab` 设置。192 版本比 191 版本有更多的 `property` 设置。        |
| `DebuggerTabs`         | `DefaultTabs`          | 使用 `DefaultTab` 设置代替 `DebuggerTabs`，除了 `DebuggerTabs.underlineHeight` 键。 |
| `TabbedPane`           | `TabbedPane`           | 仅应用于标签面板。                                                                |
| `Plugins.Tab`          | `TabbedPane`           | 使用 `TabbedPane` 代替。                                                      |
| `SearchEverywhere.Tab` | `SearchEverywhere.Tab` | 无变化。                                                                     |

识别 UI 控件键的方法见 [查找 UI 控件属性键](#finding-attribute-keys-for-ui-controls) 部分。

例如，以下是 IntelliJ 平台 [高对比度主题](%gh-ic%/platform/platform-resources/src/themes/HighContrast.theme.json) 的一个摘录：
请注意，主题文件可以混合使用不同版本的 `property` 标识符：
* 前三个 `property` 条目被 2019.1 版本识别，但由于它们由新的 `property` 标识符定义，因此会被后续版本忽略。
* `underlineColor` `property` 被 2019.1 版本及后续版本识别。
* `underlineHeight` `property` 在 2019.2 版本中引入，并且会被之前的版本忽略。
* `underlinedTabBackground` `property` 在 2019.2 版本中引入，替代了 2019.1 版本的 `selectedBackground`，并且会被之前的版本忽略。
* `inactiveColoredFileBackground` `property` 在 2019.2 版本中引入，替代了 2019.1 版本的 `inactiveMaskColor`，并且会被之前的版本忽略。

```json
{
  "ui": {
    "EditorTabs": {
      "selectedForeground": "#FFFFFF",
      "selectedBackground": "#0e5d73",
      "inactiveMaskColor": "#000000FF",

      "underlineColor": "#1AEBFF",
      "underlineHeight": 4,

      "underlinedTabBackground": "#000000",
      "inactiveColoredFileBackground": "#00000000"
    }
  }
}
```

### 定制 UI 控件的边框 {id=customizing-the-borders-of-ui-controls}

UI 控件中使用的边框的颜色和几何形状可以通过主题描述文件中的键值对进行自定义。
边框的键格式为 `element.property`，其中：
* `element` 是包含边框的 UI 控件类型，如窗口或弹出菜单。
* `property` 是所需的边框外观，例如：
  * `border` 是边框宽度（以像素为单位），指定为上、左、下和右的宽度。
    边框颜色也可以（可选地）以十六进制 RGB 形式指定，例如 `E6E6E6`，不带 `#` 字符。
  * `borderInsets` 是从 `element` 边缘到边框的内边距距离（以像素为单位）。
    它被指定为上、左、下和右的内边距。

识别 UI 控件键的方法见 [查找 UI 控件属性键](#finding-attribute-keys-for-ui-controls) 部分。

#### 定制特定 UI 控件的边框外观 {id=customizing-the-border-appearance-of-specific-ui-controls}

通过向主题描述文件的 `"ui": {}` 部分添加键值对，可以定制特定 UI 控件类型的边框外观。

以下示例为所有窗口设置了新的边框宽度和颜色：

```json
{
  "ui": {
    "Window.border" : "4,4,4,4,E6E6E6"
  }
}
```

在此示例中，自定义的边框会覆盖默认定义和任何全局颜色覆盖。

## 查找 UI 控件属性键 {id=finding-attribute-keys-for-ui-controls}

IntelliJ 平台 UI 中定义了数百个 UI 控件 `element.property` 键。
可以从 [主题参考实现](#theme-reference-implementations) 中获得一些键和应用策略。
对于一般搜索，以下是一些建议的方法来定位 UI 控件键。

### 使用编辑器中的代码补全查找 UI 控件键 {id=finding-a-ui-control-key-using-code-completion-in-the-editor}

查找 UI 控件键的首选方法是使用编辑器中的 [代码补全](https://www.jetbrains.com/help/idea/auto-completing-code.html#Auto-Completing_Code.xml) 功能。
请注意，代码补全功能提供的一些键可能已被弃用。
在 `"ui": {}` 部分中添加新条目时，会触发代码补全弹出窗口，如下所示：

![UI 控件键代码补全](uit_control_complete.png)

从 IntelliJ 平台 2019.2 版本开始，编辑器新增了代码补全和 [快速文档](https://www.jetbrains.com/help/idea/viewing-reference-information.html#inline-quick-documentation) 功能，以显示 UI 控件键开始支持的版本。
它显示为编辑器弹出窗口中的 `<control>Since</control>` 属性。
在快速文档弹出窗口中，格式为例如 `<control>Since: 2019.2</control>`。
代码补全弹出窗口类似，但格式为例如 `<control>[Since 2019.2]</control>`。

### 使用 Laf Defaults UI 查找 UI 控件键 {id=finding-a-ui-control-key-using-laf-defaults-ui}

使用 [Laf Defaults](internal_ui_laf_defaults.md) 检查器，输入键的 `element` 部分。
Laf Defaults 检查器将显示 UI 控件键及其默认颜色的列表。
