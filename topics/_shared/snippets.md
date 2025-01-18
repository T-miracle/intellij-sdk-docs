<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 片段
{is-library="true"}

<snippet id="missingContent">

> 如果您对上述部分未涵盖的主题感兴趣，请通过下方的 **此页面是否有帮助？** 反馈表格或[其他渠道](getting_help.topic#problems-with-the-guide)让我们知道。
>
> 请在添加主题时具体说明主题和添加原因，并在需要更多详细信息时留下您的电子邮件地址。感谢您的反馈！
>
{title="缺少什么吗？" style="note"}

</snippet>

<snippet id="subscribeNews">

> [订阅 Marketplace 开发者新闻](https://jb.gg/mp-updates) 以接收新闻和公告。  
> 同时关注 X（原 Twitter）上的 [JBPlatform](https://x.com/JBPlatform/)，并访问  
> [JetBrains 平台博客](https://blog.jetbrains.com/platform/) 和  
> [LinkedIn 上的 JetBrains Marketplace](https://www.linkedin.com/showcase/jetbrains-marketplace/)。
>
{title="保持最新状态"}

</snippet>

<snippet id="pluginDevKitAvailability">

_Plugin DevKit_ 插件与 IntelliJ IDEA 捆绑在一起直至 2023.2。

> 在使用 IntelliJ IDEA 2023.3 或更新版本时，必须从 JetBrains Marketplace 安装 _Plugin DevKit_ 插件（[插件主页](https://plugins.jetbrains.com/plugin/22851-plugin-devkit)）。
> 因为它不再与 IDE 捆绑在一起。
>
{title="Plugin DevKit Availability" style="warning"}

</snippet>

<snippet id="jetbrainsIDE_TLDR">

<tldr>

**IDE**: [主页](https://www.jetbrains.com/%productID%), [版本](https://www.jetbrains.com/%productID%/download/other.html)

**插件**: [JetBrains Marketplace](https://plugins.jetbrains.com/%marketplaceProductID%)

</tldr>

</snippet>

<snippet id="apiChangesHeader">

请参阅 [](verifying_plugin_compatibility.md) 了解如何使用插件验证器和 IDE 检查来排查此类问题。

即将发布的版本的 _早期访问计划_ (EAP) 版本可在 [此处](https://eap.jetbrains.com) 获取。

> 标记为 `@Deprecated(forRemoval=true)` 或任何 [`ApiStatus`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) `@Experimental`、`@ScheduledForRemoval` 或 `@Internal` 的 API 变更未在此列出，因为这些变更预计会不兼容。  
> 对于标记为 `@ApiStatus.Internal`/`@IntellijInternalApi` 的 API，请参阅 [](api_internal.md) 获取更多详情和替代方案。
>
{title="未列出的变更" style="note"}

</snippet>

<snippet id="apiChangesJavaVersion">


>
> Java 版本必须根据目标平台版本进行设置。
>
> **2024.2+**: Java 21
>
> **2022.3+**: Java 17（[博客文章](https://blog.jetbrains.com/platform/2022/08/intellij-project-migrates-to-java-17/)）
>
> **2020.3+**: Java 11（[博客文章](https://blog.jetbrains.com/platform/2020/09/intellij-project-migrates-to-java-11/)）
>
{title="IDE 和 Java 版本" style="note"}

</snippet>

<snippet id="gradlePluginVersion">

> Gradle 插件必须根据目标[平台版本](build_number_ranges.md#platformVersions)进行选择。
>
> 2024.2+
> : **需要** [](tools_intellij_platform_gradle_plugin.md)
>
> 2022.3+
> :
> **推荐** [](tools_intellij_platform_gradle_plugin.md)，<br/>  
> **需要** [](tools_gradle_intellij_plugin.md) 版本 1.10.1+（当前：%gradle-intellij-plugin-version%）
>
{style="warning" title="Gradle 插件"}

</snippet>

<snippet id="ep_list_legend">

## 图例 {id=legend}

> 另请参阅 [](explore_api.md) 获取更多信息和策略。

### 监听器

**Topic** 通过 [IntelliJ Platform Explorer](https://jb.gg/ipe) 搜索现有开源 IntelliJ 平台插件实现中的用法。

**Listener** 链接到要实现的相应监听器。

有关如何注册监听器，请参阅 [](plugin_listeners.md)。

### 扩展点 {id=extension-points}

**Extension Point** 通过 [IntelliJ Platform Explorer](https://jb.gg/ipe) 搜索现有开源 IntelliJ 平台插件实现中的用法。

**Implementation** 是相关的扩展点类。

有关如何在插件中声明扩展，请参阅 [](plugin_extensions.md)。

### 标签 {id=tags}

有关 API 状态的概述，请参阅 [](verifying_plugin_compatibility.md)。

| 图标                                                              | 描述                    | 详情                                                                                                                                                                                                                                                     |
|-----------------------------------------------------------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ![Deprecated][deprecated]                                       | 已弃用的 API              | 请参阅代码文档以获取替代方案。                                                                                                                                                                                                                                        |
| ![Removal][removal]                                             | 计划移除的 API             | 请参阅代码文档以获取替代方案。                                                                                                                                                                                                                                        |
| ![Obsolete][obsolete]                                           | 过时的 API               | 不要在新代码中使用。<p>请参阅代码文档以获取替代方案（[](verifying_plugin_compatibility.md#obsolete-api)）。</p>                                                                                                                                                                   |
| ![Experimental API][experimental]&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 实验性 API               | 使用 [`@ApiStatus.Experimental`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) 注解。<p>API 可能会被更改或移除，恕不另行通知。</p>                                                                                                    |
| ![Internal API][internal]                                       | 内部 API                | 使用 [`@ApiStatus.Internal`](%gh-java-annotations%/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) 注解。<p>第三方插件不得使用，请参阅 [](api_internal.md)。</p>                                                                                           |
| ![Project-Level][project-level]                                 | 项目级别<p>扩展点/主题</p>     | <p>可以将 [`Project`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/Project.java) 作为构造函数参数。</p><p>- 扩展点：使用 `area="IDEA_PROJECT"` 声明</p><p>- 监听器：在 [`<projectListeners>`](plugin_configuration_file.md#idea-plugin__projectListeners) 中注册</p> |
| ![Non-Dynamic][non-dynamic]                                     | 非动态<p>扩展点</p>         | 插件的安装/更新需要重启 IDE（[](dynamic_plugins.md)）。                                                                                                                                                                                                              |
| ![DumbAware][dumb-aware]                                        | `DumbAware`<p>扩展点</p> | 使用 [`DumbAware`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/DumbAware.java) 标记的实现将在 [dumb 模式](indexing_and_psi_stubs.md#dumb-mode) 期间处理。                                                                                               |

[deprecated]: https://img.shields.io/badge/-Deprecated-lightgrey?style=flat-square
[removal]: https://img.shields.io/badge/-Removal-red?style=flat-square
[obsolete]: https://img.shields.io/badge/-Obsolete-grey?style=flat-square
[experimental]: https://img.shields.io/badge/-Experimental-violet?style=flat-square
[internal]: https://img.shields.io/badge/-Internal-darkred?style=flat-square
[project-level]: https://img.shields.io/badge/-Project--Level-blue?style=flat-square
[non-dynamic]: https://img.shields.io/badge/-Non--Dynamic-orange?style=flat-square
[dumb-aware]: https://img.shields.io/badge/-DumbAware-darkgreen?style=flat-square

</snippet>
