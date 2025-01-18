<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 动态插件
<primary-label ref="2020.1"/>

<link-summary>使插件动态化允许在不重启 IDE 的情况下安装、更新和卸载插件，以及在开发期间热重载插件更改。</link-summary>

对于所有遵循以下限制的插件，可以在不重启 IDE 的情况下安装、更新和卸载插件。

在插件开发期间，[自动重载](ide_development_instance.md#enabling-auto-reload) 还允许代码更改在沙盒 IDE 实例中立即生效。
要测试动态安装是否正常工作，请验证安装 [本地构建分发](publishing_plugin.md#building-distribution) 是否成功（请参阅 [故障排除](#troubleshooting)）。

请注意，生产环境中的任何卸载问题都会要求用户重启 IDE。

> 如果插件 _需要_ 重启（例如，由于使用原生库），请在 <path>[plugin.xml](plugin_configuration_file.md)</path> 中的 [`<idea-plugin>`](plugin_configuration_file.md#idea-plugin) 根标签中指定 `require-restart="true"`。
>
{style="note"}

> 第三方 **付费** 插件无法在不重启 IDE 的情况下安装、更新或卸载。
>
{style="warning"}

## 限制 {id=restrictions}

为了使插件支持此功能，必须满足以下列出的所有限制条件。
要在本地验证插件，请调用 `<ui-path>代码 | 分析代码 | 按名称运行检查…</ui-path>` 并在所有插件描述文件上运行 `<control>插件开发工具包 | 插件描述符 | Plugin.xml 动态插件验证</control>` 检查。

对于托管在 [JetBrains Marketplace](https://plugins.jetbrains.com) 上的插件，内置的 [插件验证器](https://blog.jetbrains.com/platform/2018/07/plugins-repository-now-integrates-with-the-plugin-verification-tool/) 将自动运行这些检查。
有关如何在本地或 CI 上运行它的更多信息，请参阅 [](verifying_plugin_compatibility.md#plugin-verifier)。

### 不使用组件 {id=no-use-of-components}

不得使用任何组件；现有的组件 [必须迁移](plugin_components.md) 到服务、扩展或监听器中。

### 操作组需要 ID {id=action-group-requires-id}

所有 [`<group>`](plugin_configuration_file.md#idea-plugin__actions__group) 元素必须声明一个唯一的 `id`。

### 仅使用动态扩展 {id=use-only-dynamic-extensions}

无论是在平台本身中定义的（[](intellij_platform_extension_point_list.md)）还是来自其他插件的，所有使用的扩展点都必须明确标记为动态（参见下一段）。

一些已弃用的扩展点（例如 `com.intellij.configurationProducer`）故意不是动态的，应将其用法迁移到相应的替代方案。

### 将扩展点标记为动态 {id=mark-extension-points-as-dynamic}

如果插件定义了其自定义扩展点，则它们必须遵守特定的使用规则，然后 [明确声明](plugin_extension_points.md#dynamic-extension-points) 为动态使用。

### 依赖于扩展点的可配置项 {id=configurables-depending-on-extension-points}

任何依赖于动态扩展点的 [`Configurable`](%gh-ic%/platform/ide-core/src/com/intellij/openapi/options/Configurable.java) 都必须实现 `Configurable.WithEpDependencies`。

### 不使用服务覆盖 {id=no-use-of-service-overrides}

不允许使用 `overrides="true"` 声明的应用、项目和模块 [服务](plugin_services.md)。

## 代码 {id=code}

> 插件的加载和卸载发生在 [EDT](threading_model.md) 中，并且在写操作下进行。
>
{style="note"}

### `CachedValue` {id=cachedvalue}

加载/卸载插件会清除所有使用 [`CachedValuesManager`](%gh-ic%/platform/core-api/src/com/intellij/psi/util/CachedValuesManager.java) 创建的缓存值。

### 不要存储 PSI {id=do-not-store-psi}

不要在可能存活于插件加载或卸载的对象中存储对 PSI 元素的引用；请改用 [`SmartPsiElementPointer`](%gh-ic%/platform/core-api/src/com/intellij/psi/SmartPsiElementPointer.java)。

### 不要使用 `FileType`/`Language` 作为 Map 键 {id=do-not-use-filetypelanguage-as-map-key}

替换为 `Language.getID()`/`FileType.getName()` 返回的 `String`（使用检查 <control>插件开发工具包 | 代码 | Map 键可能泄漏</control>）。

### 插件加载/卸载事件 {id=pluginloadunloadevents}

注册 [`DynamicPluginListener`](%gh-ic%/platform/core-api/src/com/intellij/ide/plugins/DynamicPluginListener.kt) [应用监听器](plugin_listeners.md) 以接收插件加载/卸载事件的更新。

这可以用于以下场景：
- 取消长时间运行的活动或由于正在进行的进程而禁止卸载
- 清除 `UserDataHolder` 对象中的数据（参见 [`InjectedLanguageManagerImpl`](%gh-ic%/platform/analysis-impl/src/com/intellij/psi/impl/source/tree/injected/InjectedLanguageManagerImpl.java) 中的 `clearInjectorCache()` 和 `dropFileCaches()`）
- 以及其他类似情况

### 资源清理 {id=resource-cleanup}

使用实现 [`Disposable`](%gh-ic%/platform/util/src/com/intellij/openapi/Disposable.java) 的 [](plugin_services.md)，并在 `Disposable.dispose()` 中执行清理操作。

## 故障排除 {id=troubleshooting}

当插件被卸载或更新时，IDE 会同步等待插件卸载，如果卸载失败则会要求重启。

如果卸载失败：

1. 尝试使用更新版本的 IDE（最终可以从 [早期访问计划](https://eap.jetbrains.com) 获取最新版本），在某些情况下，平台错误可能是问题的原因。
   请参阅此 [已知平台问题列表](https://youtrack.jetbrains.com/issues/IDEA?q=%23dynamic-plugins%20)，了解与处理动态插件相关的问题。

2. 尝试在全新配置中运行（例如，清理 [沙盒](ide_development_instance.md#the-development-instance-sandbox-directory) 或使用不同的配置目录）。

### 日志记录 {id=logging}

所有事件都在 IDE 日志文件的 `com.intellij.ide.plugins.DynamicPlugins` 类别下跟踪。
如果插件无法重新加载，日志中将包含失败的原因。

### 诊断内存泄漏 {id=diagnosing-leaks}

<procedure title="查找阻止卸载的内存泄漏">

1. 确保 IDE 运行时启用了 VM 参数 `-XX:+UnlockDiagnosticVMOptions`。使用 [Gradle](creating_plugin_project.md) 时，指定 `runIde.jvmArgs += "-XX:+UnlockDiagnosticVMOptions"`，否则请 [配置 JVM 选项](https://www.jetbrains.com/help/idea/tuning-the-ide.html#procedure-jvm-options)。
2. 将注册表键 `ide.plugins.snapshot.on.unload.fail` 设置为 `true`（转到 <ui-path>导航 | 搜索所有内容</ui-path> 并输入 `Registry`）。
3. 触发插件重新加载。
4. 打开插件卸载时在用户主目录中生成的 <path>.hprof</path> 内存快照，查找插件 ID 字符串。[IntelliJ Ultimate](https://www.jetbrains.com/help/idea/analyze-hprof-memory-snapshots.html) 可以直接打开内存快照。
5. 找到引用插件 ID 字符串的 `PluginClassLoader`。
6. 查看对 `PluginClassLoader` 实例的引用。
7. 每一个引用都是需要解决的内存泄漏（或内存泄漏的一部分）。

</procedure>

完成步骤 1 和 2 后，日志中将包含有关内存泄漏的更多信息。
例如，以下内容显示了将类加载器保留在内存中的字段引用链：

```
2020-12-26 14:43:24,563 [ 251086]   INFO - lij.ide.plugins.DynamicPlugins - Snapshot analysis result: Root 1:
  ROOT: Global JNI
  sun.awt.X11.XInputMethod.clientComponentWindow
  com.intellij.openapi.wm.impl.IdeFrameImpl.rootPane
  com.intellij.openapi.wm.impl.IdeRootPane.myToolbar
  com.intellij.openapi.actionSystem.impl.ActionToolbarImpl.myVisibleActions
  java.util.ArrayList.elementData
  java.lang.Object[]
  com.example.ActionExample.<class>
  com.example.ActionExample.<loader>
* com.intellij.ide.plugins.cl.PluginClassLoader
```
