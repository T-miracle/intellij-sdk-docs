<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# IDE 开发实例

<link-summary>概述在开发过程中用于运行和调试插件的IDE实例。</link-summary>

插件项目可以从 IntelliJ IDEA 的开发实例中运行或调试。  
选择 Gradle 项目的 `runIde` 任务（或基于 _Plugin DevKit_ 的项目选择 [Run](running_and_debugging_a_theme.md) 菜单）  
将启动目标 IDE 的 _开发实例_，并启用当前开发版本的插件。

本页描述了如何控制开发实例的一些设置。

> 另请参阅 `runIde` 任务的属性（参考： [2.x](tools_intellij_platform_gradle_plugin_tasks.md#runIde)，[1.x](tools_gradle_intellij_plugin.md#tasks-runide)）以及 [高级配置](https://www.jetbrains.com/help/idea/tuning-the-ide.html) 以获取通用 VM 选项和属性。
>

## 在开发实例中使用 JetBrains Runtime {id=using-a-jetbrains-runtime-for-the-development-instance}

> 请参阅 [](tools_intellij_platform_gradle_plugin_jetbrains_runtime.md) 以了解如何使用 [](tools_intellij_platform_gradle_plugin.md)。
>
{title="IntelliJ Platform Gradle Plugin (2.x)"}

一个常见的使用场景是针对 JDK（例如 Java 17）开发（构建）一个插件项目，然后在 IDE 的开发实例中运行或调试该插件。  
在这种情况下，开发实例必须使用 [JetBrains Runtime (JBR)](https://www.jetbrains.com/jetbrains-runtime)，而不是用于构建插件项目的 JDK。

JetBrains Runtime 是一个用于在 Windows、macOS 和 Linux 上运行基于 IntelliJ 平台的 IDE 环境。
它经过 JetBrains 的一些修改，例如修复了官方 JDK 构建中不存在的本机崩溃问题。
JetBrains Runtime 的一个版本与所有基于 IntelliJ 平台的 IDE 捆绑在一起。
为了在开发实例中运行或调试插件项目时产生准确的结果，请按照以下步骤确保开发实例使用 JetBrains Runtime。

### 使用 JetBrains Runtime {id=using-jetbrains-runtime}

<tabs group="project-type">

<tab title="Gradle IntelliJ Plugin (1.x)" group-key="gradle">

默认情况下，Gradle 插件会获取并使用与构建插件项目所使用的 IntelliJ 平台版本对应的开发实例的 JetBrains Runtime 版本。
如果需要，可以使用 [`runIde.jbrVersion`](tools_gradle_intellij_plugin.md#tasks-runide-jbrversion) 任务属性来指定替代版本。

</tab>

<tab title="Plugin DevKit" group-key="devkit">

基于 DevKit 的插件项目的 [运行配置](https://www.jetbrains.com/help/idea/run-debug-configuration.html) 控制了在开发实例中运行和调试插件项目时使用的 JDK。
默认的运行配置在构建插件项目和在开发实例中运行插件时都使用相同的 JDK。

要更改开发实例的 runtime，请在运行配置编辑对话框中将 _JRE_ 字段设置为下载 JetBrains Runtime。

</tab>
</tabs>

### 确定 JetBrains Runtime 版本 {id="确定jetbrains-runtime版本"}

JetBrains Runtime 是由用于构建插件项目的 JDK 版本确定的，无论它是在 macOS、Windows 还是 Linux 上构建的。

<procedure title="确定示例 JetBrains Runtime 版本">

如果插件是针对 macOS 上的 Java 8 SE Development Kit 8（<path>jdk-8u212-macosx-x64.dmg</path>）开发的，为获取兼容的 JetBrains Runtime 应该进行以下步骤：

1. 前往 [GitHub JetBrains Runtime Releases](https://github.com/JetBrains/JetBrainsRuntime) 获取一般信息和最新构建版本。
2. 打开 [Releases](https://github.com/JetBrains/JetBrainsRuntime/releases) 页面以查看所有发布版本。
3. 选择与平台和SDK版本相对应的包名称。
   在这种情况下，包名称为 `jbrsdk8-osx-x64` 是适用于 **J**et**B**rains **R**untime 的 _SDK_ 版本 8，macOS x64 的硬件。
4. 在文件列表中，找到满足以下条件的名称：
    - 版本和构建号与用于构建插件项目的JDK匹配。
      例如，`jbrx-8u252-osx-x64`与 Java 8 JDK 的构建 252 匹配：`jdk-8u252-macosx-x64`。
    - 选择可用的最高 JetBrains Runtime 构建号。
      例如，文件名为 <path>jbrx-8u252-osx-x64-b1649.2.tar.gz</path>，这意味着这个 JetBrains Runtime 版本为 1649.2，与 Java 8 JDK 版本为 252 的构建相匹配。

</procedure>

### JetBrains Runtime 变种 {id="jetbrains-runtime变种"}

JetBrains Runtime 以不同的版本发布，用于不同的目的，如调试、开发运行或与 IDE 捆绑。

可用的 JBR 变体有：

- `jcef` - 带有 [JCEF](jcef.md) 浏览器引擎的发布捆绑包。
- `sdk` - 用于开发目的的 JBR SDK 捆绑包
- `fd` - fastdebug 捆绑包，其中还包括 `jcef` 模块。
- `dcevm` - 包含DCEVM（Dynamic Code Evolution Virtual Machine（动态代码演进虚拟机））的捆绑包。
- `nomod` – 没有任何额外模块的发布捆绑包。

> 对于 `JBR 17`，默认捆绑了 `dcevm`。
> 因此，不再提供分开的 `dcevm` 和 `nomod` 变种。
>
{style="note"}

## 启用自动重载 {id=enabling-auto-reload}

<primary-label ref="2020.1"/>

从 2020.1 版本开始，兼容的 [动态插件](dynamic_plugins.md) 可以使用此功能。  
它通过在检测到代码更改时（例如 JAR 文件被修改）避免完全重启开发实例，从而大大加快了开发周期。

请注意，在生产环境中的任何卸载问题都会要求用户重新启动 IDE。

> 当沙盒 IDE 实例在调试器下运行时，自动重载功能不会起作用。
>
{style="warning" title="Debugging"}

### IntelliJ Platform Gradle Plugin (2.x) {id=intellij-platform-gradle-plugin-2x}

自动重载默认启用。

将属性 [`intellijPlatform.autoReload`](tools_intellij_platform_gradle_plugin_extension.md#intellijPlatform-autoReload) 设置为 `false` 以显式禁用它，  
详细信息请参见 [](tools_intellij_platform_gradle_plugin_faq.md#how-to-disable-the-automatic-reload-of-dynamic-plugins)。

启动沙盒 IDE 实例后，在插件项目修改后运行 [`buildPlugin`](tools_intellij_platform_gradle_plugin_tasks.md#buildPlugin) 任务，然后切换回沙盒实例以触发重载。

> 当前必须 [显式禁用](tools_intellij_platform_gradle_plugin_faq.md#how-to-disable-building-the-searchable-options) [`buildSearchableOptions`](tools_intellij_platform_gradle_plugin_tasks.md#buildSearchableOptions) 任务，以解决 _每次只能运行一个 IDEA 实例_ 的问题。
>
{style="warning"}

### Gradle IntelliJ Plugin (1.x) {id=gradle-intellij-plugin-1x}

{collapsible="true" default-state="collapsed"}

<primary-label ref="Obsolete"/>

<include from="tools_gradle_intellij_plugin.md" element-id="gradlePluginObsolete"/>

自动重载默认在 2020.2 版本或更高版本的目标平台上启用。

对于早期版本的目标平台，将属性 [`runIde.autoReloadPlugins`](tools_gradle_intellij_plugin.md#tasks-runide-autoreloadplugins) 设置为 `true` 以启用自动重载，  
或者将其设置为 `false` 以显式禁用，详见 [](tools_gradle_intellij_plugin_faq.md#how-to-disable-automatic-reload-of-dynamic-plugins)。

启动沙盒 IDE 实例后，在插件项目修改后运行 [`buildPlugin`](tools_gradle_intellij_plugin.md#tasks-buildplugin) 任务，然后切换回沙盒实例以触发重载。

> 当前必须 [显式禁用](tools_gradle_intellij_plugin_faq.md#how-to-disable-building-searchable-options)
> [`buildSearchableOptions`](tools_gradle_intellij_plugin.md#tasks-buildsearchableoptions) 任务，以解决
> _每次只能运行一个 IDEA 实例_ 的问题。
>
{style="warning"}

### Plugin DevKit {id=plugin-devkit}

{collapsible="true" default-state="collapsed"}

在 _Plugin DevKit_ 的 [运行配置](running_and_debugging_a_theme.md) 中添加系统属性 `idea.auto.reload.plugins`。

要禁用自动重载，请将 `idea.auto.reload.plugins` 显式设置为 `false`（2020.1.2+）。

## 开发实例沙盒目录 {id=the-development-instance-sandbox-directory}

_沙盒主目录_ 包含 IDE 开发实例的 [设置、缓存、日志和插件](#development-instance-settings-caches-logs-and-plugins)。
这些信息存储在与[已安装的IDE本身](https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs)不同的位置。

### IntelliJ Platform Gradle Plugin (2.x) {id=sandboxGradle2}

默认的沙盒主目录位置在 [](tools_intellij_platform_gradle_plugin.md) 插件项目中为：

* Windows: <path>\$PROJECT_DIRECTORY\$\\build\\\$TARGET_IDE\$\\idea-sandbox</path>
* Linux/macOS: <path>\$PROJECT_DIRECTORY\$/build/\$TARGET_IDE\$/idea-sandbox</path>

沙盒主目录位置可以通过 [`intellijPlatform.sandboxContainer`](tools_intellij_platform_gradle_plugin_extension.md#intellijPlatform-sandboxContainer) 属性进行配置。

### Gradle IntelliJ Plugin (1.x) {id="sandboxGradle1"}

{collapsible="true" default-state="collapsed"}

<primary-label ref="Obsolete"/>

<include from="tools_gradle_intellij_plugin.md" element-id="gradlePluginObsolete"/>

在 [](tools_gradle_intellij_plugin.md) 插件项目中，默认的沙盒主目录位置为：

* Windows: <path>\$PROJECT_DIRECTORY\$\\build\\idea-sandbox</path>
* Linux/macOS: <path>\$PROJECT_DIRECTORY\$/build/idea-sandbox</path>

沙盒主目录位置可以通过 [`intellij.sandboxDir`](tools_gradle_intellij_plugin.md#intellij-extension-sandboxdir) 属性进行配置。

### Plugin DevKit {id="sandboxPluginDevKit"}

{collapsible="true" default-state="collapsed"}

对于基于 _Plugin DevKit_ 的插件，默认的 <control>沙盒主目录</control> 位置是在 IntelliJ 平台插件 SDK 中定义的。  
有关如何在 IntelliJ 平台 SDK 中设置沙盒主目录的信息，请参见 [设置主题开发环境](setting_up_theme_environment.md#add-intellij-platform-plugin-sdk)。

默认的沙盒主目录位置是：

* Windows: `<path>\$USER_HOME\$\\.\$PRODUCT_SYSTEM_NAME\$\$PRODUCT_VERSION\$\\system\\plugins-sandbox\\</path>
* Linux: `<path>~/.\$PRODUCT_SYSTEM_NAME\$\$PRODUCT_VERSION\$/system/plugins-sandbox/</path>
* macOS: `<path>~/Library/Caches/\$PRODUCT_SYSTEM_NAME\$\$PRODUCT_VERSION\$/plugins-sandbox/</path>

### 开发实例的设置、缓存、日志和插件 {id=development-instance-settings-caches-logs-and-plugins}

在沙盒主目录中，包含有开发实例的子目录：

* `<path>config</path>` 包含 IDE 实例的设置。
* `<path>plugins</path>` 包含运行在 IDE 实例中的每个插件的文件夹。
* `<path>system/caches</path>` 或 `<path>system\caches</path>` 存储了 IDE 实例的数据。
* `<path>system/log</path>` 或 `<path>system\log</path>` 包含 IDE 实例的 `<path>idea.log</path>` 文件。

这些沙盒主目录的每一个子目录都可以手动清除，以重置 IDE 开发实例。
在下一次启动开发实例时，子目录将重新填充适当的信息。
