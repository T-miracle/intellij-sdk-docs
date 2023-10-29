# SDK 文档风格指南

<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>SDK 文档的写作和引用风格指南。</link-summary>

本文档描述了编写开源 IntelliJ Platform SDK 文档时使用的写作风格。
在开始之前，请仔细阅读本页以及 [行为准则](intellij-sdk-docs-original_CODE_OF_CONDUCT.md) 和 [许可证](https://github.com/JetBrains/intellij-sdk-docs/blob/main/LICENSE.txt) 文档。
另请参阅 [](intellij-sdk-docs-original_CONTRIBUTING.md) 了解一些一般性说明。
有关为 IntelliJ 平台本身做出贡献的信息，请访问 [为 IntelliJ 平台做出贡献](platform_contributions.md)。

首先，我们应该牢记我们的受众和他们的目标:

_阅读技术内容的人通常是在寻找一个特定问题的答案。
这个问题可能是广泛的，也可能是狭隘的，但无论如何，我们的目标是在不分散注意力的情况下提供答案_

Intellij 平台 SDK 文档 的风格是通过使用名为 [Markdown](https://github.github.com/gfm/) 的标记语言来捕获的。

为了验证语法和正确的拼写，强烈建议使用 [Grazie Professional](https://plugins.jetbrains.com/plugin/16136-grazie-professional) 插件来突出显示 IDE 中的任何问题。

The documentation project is using [Writerside](https://www.jetbrains.com/writerside), so the plugin should be installed to have full support in the IDE.
The topic files themselves are [Markdown](https://github.github.com/gfm/) files (<path>*.md</path>) using some Writerside-specific custom tags (see below).

文档项目使用 [Writerside](https://plugins.jetbrains.com/plugin/20158-writerside/)，因此应安装该插件才能在 IDE 中获得全面支持。
主题文件本身是使用一些 Writerside 特定自定义标签的 [Markdown](https://github.github.com/gfm/) 文件 (<path>*.md</path>)（见下文）。

### 页面格式

每个 Markdown 文件**必须**以版权声明开头，并使用 HTML 注释符号进行格式化：

```html
<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->
```

它**必须**后面跟着一个使用 1 级标题定义的标题：

```
# Contributing to the IntelliJ Platform SDK
```

页面标题应尽可能简洁，以便可以在 [](#目录) 中按原样重复使用。

#### 摘抄

每个页面**应该**在主页内容之前使用专用的 `<link-summary>` 标签提供一个简短的摘录（通常是一个句子）：

```html

<link-summary>定义相关设置的含义。</link-summary>
```

#### 突出显示的链接

页面可以使用 `<tldr>` 标记在实际内容之前突出显示相关主题和其他重要链接。
链接必须使用“**粗体类别名称**：link1、link2、\[...]”（[示例](language_and_filetype.md)）进行分组。

使用 _参考_ 链接到其他主题，使用 _代码_ 链接到代码/文件，使用 _平台 UI 指南_ 链接到[IntelliJ 平台 UI 指南](https://jetbrains.design/intellij/)，并使用 _产品帮助_ 链接到[IntelliJ IDEA帮助](https://www.jetbrains.com/help/idea)。

#### 介绍性文字

**不要**使用 _介绍_、 _概述_ 等标题作为任何介绍性文本。

## 内容风格

### 术语

一致的术语有助于读者更快地掌握新概念：

* GitHub 存储库 `intellij-community` 中的开源代码被称为 IntelliJ 平台。
  使用 SDK 文档中的完整短语。
* 基于 IntelliJ 平台 的 IDE 被描述为 _基于 IntelliJ 平台的 IDE_。
  一旦在页面上使用该术语，作者就可以使用 _IDE_。
* 当提及 JetBrains 产品时，请始终使用全名，例如 _IntelliJ IDEA Ultimate Edition_。
  但是，仅当可扩展性或功能特定于产品时才使用产品名称。

> 在 `intellij-sdk-docs` 的主分支中不鼓励使用 TODO/todo 注释。
> 例外总是会有的，但最佳的习惯是在最终审核之前解决所有 TODO。
> 如果无法立即解决，请编写一个 YouTrack [SDK 问题](https://youtrack.jetbrains.com/issues/IJSDK) 来捕获 TODO 并从文档中删除注释。
>
{title="不要使用 TODO"}

### 文本格式约定

每个句子都另起一行。
对于很长的句子，请在 `,`、`:` 或其他合理的地方添加额外的换行。
很长的 [链接](#链接) 也应该单独一行。

使用一致的文本样式来标准化参考文献和关键字：

* 菜单路径使用 `<ui-path>` 包裹，并用管道字符分隔每个级别： `<ui-path>Settings | Editor</ui-path>`: <ui-path>Settings | Editor</ui-path>。
  在表格内部， 使用 `&#124;` 代替 `|` 防止出现问题。
* 用户界面元素名称（如标签、按钮、复选框等）使用 `<control>` 进行包装： `Press <control>Continue</continue>`：Press <control>Continue</control>
* 非代码关键字和引用，或者非代码文件的名称被格式化为斜体样式：\_Theme\_ (_Theme_)、\_README.md\_ (_README.md_.)
  此文件类型的示例包括 _LICENSE.txt_ 和 _README.md_。
* 代码关键字和类名的格式为代码样式：\`interface\`：`interface`，\`AnAction \`：`AnAction`，\`name \`属性：`name`属性。
* 文件名使用 `<path>` 包装：`<path>build.gradle.kts</path>` <path>build.gradle.kts</path>。
* 文件格式显示为全部大写字母：PNG 和 XML。
* 当文件扩展名是完整文件名、路径或 URL 的一部分时，文件扩展名不会大写：<path>plugin.xml</path>。
* 键盘快捷键使用 `<shortcut>` 进行包装：`press <shortcut>Alt+Insert</shortcut>` 变为 “press <shortcut>Alt+Insert</shortcut>”。
* 有关表示代码片段的最佳实践，请参阅[突出显示语法指南](#guidelines-for-highlighting-syntax)。
* 有关在链接中表示源文件名称的更多详细信息，请参阅 [IntelliJ 平台源代码链接](#链接到IntelliJ平台源代码)。

### 链接 { id="链接" }

链接作为标准 Markdown 链接处理，可以锚定到外部站点、站点内的页面或页面中的标题。

当 Markdown 标头转换为 HTML 标头时，它会被分配一个 ID，以便可以链接。
例如，`## Basics` 获取 `basics` 的 ID，并且可以在同一页面或跨页面链接，如下所述。

在某些情况下（例如，相同的标题文本在同一页面上多次出现），有必要手动指定不同的 ID：

```
## Task 1
### Properties
{#task1-properties}
[...]

## Task 2
### Properties
{#task2-properties}
[...]
```

#### 一般链接

一般 Markdown 链接具有默认的 Markdown 链接样式：

* `[Gradle](https://gradle.org)`{disable-links} ([Gradle](https://gradle.org)) links to an external site, such as companies, articles, etc.
  If URL contains `%` character, append `{ignore-vars="true"}`.
* Linking to pages and page sections within the SDK documentation:
    * `[Page Title](page.md)`{disable-links} or `[](page.md)` (use page title as link text) links to an SDK doc page (all located under <path>/topics</path>).
      Note that the extension is <path>.md</path>, _NOT_ <path>.html</path>.
    * Specific _sections_ on pages in the SDK documentation are linked by using section anchors.
      The anchor name will be all lower case, and spaces are replaced with `-`, e.g. `## Page setup` becomes `#page-setup`.
      Once the anchor (`#`) character of the link is entered, the IDE code completion feature shows the available sections.
        * `[Link to a section on the current page](#another-section)`{disable-links} links to a heading on the current page.
        * `[Link to the section on another page](other_page.md#another-section)`{disable-links} links to a heading on another page.

  If the desired link label is the same as an SDK doc page or section title, leave the label part empty, e.g., `[](test-page.md)`{disable-links} or `[](test-page.md#section-1)`{disable-links}.
  The empty link label will be automatically filled with the actual page or section title.

#### 链接到 IntelliJ 平台源代码 { id="链接到IntelliJ平台源代码" }

指向 IntelliJ 平台 (`intellij-community`) 存储库中文件的链接使用 `%\gh-ic%` 前缀，而不是存储库的完整 URL。
指向其他 GitHub 存储库中源代码包中的文件的链接遵循大致相同的规则，除了链接使用 <path>v.list</path> 中定义的不同自定义 `gh-...` 前缀。

* `[README.md](%\gh-ic%/README.md)`{disable-links} links to general, non-code information files. ([README.md](%gh-ic%/README.md))
  Examples of this file type include _LICENSE.txt_ and _README.md_.
* `[`IdeaPlugin.xml`](%\gh-ic%/community-resources/src/META-INF/IdeaPlugin.xml)`{disable-links} links to declarative source code files, use `code` style. ([`IdeaPlugin.xml`](%gh-ic%/community-resources/src/META-INF/IdeaPlugin.xml))
  Examples of this file type include: `settings.gradle`, `plugin.xml` or `theme_basics.theme.json`.
* `[`\`AnAction\``](%\gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)`{disable-links} links to source files for code objects like interfaces and classes, use `code` style but without the file extension. ([`AnAction`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java))
  Examples of this file type include Java and Kotlin sources.
    * Note the use of \`\` characters surrounding the class name in the link.
    * When linking to an API in this manner, the FQN isn't necessary in the link.
    * No file extension (*.java, *.kt, *.py, etc.) is used by convention.
    * Be judicious when using such links.
      Generally, only one link is needed for a given file on a documentation page.

### Guidelines for Highlighting Syntax

In-paragraph code fragments and IntelliJ Platform APIs are formatted according to the following rules.

#### Code

* Avoid using qualifiers like "`Foo` interface" or "`Foo` abstract class".
  Instead, refer to `Foo`.
* The FQN is used for the first reference to an interface, class, or package on a page.
  Rather than `AnAction`, introduce it as `com.intellij.openapi.actionSystem.AnAction`.
  Subsequent references on the page can be `AnAction`.
  Exception: the FQN is not used with a GitHub [link](#链接).
* Method names always use empty parentheses: "call `bar()` to apply."
  Method names are prefixed with the class/interface name when needed for clarity: `Foo.bar()`.

#### Extension Points

* Extension point name must be followed by "extension point (EP)" for the first occurrence on a page.
  All following can use "EP" suffix.
* Use the FQN when first introducing an [extension point](plugin_extension_points.md) (EP) on a page.
  Rather than `stubIndex`, introduce `com.intellij.stubIndex`.
  Subsequent mentions on the page can be `stubIndex`.

#### XML

* For XML elements, use the tag notation with syntax highlighting: `<idea-version>`.
  Attributes are shown with syntax highlighting, and attribute values are shown in quotes: `since-build="191"`

### Source Code

Source code is represented by using code fences, which are three backticks.

Syntax highlighting is applied by specifying the language after the first set of ticks:

```
    ```xml
    <tagName attribute="value">XML Text</tagName>
    ```
```

Supported languages include `xml`, `java`, `kotlin`, `groovy`, `bash`, `md`, `php`, and `text` for plaintext.

> Source code blocks must have one blank line before and after them, and must have a language specification for highlighting (use `text` as fallback).
>
{style="note"}

Whole files can be imported on a page using `src` attribute after code fences specifying the full path relative to <path>code_samples</path> root folder.

`{src="simple_language_plugin/src/main/java/org/intellij/sdk/language/SimpleFoldingBuilder.java"}`

The advantage is the code can come from the `code_samples` directory, so it will be live code that isn't silently stale.

The disadvantage is the file may contain a large class, too large for the documentation page to be useful.
If possible, use `include-symbol="ClassName"` to show only the class body without any headers and imports.
To include only a specific method, specify `include-symbol="methodName"` additionally.

In any case, please keep code samples concise and avoid any unnecessary "surrounding" code or import statements.

### Tables

The syntax is to use the pipe (`|`) and minus symbols:

```
    | Column 1 | Column 2 | Column 3 |
    |----------|----------|----------|
    | Blah     | Blah     | Blah     |
```

Use `& #124;` (remove space before `#`!) instead of `|` to prevent escaping problems for `<ui-path>` elements inside cells.

### Notes and Callouts

Notes and callouts can be specified using the blockquote syntax.
The converter looks at the `type` attribute specified after the text block.
If so, it applies a callout style.
The example below will be displayed as a callout, styled as a "note":

```
> This is a simple note.
>
{style="note"}
```

> This is a simple note.
>
{style="note"}

The styles available for callouts are:

* `tip`—Information that makes the reader more productive (Default).
* `note`—Information that is important for the reader to understand.
  This callout is reserved for essential points and concepts.
* `warning`—Information that is critical for the user to understand to prevent failures or errors.

Complex callouts can also specify `title` attribute:

```
> This is a note.
> We have a lot of text.
> Don't make everyone read it fully by adding a good title.
>
{title="A useful title"}
```

> This is a note.
> We have a lot of text.
> Don't make everyone read it fully by
> adding a good title.
>
{title="A useful title"}

### Images

Every page typically has a dedicated subdirectory within root <path>/images</path>.

Images in this documentation are generally screenshots.
For consistency, images should be 296, 460, or 706 pixels wide.
The preferred image format is PNG at 144 DPI resolution.
A resolution of 72 DPI is acceptable but may look blurry on high-resolution monitors.

Use [Window Resizer](https://plugins.jetbrains.com/plugin/18045-window-resizer) plugin for exact resizing of the IDE application window.

It is crucial to reduce the size of image files to prevent bloating the repository and impacting the performance of the documentation site.
Optimize the image files using a tool such as the [PNG optimizer](https://plugins.jetbrains.com/plugin/7942-png-optimizer) plugin.

Images are embedded in a document by adding a Markdown link to the image like so:

```
    ![Alt text](image.png)
```

If the width of an image needs to be adjusted, it can be specified as follows:

```
    ![Alt text](image.png){width="42"}
```

#### Zoom Popup

Images too big to fit into main content can have <control>+</control> overlay control to open a popup with the "zoomed" variant.

<tabs>
<tab title="PNG">

For **PNG** images, provide an additional zoomed variant <path>image.zoomed.png</path> with this notation:

```
    ![Alt text](image.png){thumbnail="true"}
```

</tab>

<tab title="SVG">

For **SVG** images, use this notation:

```
    ![Alt text](image.svg){thumbnail-same-file="true"}
```

</tab>

</tabs>

## 目录 { id="目录" }

The table of contents for the site is displayed in the tree view on the left side of the site, and it is generated from the <path>ijs.tree</path> file.
The list can have nested items, which are displayed as child items in the table of contents.

If absolutely required, overriding the page title text to show in table of contents is possible via `toc-title` attribute.

### Placeholders

If a node does not have its `id` attribute specified, it will still appear in the table of contents but will be greyed out and not clickable.
It acts as a placeholder for a documentation item.
A placeholder is useful to keep track of what should be documented, but hasn't yet, and can be helpful to show readers that the topic exists, but isn't yet documented (Pull Requests always welcome!).

### Redirects

When renaming pages, redirects **must** be configured so existing bookmarks continue working.
All existing links in other topics must be updated.

Specify the previous path(s) including <path>.html</path> extension in `accepts-web-file-names` attribute:

```xml

<toc-element
        id="themes_getting_started.md"
        accepts-web-file-names="themes.html,themes-intro.html"/>
```
