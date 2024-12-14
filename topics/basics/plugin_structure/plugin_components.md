<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 组件（已弃用）
<primary-label ref="Deprecated"/>

<link-summary>将已弃用的插件组件迁移到当前的解决方案。</link-summary>
> 在编写新插件时，必须避免创建组件。  
> 任何现有的组件应该迁移到服务、扩展或监听器（见下文）。
>
{style="warning" title="废弃通知"}

插件组件是一个遗留特性，为了兼容旧版本 IntelliJ 平台上创建的插件而保留。  
使用组件的插件不支持 [动态加载](dynamic_plugins.md)（即在不重启 IDE 的情况下安装、更新和卸载插件）。

插件组件在 [插件配置文件](plugin_configuration_file.md) 中的 `<application-components>`、`<project-components>` 和 `<module-components>` 部分中定义。

## 迁移 {id=migration}

要将现有代码从组件迁移到现代 API，请参阅以下指南。

### 管理状态 {id=manage-state}

要管理仅在用户执行特定操作时才需要的状态或逻辑，请使用 [服务](plugin_services.md)。

### 持久化状态 {id=persisting-state}

要在应用级或项目级存储插件的状态，请使用 [服务](plugin_services.md) 并实现 `PersistentStateComponent` 接口。  
有关详细信息，请参阅 [组件状态的持久化](persisting_state_of_components.md)。

### 订阅事件 {id=subscribing-to-events}

要订阅事件，请使用 [监听器](plugin_listeners.md)，或者如果该事件有对应的扩展点，创建一个 [扩展](plugin_extensions.md) 来进行订阅（例如，`com.intellij.editorFactoryListener`）。

### 应用启动 {id=application-startup}

> 尽量避免在应用启动时执行代码，因为这会拖慢启动速度。

{style="warning"}

插件代码应仅在打开项目时执行（参见 [项目打开](#project-open)）或当用户触发插件的某个操作时执行。  
如果无法避免，在 [`AppLifecycleListener`](%gh-ic%/platform/ide-core/src/com/intellij/ide/AppLifecycleListener.java) 主题上添加一个 [监听器](plugin_listeners.md) 进行订阅。  
另请参阅 [仅执行一次任务](ide_infrastructure.md#running-tasks-once)。

### 项目打开 {id=project-open}

<tabs>

<tab title="2023.1 及之后版本">

使用 [Kotlin 协程](kotlin_coroutines.md)，实现 [`ProjectActivity`](%gh-ic%/platform/core-api/src/com/intellij/openapi/startup/StartupActivity.kt) 并注册到 `com.intellij.postStartupActivity` 扩展点。  
示例：
- [`PowerSaveModeNotifier`](%gh-ic%/platform/lang-impl/src/com/intellij/ide/actions/PowerSaveModeNotifier.kt)
- [`TipOfTheDayStartupActivity`](%gh-ic%/platform/tips-of-the-day/src/com/intellij/ide/TipOfTheDayStartupActivity.kt)

要求使用 [Kotlin](using_kotlin.md) 实现，因为 Java 不支持挂起函数。

</tab>

<tab title="2023.1 之前版本">

要在打开项目时执行代码，请使用以下两种 [扩展](plugin_extensions.md) 之一：

`com.intellij.postStartupActivity`  
: [`StartupActivity`](%gh-ic%/platform/core-api/src/com/intellij/openapi/startup/StartupActivity.kt)，在 [EDT](threading_model.md) 上立即执行。  
实现 `DumbAware` 以指示该活动可以在后台线程中运行（与其他此类任务并行）。

`com.intellij.backgroundPostStartupActivity`  
: [`StartupActivity.Background`](%gh-ic%/platform/core-api/src/com/intellij/openapi/startup/StartupActivity.kt)，在后台线程中延迟 5 秒执行（2019.3 或更高版本）。

任何长时间运行或 CPU 密集型的任务应通过 `ProgressManager.run(Task.Backgroundable)` 显示给用户（参见 [](background_processes.md)）。  
访问索引必须通过 [`DumbService`](indexing_and_psi_stubs.md#dumb-mode) 包装，另请参见 [](threading_model.md)。

另请参阅 [](ide_infrastructure.md#running-tasks-once)。

</tab>

</tabs>

### 项目和应用关闭 {id=project-and-application-close}

要在项目关闭或应用关闭时执行代码，请在 [服务](plugin_services.md) 中实现 [`Disposable`](%gh-ic%/platform/util/src/com/intellij/openapi/Disposable.java) 接口，并将代码放入 `dispose()` 方法中。  
另外，可以使用 `Disposer.register()`，将 `Project` 或 `Application` 服务实例作为 `parent` 参数传递（参见 [选择可销毁的父对象](disposers.md#choosing-a-disposable-parent)）。
