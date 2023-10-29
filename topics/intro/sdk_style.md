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

文档项目使用 [WriterSide](https://www.jetbrains.com/writerside)，因此应安装插件以获得完整的 IDE 支持。
这些主题文件本身是 [Markdown](https://github.github.com/gfm/) 文件（<path>*.md</path>），使用了一些 WriterSide 特定的自定义标签（请参见下文）。

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
* 有关表示代码片段的最佳实践，请参阅[突出显示语法指南](#高亮语法指南)。
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

* `[Gradle](https://gradle.org)`{disable-links}（[Gradle](https://gradle.org)）链接到外部网站，如公司、文章等。
  如果URL包含 `%` 字符，附加 `{ignore-vars="true"}`。
* 链接到 SDK 文档中的页面和页面章节：
  * `[Page Title](page.md)`{disable-links} 或 `[](page.md)`（使用页面标题作为链接文本）链接到 SDK 文档页面（都位于 <path>/topics</path> 下）。
    请注意扩展名是 <path>.md</path>，而不是 <path>.html</path>。
  * SDK 文档中页面上的特定_章节_可以通过使用章节锚点链接。
    锚点名称将全部小写，空格用“-”替代，例如 `## Page setup` 变为 `#page-setup`。
    一旦输入链接的锚点 (`#`) 字符，IDE 的代码完成功能会显示可用的章节。
    * `[链接到当前页面上的一个章节](#another-section)`{disable-links} 链接到当前页面上的标题。
    * `[链接到另一页上的章节](other_page.md#another-section)`{disable-links} 链接到另一页上的标题。

  如果所需的链接标签与 SDK 文档页面或章节标题相同，可以将标签部分留空，例如 `[](test-page.md)`{disable-links} 或 `[](test-page.md#section-1)`{disable-links}。
  空的链接标签将自动填充为实际的页面或章节标题。

#### 链接到 IntelliJ 平台源代码 { id="链接到IntelliJ平台源代码" }

指向 IntelliJ 平台 (`intellij-community`) 存储库中文件的链接使用 `%\gh-ic%` 前缀，而不是存储库的完整 URL。
指向其他 GitHub 存储库中源代码包中的文件的链接遵循大致相同的规则，除了链接使用 <path>v.list</path> 中定义的不同自定义 `gh-...` 前缀。

* `[README.md](%\gh-ic%/README.md)`{disable-links} 链接到一般的非代码信息文件。 ([README.md](%gh-ic%/README.md))
  此文件类型的示例包括 _LICENSE.txt_ 和 _README.md_。
* `[`IdeaPlugin.xml`](%\gh-ic%/community-resources/src/META-INF/IdeaPlugin.xml)`{disable-links} 链接到声明性源代码文件，使用 `code` 样式。 ([`IdeaPlugin.xml`](%gh-ic%/community-resources/src/META-INF/IdeaPlugin.xml))
  此文件类型的示例包括：`settings.gradle`、`plugin.xml` 或 `theme_basics.theme.json`。
* `[`AnAction`](%\gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)`{disable-links} 链接到代码对象的源文件，如接口和类，使用 `code` 样式，但不包括文件扩展名。（[`AnAction`](%gh-ic%/platform/editor-ui-api/src/com/intellij/openapi/actionSystem/AnAction.java)）
  此文件类型的示例包括 Java 和 Kotlin 源文件。
  * 请注意链接中类名周围的 `` 字符的使用。
  * 在这种方式链接到 API 时，链接中不需要 FQN。
  * 按照惯例，不使用文件扩展名（*.java，*.kt，*.py 等）。
  * 在使用此类链接时要审慎。
    通常，文档页面上只需要一个链接指向给定的文件。

### 高亮语法指南 {id="高亮语法指南"}

段内代码片段和 IntelliJ 平台 API 根据以下规则进行格式化。

#### 代码

* 避免使用限定词，如“`Foo` 接口”或“`Foo` 抽象类”。
  反而，参考 `Foo`。
* FQN 在页面上首次引用接口、类或包时使用。
  将其介绍为 `com.intellij.openapi.actionSystem.AnAction`，而不是 `AnAction`。
  页面上随后的引用可以使用 `AnAction`。
  例外情况：在 GitHub [链接](#链接) 中不使用 FQN。
* 方法名总是使用空括号：“调用 `bar()` 来应用。”
  方法名在需要明确时以类/接口名称为前缀：“`Foo.bar()`”。

#### 扩展点

* 首次在页面上出现的扩展点名称必须后跟 "extension point (EP)"。
  随后提及可以使用 "EP" 后缀。
* 在首次介绍 [扩展点](plugin_extension_points.md)（EP）时，使用完全限定名（FQN）。
  而不是 `stubIndex`，介绍 `com.intellij.stubIndex`。
  页面上随后的提及可以使用 `stubIndex`。

#### XML

* 对于 XML 元素，请使用带有语法高亮的标记表示：`<idea-version>`。
  属性使用语法高亮显示，并且属性值显示在引号内：`since-build="191"`。

### 源代码

源代码通过使用代码围栏表示，它们由三个反引号组成。

通过在第一组反引号之后指定语言来应用语法高亮：

```
    ```xml
    <tagName attribute="value">XML Text</tagName>
    ```
```

支持的语言包括 `xml`, `java`, `kotlin`, `groovy`, `bash`, `md`, `php`，以及纯文本的 `text`。

> 源代码块前后必须有一行空白，并且必须具有用于高亮的语言规范（使用 `text` 作为备用）。
>
{style="note"}

可以使用代码围栏后的 `src` 属性导入整个文件，该属性指定了相对于<path>code_samples</path>根目录的完整路径。

`{src="simple_language_plugin/src/main/java/org/intellij/sdk/language/SimpleFoldingBuilder.java"}`

优点是代码可以来自`code_samples`目录，因此它将是实时代码，不会悄无声息地过时。

缺点是文件可能包含一个较大的类，太大以至于使文档页面无法使用。
如果可能的话，可以使用 `include-symbol="ClassName"` 仅显示类体，不包括任何头部和导入。
要仅包括特定的方法，请另外指定 `include-symbol="methodName"`。

无论如何，请确保代码示例简洁，避免任何不必要的“包围”代码或导入语句。

### 表格

语法是使用竖线 (`|`) 和减号：

```
    | Column 1 | Column 2 | Column 3 |
    |----------|----------|----------|
    | Blah     | Blah     | Blah     |
```

在单元格内的`<ui-path>`元素中，使用 `& #124;`（在 `#` 前去掉空格！）而不是 `|`，以避免转义问题。

### 注释和标注

可以使用块引用语法来指定注释和标注。
转换器会查看文本块后面指定的 `type` 属性。
如果有指定，它会应用标注样式。
下面的示例将被显示为一个标注，样式为 "note"：

```
> This is a simple note.
>
{style="note"}
```

> 这是一个简单的注释。
>
{style="note"}

可用于标注的样式有：

* `tip` — 使读者更具生产力的信息（默认）。
* `note` — 读者理解所需的重要信息。
  此标注保留用于关键要点和概念。
* `warning` — 用户必须理解的信息，以防止失败或错误。

复杂的标注还可以指定 `title` 属性：

```
> This is a note.
> We have a lot of text.
> Don't make everyone read it fully by adding a good title.
>
{title="A useful title"}
```

> 这是一个注释。
> 我们有很多文本。
> 通过添加一个好的标题
> 不要让每个人都完全阅读它。
>
{title="A useful title"}

### 图片

通常，每个页面在根目录<path>/images</path>下都有一个专用的子目录。

本文档中的图像通常是屏幕截图。
为了保持一致，图像应具有296、460或706像素宽。
首选的图像格式是分辨率为144 DPI的PNG。
72 DPI的分辨率也可以接受，但在高分辨率显示器上可能会显得模糊。

使用 [Window Resizer](https://plugins.jetbrains.com/plugin/18045-window-resizer) 插件来精确调整IDE应用程序窗口的大小。

减小图像文件的大小非常重要，以防止使存储库臃肿并影响文档站点的性能。
使用像 [PNG optimizer](https://plugins.jetbrains.com/plugin/7942-png-optimizer) 插件这样的工具来优化图像文件。

通过向图像添加 Markdown 链接来将图像嵌入到文档中，如下所示：

```
    ![Alt text](image.png)
```

如果需要调整图像的宽度，可以指定如下：

```
    ![Alt text](image.png){width="42"}
```

#### 缩放弹出窗口

过大以至于无法放入主内容的图像可以具有<control>+</control>叠加控制，以打开一个带有"放大"变体的弹出窗口。

<tabs>
<tab title="PNG">

对于 **PNG** 图像，提供一个带有以下注释的额外放大变体<path>image.zoomed.png</path>：

```
    ![Alt text](image.png){thumbnail="true"}
```

</tab>

<tab title="SVG">

对于 **SVG** 图像，请使用以下表示法：

```
    ![Alt text](image.svg){thumbnail-same-file="true"}
```

</tab>

</tabs>

## 目录 { id="目录" }

网站的目录以树形视图的形式显示在站点左侧，并从<path>ijs.tree</path>文件生成。
列表可以包含嵌套项，这些项在目录中显示为子项。

如有必要，可以通过 `toc-title` 属性覆盖页面标题文本，以在目录中显示。

### 占位符

如果节点未指定其 `id` 属性，它仍将出现在目录中，但会变成灰色，无法点击。
它充当文档项的占位符。
占位符有助于跟踪应该记录但尚未记录的内容，也有助于向读者显示该主题存在，但尚未记录（随时欢迎拉请求！）。

### 重定向

在重命名页面时，**必须**配置重定向，以确保现有的书签继续工作。
必须更新其他主题中的所有现有链接。

在 `accepts-web-file-names` 属性中指定以前的路径（包括 <path>.html</path> 扩展名）：

```xml

<toc-element
        id="themes_getting_started.md"
        accepts-web-file-names="themes.html,themes-intro.html"/>
```
