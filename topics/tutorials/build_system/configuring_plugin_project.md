<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 配置 Gradle IntelliJ Plugin

<link-summary>配置 Gradle IntelliJ 插件的基本属性和任务。</link-summary>

本节将引导你了解 Gradle 插件属性，以实现常见的功能需求。  
欲了解更多高级选项，请参阅完整的 [](tools_gradle_intellij_plugin.md) 参考。

<include from="creating_plugin_project.md" element-id="gradle1xOnly"/>

<include from="snippets.md" element-id="gradlePluginVersion"/>

## 保持最新 {id=keep-up-to-date}

Gradle IntelliJ 插件和 [Gradle](https://gradle.org/install/) 构建系统在不断开发，每个新版本都会带来重要的错误修复、新功能和改进，从而提高开发效率。
强烈建议将 Gradle 和 Gradle IntelliJ 插件都升级到最新版本。
较新的 IDE 发布版本在较旧的 Gradle IntelliJ 插件版本中可能不会得到完全支持。

> 当前 Gradle IntelliJ 插件版本为 %gradle-intellij-plugin-version%
{style="note"}

## 目标平台和依赖项

<snippet id="whichPlatformVersion">

> 您的插件应该支持哪些版本？我们根据 [统计：使用中的产品版本](https://plugins.jetbrains.com/docs/marketplace/product-versions-in-use-statistics.html) 中的下载统计数据收集了一些见解。
>

</snippet>

默认情况下，Gradle 插件将针对由 IntelliJ IDEA 社区版的最新 EAP 快照定义的 IntelliJ 平台构建一个插件项目。

如果本地计算机上没有匹配的指定 IntelliJ 平台版本，Gradle 插件会下载正确版本和类型的平台。
然后，IntelliJ IDEA 会对构建以及任何相关的源代码和 JetBrains Java Runtime 进行索引。

要为多个目标平台版本构建插件，请参阅 [](build_number_ranges.md#multipleIDEVersions) 了解重要说明。

### IntelliJ 平台配置 {id="intellij平台配置"}

显式设置 [`intellij.version`](tools_gradle_intellij_plugin.md#intellij-extension-version) 和 [`intellij.type`](tools_gradle_intellij_plugin.md#intellij-extension-type) 属性会告诉 Gradle 插件使用该 IntelliJ 平台的配置来创建插件项目。

> 请查看 [开发多种产品](dev_alternate_products.md) 页面，了解如何开发与多个基于 IntelliJ 的 IDE 兼容的插件的信息。
>

所有可用的平台版本可以在 [](intellij_artifacts.md) 中查看。

如果存储库中没有所选的平台版本，或者需要使用目标IDE的所需类型和版本的 IntelliJ 平台的本地安装，请使用 [`intellij.localPath`](tools_gradle_intellij_plugin.md#intellij-extension-localpath) 来指向该安装路径。
如果设置了 `intellij.localPath` 属性，请不要设置 `intellij.version` 和 `intellij.type` 属性，因为这可能导致未定义的行为。

### 插件依赖项 {id="插件依赖项"}

IntelliJ 平台插件项目可以依赖于捆绑的或第三方的插件。
在这种情况下，项目应该针对与用于构建插件项目的 IntelliJ 平台版本相匹配的那些插件版本构建。
Gradle 插件将获取由 [`intellij.plugins`](tools_gradle_intellij_plugin.md#intellij-extension-plugins) 定义的插件列表中的任何插件。
有关指定插件和版本的信息，请参阅 Gradle 插件 [IntelliJ 扩展](tools_gradle_intellij_plugin.md#configuration-intellij-extension)。

请注意，此属性描述了依赖项，以便 Gradle 插件可以获取所需的工件。  
运行时依赖项必须按照 [插件依赖项](plugin_dependencies.md#dependency-declaration-in-pluginxml) 中的描述添加到 [插件配置](plugin_configuration_file.md)（<path>plugin.xml</path>）文件中。

## 运行 IDE 任务

默认情况下，Gradle 插件将使用与用于构建插件的 IntelliJ 平台版本相同的版本来启动 IDE 开发实例。
同时，默认情况下，也会使用相应的 JJetBrains Runtime，因此对于这种用例，无需进一步配置。

### 运行不同版本和类型的基于 IntelliJ 平台的 IDE

用于 [开发实例](ide_development_instance.md) 的 IntelliJ 平台 IDE 可能与用于构建插件项目的 IDE 不同。
设置 [`runIde.ideDir`](tools_gradle_intellij_plugin.md#tasks-runide-idedir) 属性将定义用于开发实例的 IDE。
在 [基于 IntelliJ 平台的替代 IDE](intellij_platform.md#基于IntelliJ平台的IDE) 中运行或调试插件时，通常会使用此属性。

### 运行在不同版本的 JetBrains Runtime 上

每个 IntelliJ 平台版本都有对应的 [JetBrains Runtime](ide_development_instance.md#using-a-jetbrains-runtime-for-the-development-instance) 版本。
可以通过指定 [`runIde.jbrVersion`](tools_gradle_intellij_plugin.md#tasks-runide-jbrversion) 属性来使用不同版本的 Runtime，该属性描述了 IDE 开发实例应该使用的 JetBrains Runtime 版本。
Gradle 插件将根据需要获取指定的 JetBrains Runtime 版本。

## 修改插件配置文件 {id=patching-the-plugin-configuration-file}

插件项目的 `<path>plugin.xml</path>` 文件的元素值在构建时会从 [`patchPluginXml`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml) 任务的属性中进行"patch"（修补）操作。
Patching DSL 中尽可能多的属性将被替换到插件项目的 `<path>plugin.xml</path>` 文件内相应的元素值中：
* 如果定义了`patchPluginXml`属性的默认值，该属性的值将被在`<path>plugin.xml</path>`文件中进行替换，_无论`patchPluginXml`任务是否出现在Gradle构建脚本中_。
  * 例如，[`patchPluginXml.sinceBuild`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-sincebuild) 和 [`patchPluginXml.untilBuild`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-untilbuild) 属性的默认值是基于声明的（或默认的）[`intellij.version`](tools_gradle_intellij_plugin.md#intellij-extension-version) 值来定义的。
    因此，默认情况下，`patchPluginXml.sinceBuild` 和 `patchPluginXml.untilBuild` 将被替换到 `<path>plugin.xml</path>` 文件中的 [`<idea-version>`](plugin_configuration_file.md#idea-plugin__idea-version) 元素的 `since-build` 和 `until-build` 属性中。
* 如果明确定义了 [`patchPluginXml`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml) 任务的属性值，那么该属性值将被替换到 `<path>plugin.xml</path>` 文件中。
  * 如果同时明确设置了 `patchPluginXml.sinceBuild` 和 `patchPluginXml.untilBuild` 属性，它们都将被替换到 `<path>plugin.xml</path>` 文件中。
  * 如果一个属性被明确设置（例如 `patchPluginXml.sinceBuild`），而另一个属性没有被明确设置（例如 `patchPluginXml.untilBuild` 有默认值），那么两个属性都将以各自的（明确和默认）值进行替换。
* 如果希望对 `<idea-version>` 元素的 `since-build` 和 `until-build` 属性**不进行替换**，请将 [`intellij.updateSinceUntilBuild`](tools_gradle_intellij_plugin.md#intellij-extension-updatesinceuntilbuild) 设置为 `false`，并且不提供 `patchPluginXml.sinceBuild` 和 `patchPluginXml.untilBuild` 的值。

避免混淆的最佳实践是用注释替换 Gradle 插件将要修补的 `<path>plugin.xml</path>` 中的元素。
这样，这些参数的值就不会在源代码的两个地方出现。
Gradle 插件会在修补过程中添加必要的元素。
对于那些包含描述信息的 [`patchPluginXml`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml) 属性，例如 [`patchPluginXml.changeNotes`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-changenotes) 和 [`patchPluginXml.pluginDescription`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-plugindescription)，在使用 HTML 元素时不需要使用 `CDATA` 块。

> 为了维护并生成最新的变更日志，您可以尝试使用 [Gradle Changelog Plugin](https://github.com/JetBrains/gradle-changelog-plugin)。这个插件可以帮助您有效地管理和生成变更日志，以便跟踪项目的演化和版本历史。
>

如在 [](creating_plugin_project.md#creating-a-plugin-with-new-project-wizard) 中所讨论的，Gradle 属性 `project.version`、`project.group` 和 `rootProject.name` 都是基于向导输入生成的。
然而，在 [Gradle IntelliJ 插件文档](tools_gradle_intellij_plugin.md) 中，并没有在 `<path>plugin.xml</path>` 文件中将这些 Gradle 属性合并并替代成默认的 [`<id>`](plugin_configuration_file.md#idea-plugin__id) 和 [`<name>`](plugin_configuration_file.md#idea-plugin__name) 元素。

最佳实践是保持 `project.version` 始终保持最新。
默认情况下，如果您在 Gradle 构建脚本中修改了 `project.version`，Gradle 插件会自动更新 `<path>plugin.xml</path>` 文件中的 [`<version>`](plugin_configuration_file.md#idea-plugin__version) 值。
这种做法确保了所有版本声明的同步性。

## 验证插件

Gradle 插件提供了一些任务，允许运行完整性和兼容性测试：
* [`verifyPluginConfiguration`](tools_gradle_intellij_plugin.md#tasks-verifypluginconfiguration) - 验证在插件项目中配置的 SDK、目标平台、API 等版本是否正确，
* [`verifyPlugin`](tools_gradle_intellij_plugin.md#tasks-verifyplugin) - 验证 `<path>plugin.xml</path>` 描述文件的完整性和内容，以及插件的存档结构，
* [`runPluginVerifier`](tools_gradle_intellij_plugin.md#tasks-runpluginverifier) - 运行 [IntelliJ 插件验证器](https://github.com/JetBrains/intellij-plugin-verifier) 工具，检查与指定的 IntelliJ IDE 版本的二进制兼容性。

插件验证器集成任务允许配置要检查您的插件的确切 IDE 版本。
更多信息请参阅 [插件兼容性验证](verifying_plugin_compatibility.md#plugin-verifier)。

## 发布插件

在使用 [`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) 任务之前，请查阅 [发布插件文档](publishing_plugin.md) 页面。
该文档解释了在不暴露账户凭据的情况下，使用 Gradle 进行插件上传的不同方法。
