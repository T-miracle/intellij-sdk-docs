<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 片段
{is-library="true"}

<snippet id="missingContent">

> 如果您对上述部分未涵盖的主题感兴趣，请通过下方的**此页面是否有帮助？**反馈表格或[其他渠道](getting_help.topic#problems-with-the-guide)让我们知道。
>
> 请在添加主题时具体说明主题和添加原因，并在需要更多详细信息时留下您的电子邮件地址。感谢您的反馈！
>
{title="Something missing?" style="note"}

</snippet>

<snippet id="subscribeNews">

> [Subscribe to Marketplace Developer News](https://jb.gg/mp-updates) to receive news and announcements.
> Also follow [JBPlatform](https://twitter.com/JBPlatform/) on Twitter and visit [JetBrains Platform Blog](https://blog.jetbrains.com/platform/).
>
{title="Staying up to date"}

</snippet>

<snippet id="pluginDevKitAvailability">

_Plugin DevKit_ 插件与 IntelliJ IDEA 捆绑在一起直至 2023.2。

> 在使用 IntelliJ IDEA 2023.3 或更新版本时，必须从 JetBrains Marketplace 安装 _Plugin DevKit_ 插件（[插件主页](https://plugins.jetbrains.com/plugin/22851-plugin-devkit)）。
> 因为它不再与 IDE 捆绑在一起。
>
{title="Plugin DevKit Availability" style="warning"}

</snippet>

<snippet id="jetbrainsProductOpenSourceLicense">

> 合格的开源项目可以[申请 JetBrains 产品的免费许可证](https://www.jetbrains.com/community/opensource/)。

</snippet>

<snippet id="jetbrainsIDE_TLDR">

<tldr>

**IDE**: [Homepage](https://www.jetbrains.com/%productID%), [Versions](https://www.jetbrains.com/%productID%/download/other.html)

**Plugins**: [JetBrains Marketplace](https://plugins.jetbrains.com/%marketplaceProductID%)

</tldr>

</snippet>

<snippet id="apiChangesHeader">

Please see [](verifying_plugin_compatibility.md) on how to use Plugin Verifier and IDE inspections to check such problems.

_Early Access Program_ (EAP) releases of upcoming versions are available [here](https://eap.jetbrains.com).

> Changes from API marked with `@Deprecated(forRemoval=true)` or any of [`org.jetbrains.annotations.ApiStatus`](https://github.com/JetBrains/java-annotations/blob/master/common/src/main/java/org/jetbrains/annotations/ApiStatus.java) `@Experimental`, `@ScheduledForRemoval`, or `@Internal` are not listed here, as incompatible changes are to be expected.
>
> For API annotated with `ApiStatus.@Internal`/`@IntellijInternalApi`, see [](api_internal.md) for more details and replacements.
>
{title="Non-listed changes" style="note"}

</snippet>

<snippet id="apiChangesJavaVersion">

> 仅当面向 2022.2 及更高版本时，需要 Java 17（[博客文章](https://blog.jetbrains.com/platform/2022/08/intellij-project-migrates-to-java-17/)）。
>
> 仅当面向 2020.3 及更高版本时，才需要 Java 11（[博客文章](https://blog.jetbrains.com/platform/2020/09/intellij-project-migrates-to-java-11/)）。
>
> 请确保始终将 `gradle-intellij-plugin` 升级到最新版本 [%gradle-intellij-plugin-version%](https://github.com/jetbrains/gradle-intellij-plugin/releases)
>
{style="note"}

</snippet>

<snippet id="ep_list_legend">

## Legend

> See also [](explore_api.md) for more information and strategies.

### Listeners

**Topic** searches for usages inside existing implementations of open-source IntelliJ Platform plugins via [IntelliJ Platform Explorer](https://jb.gg/ipe).

**Listener** links corresponding listener to implement.

See [](plugin_listeners.md) on how to register listeners.

### Extension Points

**Extension Point** searches for usages inside existing implementations of open-source IntelliJ Platform plugins via [IntelliJ Platform Explorer](https://jb.gg/ipe).

**Implementation** is the related Extension Point class.

See [](plugin_extensions.md) on how to declare extensions in your plugin.

### Tags

See [](verifying_plugin_compatibility.md) for overview of API status.

| Icon                              | Description                         | Details                                                                                                                                                                                                                                                                                                   |
|-----------------------------------|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ![Deprecated][deprecated]         | Deprecated API                      | Please see code documentation for replacement                                                                                                                                                                                                                                                             |
| ![Removal][removal]               | Scheduled for Removal API           | Please see code documentation for replacement                                                                                                                                                                                                                                                             |
| ![Obsolete][obsolete]             | Obsolete API                        | Do not use in new code, please see code documentation for replacement ([](verifying_plugin_compatibility.md#obsolete-api))                                                                                                                                                                                |
| ![Experimental API][experimental] | Experimental API                    | Annotated with [`@ApiStatus.Experimental`](https://github.com/JetBrains/java-annotations/blob/master/common/src/main/java/org/jetbrains/annotations/ApiStatus.java), API might be altered or removed without prior notice                                                                                 |
| ![Internal API][internal]         | Internal API                        | Annotated with [`@ApiStatus.Internal`](https://github.com/JetBrains/java-annotations/blob/master/common/src/main/java/org/jetbrains/annotations/ApiStatus.java), must not be used by 3rd party, see [](api_internal.md)                                                                                   |
| ![Project-Level][project-level]   | Project-Level Extension Point/Topic | <p>Can have [`Project`](%gh-ic%/platform/core-api/src/com/intellij/openapi/project/Project.java) as constructor parameter</p><p>Extension Point: Declared with `area="IDEA_PROJECT"`</p><p>Listener: registered in [`<projectListeners>`](plugin_configuration_file.md#idea-plugin__projectListeners)</p> |
| ![Non-Dynamic][non-dynamic]       | Non-Dynamic Extension Point         | Installation/update of plugin requires IDE restart ([Dynamic Plugins](dynamic_plugins.md))                                                                                                                                                                                                                |

[deprecated]: https://img.shields.io/badge/-Deprecated-lightgrey?style=flat-square
[removal]: https://img.shields.io/badge/-Removal-red?style=flat-square
[obsolete]: https://img.shields.io/badge/-Obsolete-grey?style=flat-square
[experimental]: https://img.shields.io/badge/-Experimental-violet?style=flat-square
[internal]: https://img.shields.io/badge/-Internal-darkred?style=flat-square
[project-level]: https://img.shields.io/badge/-Project--Level-blue?style=flat-square
[non-dynamic]: https://img.shields.io/badge/-Non--Dynamic-orange?style=flat-square

</snippet>
