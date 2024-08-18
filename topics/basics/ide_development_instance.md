<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# IDE 开发实例

<link-summary>概述在开发过程中用于运行和调试插件的IDE实例。</link-summary>

A plugin project can be run or debugged from within the development instance of IntelliJ IDEA.
Selecting the `runIde` task for a Gradle-based project (or [Run](running_and_debugging_a_theme.md) menu for a Plugin DevKit-based project)
will launch a _Development Instance_ of the target IDE with the current development version of the plugin enabled.

This page describes how to control some settings for the Development Instance.

> See also `runIde` task (Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#runIde), [1.x](tools_gradle_intellij_plugin.md#tasks-runide)) properties and [Advanced Configuration](https://www.jetbrains.com/help/idea/tuning-the-ide.html) for general VM options and properties.
>

## 在开发实例中使用 JetBrains Runtime {id=using-a-jetbrains-runtime-for-the-development-instance}

> See [](tools_intellij_platform_gradle_plugin_jetbrains_runtime.md) when using [](tools_intellij_platform_gradle_plugin.md).
>
{title="IntelliJ Platform Gradle Plugin (2.x)"}

An everyday use case is to develop (build) a plugin project against a JDK, e.g., Java 17, and then run or debug the plugin in a Development Instance of the IDE.
In such a situation, Development Instance must use a [JetBrains Runtime (JBR)](https://www.jetbrains.com/jetbrains-runtime) rather than the JDK used to build the plugin project.

JetBrains Runtime 是一个用于在 Windows、macOS 和 Linux 上运行基于 IntelliJ 平台的 IDE 环境。
它经过 JetBrains 的一些修改，例如修复了官方 JDK 构建中不存在的本机崩溃问题。
JetBrains Runtime 的一个版本与所有基于 IntelliJ 平台的 IDE 捆绑在一起。
为了在开发实例中运行或调试插件项目时产生准确的结果，请按照以下步骤确保开发实例使用 JetBrains Runtime。

### Using JetBrains Runtime

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

Starting in 2020.1, this is available for compatible [dynamic plugins](dynamic_plugins.md).
This allows a much faster development cycle by avoiding a full restart of the development instance after detecting code changes (when JARs are modified).

请注意，在生产环境中的任何卸载问题都会要求用户重新启动 IDE。

> 当沙盒 IDE 实例在调试器下运行时，自动重载功能不会起作用。
>
{style="warning" title="Debugging"}

### IntelliJ Platform Gradle Plugin (2.x)

Auto-Reload is enabled by default.

Set property [`intellijPlatform.autoReload`](tools_intellij_platform_gradle_plugin_extension.md#intellijPlatform-autoReload) to `false` to disable it explicitly,
see [](tools_intellij_platform_gradle_plugin_faq.md#how-to-disable-the-automatic-reload-of-dynamic-plugins)

After starting the sandbox IDE instance, run the [`buildPlugin`](tools_intellij_platform_gradle_plugin_tasks.md#buildPlugin) task after modifications
in the plugin project and switch back focus to the sandbox instance to trigger reload.

> [`buildSearchableOptions`](tools_intellij_platform_gradle_plugin_tasks.md#buildSearchableOptions) task must currently be
> [disabled explicitly](tools_intellij_platform_gradle_plugin_faq.md#how-to-disable-building-the-searchable-options) to work around
> _Only one instance of IDEA can be run at a time_ problem.
>
{style="warning"}

### Gradle IntelliJ Plugin (1.x)

{collapsible="true" default-state="collapsed"}

Auto-Reload is enabled by default for target platform 2020.2 or later.

Set property [`runIde.autoReloadPlugins`](tools_gradle_intellij_plugin.md#tasks-runide-autoreloadplugins) to `true` for enabling it in earlier platform versions or `false` to disable it explicitly,
see [](tools_gradle_intellij_plugin_faq.md#how-to-disable-automatic-reload-of-dynamic-plugins)

After starting the sandbox IDE instance, run the [`buildPlugin`](tools_gradle_intellij_plugin.md#tasks-buildplugin) task after modifications in the plugin project
and switch focus back to the sandbox instance to trigger reload.

> [`buildSearchableOptions`](tools_gradle_intellij_plugin.md#tasks-buildsearchableoptions) task must currently be
> [disabled explicitly](tools_gradle_intellij_plugin_faq.md#how-to-disable-building-searchable-options) to work around
> _Only one instance of IDEA can be run at a time_ problem.
>
{style="warning"}

### Plugin DevKit

{collapsible="true" default-state="collapsed"}

在 Plugin DevKit 的 [运行配置](running_and_debugging_a_theme.md) 中添加系统属性 `idea.auto.reload.plugins`。

要禁用自动重载，请将 `idea.auto.reload.plugins` 显式设置为 `false`（2020.1.2+）。

## The Development Instance Sandbox Directory

_沙盒主目录_ 包含 IDE 开发实例的 [设置、缓存、日志和插件](#development-instance-settings-caches-logs-and-plugins)。
这些信息存储在与[已安装的IDE本身](https://intellij-support.jetbrains.com/hc/en-us/articles/206544519-Directories-used-by-the-IDE-to-store-settings-caches-plugins-and-logs)不同的位置。

### IntelliJ Platform Gradle Plugin (2.x) {id="sandboxGradle2"}

The default Sandbox Home location in a [](tools_intellij_platform_gradle_plugin.md) plugin project is:

* Windows: <path>\$PROJECT_DIRECTORY\$\\build\\\$TARGET_IDE\$\\idea-sandbox</path>
* Linux/macOS: <path>\$PROJECT_DIRECTORY\$/build/\$TARGET_IDE\$/idea-sandbox</path>

The Sandbox Home location can be configured with the [`intellijPlatform.sandboxContainer`](tools_intellij_platform_gradle_plugin_extension.md#intellijPlatform-sandboxContainer) property.

### Gradle IntelliJ Plugin (1.x) {id="sandboxGradle1"}

{collapsible="true" default-state="collapsed"}

The default Sandbox Home location in a [](tools_gradle_intellij_plugin.md) plugin project is:

* Windows: <path>\$PROJECT_DIRECTORY\$\\build\\idea-sandbox</path>
* Linux/macOS: <path>\$PROJECT_DIRECTORY\$/build/idea-sandbox</path>

沙盒主目录位置可以通过 [`intellij.sandboxDir`](tools_gradle_intellij_plugin.md#intellij-extension-sandboxdir) 属性进行配置。

### Plugin DevKit {id="sandboxPluginDevKit"}

{collapsible="true" default-state="collapsed"}

For Plugin DevKit-based plugins, the default <control>Sandbox Home</control> location is defined in the IntelliJ Platform Plugin SDK.
See the [Setting Up a Theme Development Environment](setting_up_theme_environment.md#add-intellij-platform-plugin-sdk) for information about how to set up Sandbox Home in IntelliJ Platform SDK.

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
