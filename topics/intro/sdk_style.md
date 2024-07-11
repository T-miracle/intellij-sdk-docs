# SDK 文档风格指南

<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>SDK文档写作和注释风格指南。</link-summary>

该文档描述了在编写开源IntelliJ平台SDK文档时使用的写作风格。
在开始之前，请仔细阅读本页内容，以及[行为准则](intellij-sdk-docs-original_CODE_OF_CONDUCT.md)和[许可证](https://github.com/JetBrains/intellij-sdk-docs/blob/main/LICENSE.txt)文档。
请参阅 [](intellij-sdk-docs-original_CONTRIBUTING.md) 获取一些常规说明。
有关如何为IntelliJ平台做出贡献的信息，请访问[贡献到IntelliJ平台](platform_contributions.md)。

首先，我们应该牢记我们的受众及其目标：
_阅读技术内容的人通常是为了解答特定问题。
这个问题可能是广泛的或者是具体的，但无论如何，我们的目标是在不分散注意力的情况下提供答案。_

IntelliJ平台SDK文档的风格通过使用名为[Markdown](https://github.github.com/gfm/)的标记语言来体现。

为了验证语法和正确拼写，强烈推荐使用[Grazie Professional](https://plugins.jetbrains.com/plugin/16136-grazie-professional)插件，在IDE中实时突出显示任何问题。

## 文档标记 {id=documentation-markup}

该文档项目使用[Writerside](https://www.jetbrains.com/writerside)，因此应安装该插件以在IDE中获得全面支持。
主题文件本身是[Markdown](https://github.github.com/gfm/)文件（<path>*.md</path>），使用一些特定于Writerside的自定义标签（见下文）。

### 页面格式 {id=page-format}

每个Markdown文件**必须**以版权声明开头，使用HTML注释格式化：

```html
<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->
```

接着**必须**是一个定义标题的页眉，使用一级标题：

```
# 贡献到IntelliJ平台SDK
```

页面标题应尽可能简洁，以便可以直接在[](#table-of-contents)中重复使用。

#### 摘录 {id=excerpt}

每个页面**应该**在主要页面内容之前使用专用的`<link-summary>`标签提供一个简短的摘录（通常是一句话）：

```html
<link-summary>定义相关设置的组。</link-summary>
```

#### 突出链接 {id=highlighted-links}

页面可以在实际内容之前使用`<tldr>`标签突出显示相关主题和其他重要链接。
链接必须使用“**粗体类别名称**：link1, link2, [...]”进行分组（[示例](language_and_filetype.md)）。

使用 _Reference_ 链接到其他主题， _Code_ 链接到代码/文件， _Platform UI Guidelines_ 链接到[IntelliJ平台UI指南](https://jetbrains.design/intellij/)， _Product Help_ 链接到[IntelliJ IDEA帮助](https://www.jetbrains.com/help/idea)。

#### 介绍性文本

在任何介绍性文本中**不要**使用像 _Introduction_, _Overview_ 等标题。

## 内容风格 {id=content-style}

### 术语

一致的术语有助于读者更快地理解新概念：

* GitHub仓库`intellij-community`中的开源代码称为IntelliJ平台。在SDK文档中使用完整短语。
* 基于IntelliJ平台的IDE被描述为 _IntelliJ平台基础的IDE_。一旦在页面上使用了该术语，作者可以使用 _IDE_。
* 当提及JetBrains产品时，始终使用完整名称，如 _IntelliJ IDEA Ultimate Edition_。然而，仅在对某个产品的可扩展性或功能特别时使用产品名称。

> 在`intellij-sdk-docs`的主分支中，不鼓励使用TODO/todo注释。
> 总会有例外，但最佳实践是在最终审查前解决所有TODO。
> 如果无法立即解决，请在YouTrack [SDK Issue](https://youtrack.jetbrains.com/issues/IJSDK)中记录TODO，并从文档中删除该注释。
>
{title="不要使用TODO"}

### 文本格式约定 {id=text-format-conventions}

每个句子都应从新的一行开始。
对于非常长的句子，在`,`、`:`或其他合理的位置后添加额外的换行。
非常长的[链接](#links)也应放在单独的一行上。

一致的文本样式用于标准化引用和关键词：

* 菜单路径使用 `<ui-path>` 包裹，使用竖线字符分隔每个级别：`<ui-path>Settings | Editor</ui-path>`： <ui-path>Settings | Editor</ui-path>
  在表格内部，请使用 `&#124;` 替代 `|`，以避免转义问题。
* 用户界面元素名称如标签、按钮、复选框等使用 `<control>` 包裹：`Press <control>Continue</control>`： 按下 <control>Continue</control>
* 非代码关键词、引用或非代码文件名称使用斜体样式：\_Theme\_ (_Theme_), \_README.md\_ (_README.md_).
  此类文件的示例包括 _LICENSE.txt_ 和 _README.md_。
* 代码关键词和类名使用代码样式包裹：\`interface\`： `interface`, \`AnAction\`： `AnAction`, \`name\` 属性： `name` 属性。
* 文件名使用 `<path>` 包裹：`<path>build.gradle.kts</path>` <path>build.gradle.kts</path>.
* 文件格式以全大写显示：PNG 和 XML。
* 文件扩展名在组成完整文件名、路径或URL时不大写： <path>plugin.xml</path>.
* 键盘快捷键使用 `<shortcut>` 包裹：`press <shortcut>Alt+Insert</shortcut>` becomes "press <shortcut>Alt+Insert</shortcut>".
* 查看[语法高亮指南](#guidelines-for-highlighting-syntax)以了解表示代码片段的最佳实践。
* 查看[链接到IntelliJ平台源代码](#links-to-intellij-platform-source)获取更多关于在链接中表示源文件名称的详细信息。

### 链接 {id=links}

链接作为标准的Markdown链接处理，可以链接到外部站点、站点内的页面或页面中的标题。

当Markdown标题转换为HTML标题时，会分配一个ID，以便可以进行链接。
例如，`## 基础` 被分配ID为 `basics`，可以在同一页或跨页链接，如下所述。

在某些情况下（例如，同一页上出现相同的标题文本多次），需要手动指定一个唯一的ID：

```
## 任务1
### 属性
{#task1-properties}
[...]

## 任务2
### 属性
{#task2-properties}
[...]
```

#### 通用链接 {id=general-links}

通用的Markdown链接采用默认的Markdown链接样式：

* `[Gradle](https://gradle.org)`{disable-links} ([Gradle](https://gradle.org)) 链接到外部站点，例如公司、文章等。
  如果URL包含 `%` 字符，请附加 `{ignore-vars="true"}`。
* 链接到SDK文档中的页面和页面部分：
  * `[页面标题](page.md)`{disable-links} 或 `[](page.md)`（使用页面标题作为链接文本）链接到SDK文档页面（所有位于 <path>/topics</path> 下）。
    注意扩展名为 <path>.md</path>，_不是_ <path>.html</path>。
  * SDK文档页面上的具体 _部分_ 可以通过使用部分锚点进行链接。
    锚点名称将全部小写，并且空格会被替换为 `-`，例如 `## 页面设置` 变为 `#page-setup`。
    输入链接的锚点（`#`）字符后，IDE代码完成功能会显示可用的部分。
    * `[链接到当前页面上的一个部分](#another-section)`{disable-links} 链接到当前页面上的一个标题。
    * `[链接到另一页上的一个部分](other_page.md#another-section)`{disable-links} 链接到另一页上的一个标题。

  如果期望的链接标签与SDK文档页面或部分标题相同，请将标签部分留空，例如 `[](test-page.md)`{disable-links} 或 `[](test-page.md#section-1)`{disable-links}。
  空的链接标签将自动填充为实际的页面或部分标题。

#### 链接到IntelliJ平台源代码 {id=links-to-intellij-platform-source}

链接到IntelliJ平台（`intellij-community`）仓库中的文件使用 `%\gh-ic%` 前缀而不是完整的仓库URL。
链接到其他GitHub仓库中源代码包中的文件遵循类似的规则，但链接使用在 <path>v.list</path> 中定义的不同的自定义 `gh-...` 前缀。

* `[README.md](%\gh-ic%/README.md)`{disable-links} 链接到一般的非代码信息文件。 ([README.md](%gh-ic%/README.md))
  此类文件包括 _LICENSE.txt_ 和 _README.md_。
* `[`IdeaPlugin.xml`](%\gh-ic%/community-resources/resources/META-INF/IdeaPlugin.xml)`{disable-links} 链接到声明式源代码文件，使用 `code` 样式。 ([`IdeaPlugin.xml`](%gh-ic%/community-resources/resources/META-INF/IdeaPlugin.xml))
  此类文件包括：`settings.gradle`、`plugin.xml` 或 `theme_basics.theme.json`。
* `[`\`AnAction\``](%\gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)`{disable-links} 链接到代码对象如接口和类的源文件，使用 `code` 样式但不带文件扩展名。 ([`AnAction`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java))
  此类文件包括Java和Kotlin源码。
  * 注意链接中类名周围的 \` 字符。
  * 在这种方式链接到API时，链接中不需要全限定名（FQN）。
  * 按照惯例，不使用文件扩展名（*.java, *.kt, *.py等）。
  * 在文档页面中谨慎使用此类链接。
    通常情况下，一个文件在文档页面上只需要一个链接。

### 高亮语法指南 {id=guidelines-for-highlighting-syntax}

段落内的代码片段和IntelliJ平台API按照以下规则进行格式化。

#### 代码 {id=code}

* 避免使用诸如“`Foo`接口”或“`Foo`抽象类”这样的限定词。
  取而代之，使用 `Foo`。
* 第一次引用页面上的接口、类或包时使用全限定名（FQN）。
  例如，不要使用 `AnAction`，而是引入 `com.intellij.openapi.actionSystem.AnAction`。
  页面上后续的引用可以使用 `AnAction`。
  例外：在GitHub [链接](#links) 中不使用全限定名。
* 方法名称总是使用空括号："调用 `bar()` 进行应用。"
  当需要清晰明了时，方法名称前缀使用类/接口名称：`Foo.bar()`。

#### 扩展点 {id=extension-points}

* 首次在页面上提到扩展点名称后必须加上“扩展点（EP）”。
  页面上后续的提及可以使用“EP”后缀。
* 首次在页面上介绍扩展点（EP）时，请使用全限定名（FQN）。
  例如，不要使用 `stubIndex`，而是介绍 `com.intellij.stubIndex`。
  页面上后续的提及可以简称为 `stubIndex`。

#### XML {id=xml}

* 对于XML元素，请使用标签表示法进行语法高亮：`<idea-version>`。
  属性使用语法高亮显示，属性值用引号括起来：`since-build="191"`。

### 源代码 {id=source-code}

源代码使用三个反引号表示代码块。

通过在第一组反引号后指定语言来应用语法高亮：

```xml
<tagName attribute="value">XML Text</tagName>
```

支持的语言包括 `xml`, `java`, `kotlin`, `groovy`, `bash`, `md`, `php`，和 `text` 用于纯文本。

> 源代码块前后必须有一个空行，并且必须指定语言以进行语法高亮（可以使用 `text` 作为后备）。
>
{style="note"}

可以使用代码块后的 `src` 属性导入整个文件，指定相对于 <path>code_samples</path> 根目录的完整路径。

`{src="simple_language_plugin/src/main/java/org/intellij/sdk/language/SimpleFoldingBuilder.java"}`

优势在于代码可以来自 `code_samples` 目录，因此它将是活动的代码而不是静态的。

缺点是文件可能包含一个较大的类，对于文档页面来说可能过于庞大。
如果可能，可以使用 `include-symbol="ClassName"` 来仅显示类体而不包括任何头部和导入语句。
要仅包含特定方法，请额外指定 `include-symbol="methodName"`。

无论如何，请确保代码示例简洁，并避免任何不必要的“周围”代码或导入语句。

### 表格 {id=tables}

表格的语法是使用竖线 (`|`) 和减号 (`-`) 符号：

```
    | Column 1 | Column 2 | Column 3 |
    |----------|----------|----------|
    | Blah     | Blah     | Blah     |
```

在单元格内部使用 `<ui-path>` 元素时，请使用 `& #124;`（删除 `#` 前面的空格！）而不是 `|`，以避免转义问题。

### 注释和标注 {id=notes-and-callouts}

注释和标注可以使用块引用语法指定。
转换器会查看文本块后指定的 `type` 属性。
如果有，它会应用标注样式。
下面的示例将显示为一个标注，样式为“note”：

```
> 这是一个简单的注释。
>
{style="note"}
```

> 这是一个简单的注释。
>
{style="note"}

可用于标注的样式包括：

* `tip` — 使读者更加高效的信息（默认）。
* `note` — 读者理解的重要信息。
  此标注保留用于重要的要点和概念。
* `warning` — 用户理解以避免失败或错误的关键信息。

复杂的标注也可以指定 `title` 属性：

```
> 这是一个注释。
> 我们有很多文字。
> 通过添加一个好的标题，不要让每个人完全阅读它。
>
{title="一个有用的标题"}
```

> 这是一个注释。
> 我们有很多文字。
> 通过添加一个好的标题，不要让每个人完全阅读它。
>
{title="一个有用的标题"}

### 图片 {id=images}

每个页面通常在根目录下有一个专用的子目录 <path>/images</path>。

此文档中的图片通常是屏幕截图。
为保持一致性，图片的宽度应为 296、460 或 706 像素。
首选的图片格式是分辨率为 144 DPI 的 PNG。
分辨率为 72 DPI 也可以接受，但在高分辨率显示器上可能会显得模糊。

使用 [Window Resizer](https://plugins.jetbrains.com/plugin/18045-window-resizer) 插件确保精确调整 IDE 应用窗口大小。

为了防止仓库膨胀和影响文档站点的性能，重要的是要减小图片文件的大小。
可以使用 [PNG optimizer](https://plugins.jetbrains.com/plugin/7942-png-optimizer) 插件等工具对图片文件进行优化。

通过以下方式将图片嵌入文档中，将Markdown链接添加到图片：

```
![Alt text](image.png)
```

如果需要调整图片的宽度，可以指定如下：

```
![Alt text](image.png){width="42"}
```

#### 缩放弹出框 {id=zoom-popup}

如果图片过大无法适应主内容区域，可以使用 `<control>+</control>` 叠加控件来打开一个带有“放大”变体的弹出框。

<tabs>
<tab title="PNG">

对于 **PNG** 图片，提供一个额外的带有放大效果的变体 `<path>image.zoomed.png</path>`，使用以下标记：

```
![Alt text](image.png){thumbnail="true"}
```

</tab>

<tab title="SVG">

对于 **SVG** 图片，使用以下标记：

```
![Alt text](image.svg){thumbnail-same-file="true"}
```

</tab>

</tabs>

## 目录 {id=table-of-contents}

站点的目录以树形视图显示在站点左侧，并且是从 <path>ijs.tree</path> 文件生成的。
目录列表可以有嵌套项目，这些项目在目录中显示为子项目。

如有必要，可以通过 `toc-title` 属性覆盖页面标题文本以在目录中显示。

### 占位符 {id=placeholders}

如果节点没有指定其 `id` 属性，它仍会出现在目录中，但会变灰且不可点击。
它作为文档项目的占位符。
占位符有助于跟踪应记录但尚未记录的内容，并向读者显示该主题存在但尚未记录（欢迎提交 Pull Requests！）。

### 重定向 {id=redirects}

在重命名页面时，**必须** 配置重定向，以确保现有的书签继续有效。
所有其他主题中的现有链接必须更新。

在 `accepts-web-file-names` 属性中指定包括 <path>.html</path> 扩展名的先前路径：

```xml
<toc-element
    id="themes_getting_started.md"
    accepts-web-file-names="themes.html, themes-intro.html"/>
```