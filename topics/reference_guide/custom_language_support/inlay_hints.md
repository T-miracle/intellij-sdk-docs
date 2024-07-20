<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 内联提示

<link-summary>在不更改文档内容的情况下，直接在编辑器中提供额外的代码信息。</link-summary>

<tldr>

**产品帮助：** [内联提示](https://www.jetbrains.com/help/idea/inlay-hints.html)

</tldr>

内联提示将小块信息直接渲染到编辑器中，为开发者提供额外的代码洞察而不会打断工作流程。
一个常见的示例是参数提示，通常显示函数参数的名称，如其声明中所示。
它们与 [参数信息](parameter_info.md) 紧密相关，后者显示函数所有可能重载的参数类型，但以弹出窗口的形式覆盖在代码上。

内联提示具有灵活性，并在 IntelliJ Platform 中有广泛的应用。
例如，以下是使用内联提示的知名示例：

- Java 使用内联提示在 Java 链式方法调用中显示类型注解。
- Kotlin 在区间表达式中使用内联提示显示小于或小于等于符号，以便开发者了解区间是包含还是不包含。
- 在版本控制的项目中，代码的作者使用内联提示显示。

## 实现 {id=implementation}

内联提示的主要特征是其在编辑器中的显示方式：
- **内联** - 内联提示显示在代码令牌之间
- **块状** - 内联提示显示在代码块上方

根据需求和目标 IntelliJ Platform 版本，实施内联提示时可以选择多个扩展点。

本节描述了可用的 API 及其使用案例。

> 要检查 IDE 中现有的内联提示，请使用 [UI 检查器](internal_ui_inspector.md#editor)。
> 对应的设置项可从 <ui-path>Settings | Editor | Inlay Hints</ui-path> 获得，也可以从 [](internal_ui_inspector.md#inspecting-settings) 中查看。

### 内联参数提示提供者 {id=inlay-parameter-hints-provider}

内联参数提示是简单的字符串 **内联** 提示，放置在方法和函数调用的参数名称之前。
无法提供高级的展示和行为自定义。

要提供内联参数提示，需实现
[`InlayParameterHintsProvider`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/InlayParameterHintsProvider.java)
并在 `com.intellij.codeInsight.parameterNameHints` 扩展点 (EP) 中注册。
`InlayParameterHintsProvider` 的 API 文档详细解释了所有方法的设计 rationale。

**示例：**
- [`GroovyInlayParameterHintsProvider`](%gh-ic%/plugins/groovy/src/org/jetbrains/plugins/groovy/codeInsight/hint/GroovyInlayParameterHintsProvider.kt) - 在 Groovy 代码中显示参数提示
- [`KotlinInlayParameterHintsProvider`](%gh-ic%/plugins/kotlin/idea/src/org/jetbrains/kotlin/idea/codeInsight/hints/KotlinInlayParameterHintsProvider.kt) - 在 Kotlin 代码中显示参数提示

要在特定位置抑制内联参数提示，实现
[`ParameterNameHintsSuppressor`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/ParameterNameHintsSuppressor.kt)
并在 `com.intellij.codeInsight.parameterNameHintsSuppressor` EP 中注册。

### 声明式内联提示提供者 {id=declarative-inlay-hints-provider}

声明式内联提示是 **内联** 文本提示，可以包含可展开的可点击项列表。
请注意，由于其与 UI 无关的设计，该 API 的展示定制可能性有限，这允许其由不同的前端技术（不仅仅是 Swing）使用。

要提供声明式内联提示，实现声明式
[`InlayHintsProvider`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/declarative/InlayHintsProvider.kt)
并在 `com.intellij.codeInsight.declarativeInlayProvider` 扩展点 (EP) 中注册。
有关详细信息，请参见 API 文档。

**示例：**
- [`JavaImplicitTypeDeclarativeInlayHintsProvider`](%gh-ic%/java/java-impl/src/com/intellij/codeInsight/hints/JavaImplicitTypeDeclarativeInlayHintsProvider.kt) - 在 Java 代码中显示用 `var` 关键字声明的变量的推断类型，当推断类型可能不清晰时
- [`JavaMethodChainsDeclarativeInlayProvider`](%gh-ic%/java/java-impl/src/com/intellij/codeInsight/hints/JavaMethodChainsDeclarativeInlayProvider.kt) - 在 Java 代码中的方法调用链中显示方法返回类型

### 代码视图提供者 {id=code-vision-provider}

> 此 API 仍处于实验状态，可能会在不保持向后兼容的情况下进行更改。
>
{style="note"}

代码视图提供者允许为类、方法、字段等元素提供 **块状** 内联提示。
如果为单个元素提供了多个提示，则所有提示将显示在同一行上以节省垂直空间。

代码视图提示可以显示在元素上方或右侧行末。
用户可以在 <ui-path>Settings | Editor | Inlay Hints | Code vision</ui-path> 中通过选择 <control>Default position for metrics</control> 组合框中的值，或通过在特定提供者条目中选择 <control>Position</control> 来配置位置。

实现代码视图提供者有三个扩展点：
- [`DaemonBoundCodeVisionProvider`](%gh-ic%/platform/lang-impl/src/com/intellij/codeInsight/hints/codeVision/DaemonBoundCodeVisionProvider.kt) 注册在 `com.intellij.codeInsight.daemonBoundCodeVisionProvider` 扩展点 (EP)
- [`CodeVisionProvider`](%gh-ic%/platform/lang-impl/src/com/intellij/codeInsight/codeVision/CodeVisionProvider.kt) 注册在 `com.intellij.codeInsight.codeVisionProvider` 扩展点 (EP)
- [`CodeVisionGroupSettingProvider`](%gh-ic%/platform/lang-impl/src/com/intellij/codeInsight/codeVision/settings/CodeVisionGroupSettingProvider.kt) 注册在 `com.intellij.config.codeVisionGroupSettingProvider` 扩展点 (EP)

`DaemonBoundCodeVisionProvider` API 应在代码视图条目与 PSI 相关的情况下使用，以便在 PSI 更改时使计算值无效并重新计算。

`CodeVisionProvider` API 应在呈现的信息不依赖于 PSI 的情况下使用。

`CodeVisionGroupSettingProvider` 是在设置中显示代码视图提供者的名称和描述所必需的。
`groupId` 必须与 `CodeVisionProvider` 实现中指定的值匹配；如果未指定，则默认为 `id`。
`groupName` 是在代码视图组中显示的名称，`description` 将在右侧详细信息面板中可见。

**示例：**
- [`JavaInheritorsCodeVisionProvider`](%gh-ic%/java/java-impl/src/com/intellij/codeInsight/daemon/impl/JavaInheritorsCodeVisionProvider.kt) - 显示 Java 类或方法的继承者数量。点击内联提示将打开继承者列表。该提供者是 `DaemonBoundCodeVisionProvider`。
- [`JavaReferencesCodeVisionProvider`](%gh-ic%/java/java-impl/src/com/intellij/codeInsight/daemon/impl/JavaReferencesCodeVisionProvider.kt) - 显示 Java 类或成员的使用次数。点击内联提示将打开使用列表，或如果只有一个使用实例，则导航到该使用实例。该提供者是 `DaemonBoundCodeVisionProvider`。
- [`VcsCodeVisionProvider`](%gh-ic%/platform/vcs-impl/src/com/intellij/codeInsight/hints/VcsCodeVisionProvider.kt) - 基于 VCS 信息显示给定元素（例如类或方法）的作者。该提供者是 `CodeVisionProvider`。

### 内联提示提供者 {id=inlay-hints-provider}

内联提示提供者允许实现自定义展示和行为的 **内联** 和 **块状** 内联提示。有关详细信息，请参见 API 文档。

> 对于在 2023.1 及更高版本中实现 **内联** 内联提示，建议使用 [](#declarative-inlay-hints-provider)。
>
> 对于在 2022.1 及更高版本中实现 **块状** 内联提示，建议使用 [](#code-vision-provider)。
>
{title="弃用通知" style="warning"}

要提供内联提示，实现 [`InlayHintsProvider`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/InlayHintsProvider.kt) 并在 `com.intellij.codeInsight.inlayProvider` 扩展点 (EP) 中注册。有关详细信息，请参见 API 文档。

**示例：**
- [`GroovyLocalVariableTypeHintsInlayProvider`](%gh-ic%/plugins/groovy/src/org/jetbrains/plugins/groovy/codeInsight/hint/types/GroovyLocalVariableTypeHintsInlayProvider.kt) - 显示 Groovy 代码中的局部变量类型
- [`MarkdownTableInlayProvider`](%gh-ic%/plugins/markdown/core/src/org/intellij/plugins/markdown/editor/tables/ui/MarkdownTableInlayProvider.kt) - _装饰_ Markdown 文件中的表格。
- 复杂示例请参见 [`KotlinLambdasHintsProvider`](%gh-ic%/plugins/kotlin/idea/src/org/jetbrains/kotlin/idea/codeInsight/hints/KotlinLambdasHintsProvider.kt)，以及其父类和所有实现。

### 进一步提示

1. 前往 <ui-path>Settings | Editor | Inlay Hints</ui-path> ([产品帮助](https://www.jetbrains.com/help/idea/inlay-hints.html))，查看已实现的内联提示。
2. 要支持多种语言的单一类型的内联提示，请参见声明式 [`InlayHintsProviderFactory`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/declarative/InlayHintsProviderFactory.kt)（2023.1+）或 [`InlayHintsProviderFactory`](%gh-ic%/platform/lang-api/src/com/intellij/codeInsight/hints/InlayHintsProviderFactory.kt)（2023.1 之前）。
3. 要测试内联提示，请参见 [`InlayHintsProviderTestCase`](%gh-ic%/platform/testFramework/src/com/intellij/testFramework/utils/inlays/InlayHintsProviderTestCase.kt) 和 [`InlayParameterHintsTest`](%gh-ic%/platform/testFramework/src/com/intellij/testFramework/utils/inlays/InlayParameterHintsTest.kt)。
