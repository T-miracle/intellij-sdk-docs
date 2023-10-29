# 设置开发环境

<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>搭建开发主题所需的开发环境。</link-summary>

<procedure title="初步步骤">

<include from="snippets.md" element-id="pluginDevKitAvailability"/>

使用以下检查列表确保您已准备好开发自定义主题：

1. 已安装 **[IntelliJ IDEA Community Edition](https://www.jetbrains.com/idea/download/)** 或 **[IntelliJ IDEA Ultimate](https://www.jetbrains.com/idea/download/)**。
2. 已安装并在 IntelliJ IDEA 中 [启用 **Plugin DevKit** 插件](https://www.jetbrains.com/help/idea/managing-plugins.html)。
3. 已检出 **IntelliJ IDEA CE 源代码**。 _(可选)_
   仅当您打算调试 IntelliJ 平台代码时才需要执行此步骤。
   有关更多详细信息，请参阅 [](#intellij-idea-ce)。
4. **IntelliJ 平台 SDK** 已[配置](#intellij-sdk)。

</procedure>

## 获取 IntelliJ IDEA CE 源代码

获取 IntelliJ IDEA CE 源代码并不是主题开发的必要条件，因为在开发主题时调试平台代码的情况很少见。
如果您正在开发扩展 IDE 行为的插件，或者您需要了解某些组件的内部工作原理，那么拥有源代码可以使调试变得更加简单。

有关如何高效查看代码的详细说明，请参阅 [IntelliJ IDEA 社区版 README 文件](%gh-ic%/README.md) 的 _获取 IntelliJ IDEA 社区版 源代码_ 部分。
请注意，在这种情况下不需要构建签出的源。

## 配置 IntelliJ 平台插件 SDK

> 有关 SDK 的更多信息，请参阅 IntelliJ IDEA Web 帮助中的 [SDK 部分](https://www.jetbrains.com/help/idea/working-with-sdks.html)。

### 下载基于 IntelliJ 的 IDE

要在真实的 IDE 中查看开发的插件的效果，需要在[](ide_development_instance.md)中运行插件。
大多数情况下，下载使用 _[IntelliJ IDEA](https://www.jetbrains.com/idea/download/)社区版_ 就足够了。
如果需要对仅在 _IntelliJ IDEA Ultimate_ 或 _WebStorm_ 等特定 IDE 中使用的组件进行样式化，它们也可以用作 SDK，但调试核心代码只能与 _IntelliJ IDEA 社区版_ 一起使用。

### 添加 JDK 和 IntelliJ 平台插件 SDK

配置主题插件 SDK 的第一步是添加 JDK。

<include from="snippets.md" element-id="apiChangesJavaVersion"/>

<procedure title="添加 JDK" id="add-jdk">

1. 跳转到 <ui-path>File（文件） | Project Structure（项目结构） | Platform Settings（平台设置） | SDKs（SDK）</ui-path>.
2. 点击 <control>添加</control> 按钮 (<control>+</control>).
3. 如果您的计算机上安装了所需的 JDK，并且检测到了它，请从 <control>检测到的 SDK</control> 列表中选择它。
   如果未检测到您的 JDK，请选择 <control>添加 JDK...</control> 选项并选择安装文件夹。

   如果您的计算机上未安装所需的 JDK，最简单的选择是使用 <control>下载 JDK...</control> 并选择分发选项。
4. 点击 <control>应用</control> 按钮.

</procedure>

第二步是添加 IntelliJ 平台插件 SDK，它将使用第一步中配置的 JDK。

<procedure title="添加 IntelliJ 平台插件 SDK" id="add-intellij-platform-plugin-sdk">

1. 跳转到 <ui-path>File（文件） | Project Structure（项目结构） | Platform Settings（平台设置） | SDKs（SDK）</ui-path>.
2. 点击 <control>添加</control> 按钮 (<control>+</control>).
3. 选择 <control>添加 IntelliJ 平台插件 SDK...</control> 选项。
4. 选择 IDE 的安装文件夹[之前下载](#intellij-ide)（在 macOS 上，选择 <path>/Applications/</path> 中的应用程序图标）。
5. 在 <control>选择内部Java平台</control> 对话框中，选择[上一步](#add-jdk)中配置的JDK，然后单击 <control>确定</control> 按钮。
6. 在添加的SDK中，指定 <control>Sandbox Home</control> 目录。

   有关详细信息，请参阅 [](ide_development_instance.md#开发实例沙盒目录)。
7. 如果需要调试，请选择 <control>源路径</control> 选项卡，单击 <control>添加</control> 按钮（<control>+</control>）并选择[签出源的根文件夹](#intellij-idea-ce)。
8. 点击 <control>应用</control> 按钮。

</procedure>
