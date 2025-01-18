<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件开发常见问题

<link-summary>插件开发论坛主题列表以及常见问题。</link-summary>

本 FAQ 是一个主题索引，列出了在我们的 [IntelliJ IDEA Open API 和插件开发论坛](https://intellij-support.jetbrains.com/hc/en-us/community/topics/200366979-IntelliJ-IDEA-Open-API-and-Plugin-Development) 上提出（并解答）的问题。

> 另请参阅 [探索 IntelliJ 平台 API](explore_api.md) 以获取更多信息和策略。
>

## 访问和修改源代码 {id=accessing-and-modifying-the-source-code}

> 另请参阅 [](psi.md) 和 [](psi_cookbook.md)

* [如何查找类的所有子类？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791895-finding-all-derived-class-given-parent-class)
* [如何查找类中创建的所有匿名类？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206792205-How-to-find-anonymous-classes-in-PsiClass-)
* [如何计算字符串字面量标记的值？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139829-How-to-evaluate-the-value-of-PsiJavaToken-of-STRING-LITERAL-type)
* [如何重命名或移动 Java 类？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791825-How-to-rename-a-class-)
* [如何构建给定类或包使用的所有类的列表？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139469-Using-DependencyValidationManager-to-Get-Required-Classes)
* [如何将空白插入到 PSI 中？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143839-Adding-PsiElements-to-a-PsiFile)
* [如何向 .properties 文件添加属性？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206142279-Dynamically-add-new-properties-to-properties-files)
* [如何在 PsiMethod 中查找特定的方法调用？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143579-finding-a-statement-within-a-PsiMethod)
* [PSI 元素的生命周期是什么？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206796015-What-is-the-lifecycle-of-a-PsiElement-)
* [如何在给定包中创建新类？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206771665-Creating-a-new-class)
* [如何使 PsiClass 继承另一个 PsiClass？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206794255-How-to-make-a-PsiClass-derive-from-another-one-)
* [如何从非 Java 文件中查找对类的引用？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206800695-How-to-obtain-the-references-to-a-class-from-non-java-files-)
* [给定 .class 文件的路径，如何查找源文件？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206800595-How-to-find-the-source-for-a-class-file)
* [如何查找具有指定非限定名称的类？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206146759-How-to-resolve-unqualified-name-to-possible-PsiClasses-)

## 处理 XML 和 XML DOM {id=working-with-xml-and-xml-dom}
*  [如何通过 PSI 更改 XML 属性的值？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139639-Change-xml-attribute-value)
*  [如何以编程方式注册 DTD 或模式？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206795425-How-to-register-DTD-with-idea)

## 重构 {id=refactoring}
*  [如何接收有关重构事件的通知？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206795955-Refactoring-Listeners)
*  [如何以编程方式显示重构对话框？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206800005-How-to-invoke-refactoring-dialog-not-refactoring-itself-)

## 构建/编译 {id=makecompile}
*  [如何访问由 javac 生成的类文件？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206800625-Implementing-a-ClassInstrumentingCompiler-how-to-get-the-generated-class-files)

## 版本控制集成 {id=version-control-integration}
*  [我可以为自定义文件系统中的文件提供行状态标记吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791585-Editor-diff-functionality-for-custom-file-system)
*  [如何根据文件状态更新 VCS 操作的状态？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791975-VCS-context-menu)
*  [如何查找已删除文件的模块？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206792195-Module-for-deleted-file-)
*  [我可以为 Changes 视图中的变更列表提供额外的装饰吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139549-Is-it-possible-to-decorate-change-lists-)
*  [如何报告过时的文件？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791775-VCS-OpenAPI-what-to-do-with-files-detected-as-out-of-date-)

## 编辑器、文档和文件 {id=editors-documents-and-files}
*  [为什么通过 PSI 更改文件后，磁盘上的文件没有变化？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791625-Action-doesn-t-see-changes-in-xml-file)
*  [我可以挂钩到文件保存逻辑吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206790685-Can-you-tie-into-the-file-save-logic-)
*  [我可以将文件的一部分标记为只读吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/207042355-Read-only-section-in-editor)
*  [如何控制用户尝试编辑此类部分时发生的情况？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791375-Using-locked-regions)
*  [如何为单个文件在标签页中显示多个编辑器？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206795495-Alternative-Editors-ala-HTML-Preview)
*  [我可以打开一个没有磁盘上底层文件的编辑器吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206135449-Create-an-Editor-for-a-non-physical-file)
*  [如何在源代码编辑器中高亮显示元素？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143909-MarkupModel-navigate-highlighted-elements)
*  [如何允许使用“查找下一个”在高亮显示的元素之间导航？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143879-HighlightManager-how-to-enable-F3-functionality)
*  [如何获取活动编辑器实例？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206141119-how-to-get-the-Editor-from-PsiElement-)
*  [如何清除文件的只读状态？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206142039-Clear-read-only-status)
*  [如何在工具窗口中显示带有错误高亮的编辑器？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206146679-Error-highlighting-in-Editors)

## 检查 {id=inspections}
*  [如何提供创建方法的快速修复？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206142769-Triggering-Create-Method-intention)
*  [是否可以仅检查自上次完整检查以来已修改的元素？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206800645-How-to-inspect-only-the-elements-modified-since-the-last-class-inspection)
*  [ExternalAnnotator 与当前编辑器不同步](https://intellij-support.jetbrains.com/hc/en-us/community/posts/115000337510-Only-trigger-externalAnnotator-when-the-file-system-is-in-sync)

## 项目结构 {id=project-structure}
*  [我可以添加新的模块依赖存储格式吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206137859-Dependency-storage-formats-)
*  [传递给 JavaModuleBuilder.setSourcePaths() 的 Pair 是什么？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143559-Usage-of-class-Pair-A-B-)

## 自定义语言 {id=custom-languages}
*  [如何在我的语言中提供自动弹出代码补全？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139359-Autopopup-code-completion-in-custom-language)
*  [如何使闭合括号取消缩进？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206797085-Custom-Language-How-to-make-a-closing-brace-unindent-)
*  [如何为编译为 Java 的自定义语言启用调试？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206786875-Debugging-custom-languages-)
*  [如何生成虚拟 Java 类以镜像我的语言的类？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143749-Custom-languages-masquarding-as-a-java-source-file-within-IntelliJ)

## 用户界面 {id=user-interface}
*  [如何提供动画状态栏通知？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206791945-IDE-Notifications)
*  [如何在组合框中启用文件名补全？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139509-Combobox-with-Browse-Button-and-Autocompletion-)
*  [如何显示一个弹出窗口，其中每个项目都有左对齐和右对齐的部分？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139049-popup-menu-with-left-and-right-aligned-items)
*  [如何为文件/PSI 元素提供自定义图标？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206143779-Is-it-possible-to-change-icon-of-file-in-Project-view-)
*  [我可以为 WriteActions 显示进度指示器吗？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206139159-WriteActions-and-ProgressIndicator)
*  [如何基于结构视图显示自定义窗口或弹出窗口？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206142679-Opening-a-customised-StructureView)
*  [如何以编程方式显示包选择器对话框？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206794265-Package-selector-dialog)
*  [如何在组合框编辑器中提供语法和错误高亮？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206800495-EditorTextField-in-3403-How-to-get-an-Editor-that-does-error-highlighting-)
*  [如何在我的 Usage View 窗口中提供关闭和重新运行按钮？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206146779-How-to-get-a-Close-button-in-an-own-Usage-View-)
*  [如何在 JComboBox 中显示 SDK 列表？](https://stackoverflow.com/questions/51499884/how-to-display-the-sdk-list-in-a-jcombobox)

## 通用 {id=general}
*  [如何实现自定义堆栈跟踪分析器？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206142959-Stack-Analyzer-extension)
*  [如何以编程方式打开项目？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206146969-how-to-open-a-project-)
*  [如何获取当前选定文件的文件夹？](https://intellij-support.jetbrains.com/hc/en-us/community/posts/206121889-How-to-get-the-folder-of-currenctly-selected-file)
