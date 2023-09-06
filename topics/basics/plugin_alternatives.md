# 实现插件的替代方案

<!-- Copyright 2000-2023 JetBrains s.r.o. and other contributors. Use of this source code is governed by the Apache 2.0 license that can be found in the LICENSE file. -->

<link-summary>避免构建“完整”插件的替代策略和工具。</link-summary>

在某些情况下，实现实际的 IntelliJ 平台插件可能有些过大，使用下面列出的替代方法之一可能会在更短的时间内为您提供所需的值。
如果您需要特定于您的项目域、约定或实践的功能，您可以避免实现和发布插件所需的所有步骤，并将这些功能作为项目或 IDE 配置文件的一部分提供。

在开始 IntelliJ 平台插件开发之前，请定义您的需求并验证它们是否可以通过下面描述的任何替代方案来满足。
仅当所描述的解决方案不足以满足您的情况并且有大量开发人员可以从中受益时，才考虑实现实际的插件。

## 结构搜索和替换检查

[结构搜索和替换 (SSR)](https://www.jetbrains.com/help/idea/structural-search-and-replace.html) 功能允许定义搜索模式，
该模式不仅基于文本信息，还基于搜索到的代码片段的结构，无论其格式或注释是怎么样的。
SSR 模板可以用于[创建自定义检查](https://www.jetbrains.com/help/idea/creating-custom-inspections.html)，它可以作为可编程的[代码检查](code_inspections.md)的替代品。
根据要求，检查可以报告与给定模板匹配的代码片段的问题，而且还提供快速修复，以配置的替换模板替换报告的片段。
所有检查元数据（例如名称、问题工具提示和描述）都是可配置的。
一次检查可以使用多个搜索和替换模板。

创建并配置 SSR 检查后，可以通过 [检查配置文件](https://www.jetbrains.com/help/idea/customizing-profiles.html) 与其他团队成员共享它们。

只能为提供 SSR 支持的语言创建 SSR 检查。
验证给定语言是否支持 SSR, 在支持该语言的 IDE 中调用 <ui-path>Edit（编辑） | Find（查找） | Search Structurally...（结构搜索）</ui-path> 操作, 并检查它是否存在于 <control>Language</control> 选择列表中。

> 有关实用的 SSR 模板示例，请参阅 [I(J)nspector](https://ijnspector.wordpress.com/) 博客。
>
{style="note"}

## IDE 脚本控制台

The [IDE scripting console](https://www.jetbrains.com/help/idea/ide-scripting-console.html) can be used for automating IDE's features and extracting required information, e.g., about a current project.
Scripts can access the IntelliJ Platform APIs and can be implemented in Kotlin, JavaScript, or Groovy by default, but it is also possible to use other languages compliant with the [JCR-223 specification](https://www.jcp.org/en/jsr/detail?id=223).

Created scripts are stored in the [IDE configuration directory](https://www.jetbrains.com/help/idea/directories-used-by-the-ide-to-store-settings-caches-plugins-and-logs.html#config-directory) and can't be shared as part of project files or configuration.

## Flora Plugin

The [Flora](https://plugins.jetbrains.com/plugin/17669-flora-beta-) plugin allows for developing project-specific extensions as Kotlin Script (<path>\*.kts</path>) or JavaScript (<path>\*.js</path>) files.
Flora extensions have access to all available IntelliJ Platform APIs, just like a regular plugin.

Every extension is represented by a single file and stored directly in a project's <path>.plugins</path> directory.
Extensions can be easily shared with other team members by adding the <path>.plugins</path> directory to VCS.
Also, adding the Flora plugin in the <ui-path>Settings | Build, Execution, Deployment | Required Plugins</ui-path> and sharing this configuration as part of a project makes it effortless to deliver additional IDE functionalities to your team without any manual setup.

## LivePlugin

The [LivePlugin](https://plugins.jetbrains.com/plugin/7282-liveplugin) allows for extending IntelliJ-based IDEs functionalities at the runtime, without the need of restarting IDE.
It adds a new <control>Plugins</control> tool window that lists all available extensions and allows managing them.
Extensions can be implemented in Kotlin or Groovy and edited directly in the IDE.
Extensions can use all IntelliJ Platform APIs and additional LivePlugin API that shorten common use cases.

Created extensions are stored on the IDE level and can be shared with other team members as plain files, GitHub gists, or repositories.
Additionally, if they are stored in a project's <path>.live-plugins</path> directory and LivePlugin's <control>Run Project Specific Plugins</control> option is enabled, all extensions from this directory will be loaded automatically when the project is opened and unloaded when the project is closed.

> See the LivePlugin [description](https://dmitrykandalov.com/liveplugin), [presentation](https://www.youtube.com/watch?v=GcYa4lMRta0), and [extensions examples](https://github.com/dkandalov/live-plugin#more-examples) for more information.
>
{style="note"}

## PhpStorm Advanced Metadata

[PhpStorm](https://www.jetbrains.com/phpstorm/) supports special [metadata files](https://www.jetbrains.com/help/phpstorm/ide-advanced-metadata.html) describing the behavior of methods and functions.
This information is used for using the existing IDE features such as code completion, navigation, finding usages, and others.
The metadata files can be [part of project files](https://www.jetbrains.com/help/phpstorm/ide-advanced-metadata.html#create-metadata-files-inside-your-project), which makes it easy to share it between team members via version control.
