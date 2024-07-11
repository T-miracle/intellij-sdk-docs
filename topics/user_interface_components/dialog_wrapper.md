<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>创建、显示和获取用户在对话框中提供的输入。</link-summary>

# 对话框

<tldr>

**平台 UI 指南：** [布局](https://jetbrains.design/intellij/principles/layout)，[验证错误](https://jetbrains.design/intellij/principles/validation_errors/)

</tldr>

## DialogWrapper {id=dialogwrapper}

[`DialogWrapper`](%gh-ic%/platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java) 是 IntelliJ 平台中用于显示所有模态对话框（以及部分非模态对话框）的基类。

它提供以下功能：

* 按钮布局（平台特定顺序的 <control>OK</control>/<control>Cancel</control> 按钮，macOS 特有的 <control>Help</control> 按钮）
* 上下文帮助
* 记住对话框的大小
* 非模态验证（当输入的数据无效时显示错误消息文本）
* 快捷键：
  * <shortcut>Esc</shortcut> 关闭对话框
  * <shortcut>Left/Right</shortcut> 切换按钮
  * <shortcut>Y</shortcut>/<shortcut>N</shortcut> 对应 <control>Yes</control>/<control>No</control> 操作（如果对话框中存在）
* 可选的 <control>不再询问</control> 复选框

### 用法 {id=usage}

当使用 [`DialogWrapper`](%gh-ic%/platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java) 类创建对话框时，请按照以下必需步骤进行操作：

* 在对话框将要显示的项目的框架中，调用基类构造函数并提供一个 `Project`，或者提供对话框的父组件。
* 调用 `setTitle()` 方法设置对话框的标题。
* 在对话框类的构造函数中调用 `init()` 方法。
* 实现 `createCenterPanel()` 方法返回对话框主要内容的组件。

可选步骤：

* 重写 `getPreferredFocusedComponent()` 方法并返回对话框首次显示时应聚焦的组件。
* 重写 `getDimensionServiceKey()` 方法返回用于持久化对话框大小的标识符。
* 重写 `getHelpId()` 方法返回与对话框关联的上下文帮助主题（参见 [上下文帮助](ide_infrastructure.md#context-help)）。

`DialogWrapper` 类通常与 [GUI 设计器表单](https://www.jetbrains.com/help/idea/gui-designer-basics.html) 结合使用。
在这种情况下，将 GUI 设计器表单绑定到扩展 `DialogWrapper` 的类，将表单的顶层面板绑定到一个字段，并从 `createCenterPanel()` 方法返回该字段。
在使用 Kotlin 时，可以使用 [Kotlin UI DSL](kotlin_ui_dsl_version_2.md) 提供对话框的内容。

> 参见 IntelliJ 平台 UI 指南中的 [布局](https://jetbrains.design/intellij/principles/layout) 主题，了解如何在对话框中布置 UI 控件的建议。
>
> 可以使用 [UI 检查器](internal_ui_inspector.md) 在运行时检查现有对话框，例如查找 UI 组件的实际实现。

要显示对话框，请调用 `show()` 方法，然后使用 `getExitCode()` 方法检查对话框的关闭方式（参见 `DialogWrapper#OK_EXIT_CODE|CANCEL_EXIT_CODE|CLOSE_EXIT_CODE`）。
可以使用 `showAndGet()` 方法组合这两个调用。

要自定义对话框中显示的按钮（替换标准的 <control>OK</control>/<control>Cancel</control>/<control>Help</control> 按钮集合），可以重写 `createActions()` 或 `createLeftActions()` 方法。
这两个方法都返回一组 Swing Action 对象。
如果按钮用于关闭对话框，请使用 [`DialogWrapperExitAction`](%gh-ic%/platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java) 作为动作的基类。
使用 `action.putValue(DialogWrapper.DEFAULT_ACTION, true)` 来设置默认按钮。

### 输入验证

还请参阅 IntelliJ 平台 UI 指南中的 [验证错误](https://jetbrains.design/intellij/principles/validation_errors/) 主题。

要验证输入到对话框中的数据，请重写 `doValidate()` 方法。
该方法将由定时器自动调用。
如果当前输入的数据有效，请返回 `null`。
否则，返回一个 [`ValidationInfo`](%gh-ic%/platform/ide-core/src/com/intellij/openapi/ui/ValidationInfo.java) 对象，其中封装了错误消息和与无效数据关联的可选组件。
在指定组件时，将在其旁边显示错误图标，并在用户尝试调用 <control>OK</control> 操作时将其聚焦。

## 示例 {id=example}

简单定义一个 [`DialogWrapper`](%gh-ic%/platform/platform-api/src/com/intellij/openapi/ui/DialogWrapper.java)：

```java
public class SampleDialogWrapper extends DialogWrapper {

  public SampleDialogWrapper() {
    super(true); // 使用当前窗口作为父窗口
    setTitle("Test DialogWrapper");
    init();
  }

  @Nullable
  @Override
  protected JComponent createCenterPanel() {
    JPanel dialogPanel = new JPanel(new BorderLayout());

    JLabel label = new JLabel("Testing");
    label.setPreferredSize(new Dimension(100, 100));
    dialogPanel.add(label, BorderLayout.CENTER);

    return dialogPanel;
  }
}
```

当用户点击按钮时显示 `SampleDialogWrapper` 对话框：

```java
JButton testButton = new JButton();
testButton.addActionListener(actionEvent -> {
  if (new SampleDialogWrapper().showAndGet()) {
    // 用户按下了 OK
  }
});
```
