<!-- Copyright 2000-2024 JetBrains s.r.o. and other contributors. Use of this source code is governed by the Apache 2.0 license that can be found in the LICENSE file. -->

# Alternatives to Implementing a Plugin

<link-summary>Alternative strategies and tools to avoid building a "full" plugin.</link-summary>

在某些情况下，实现实际的 IntelliJ 平台插件可能有些过大，使用下面列出的替代方法之一可能会在更短的时间内为您提供所需的值。
如果您需要特定于您的项目域、约定或实践的功能，您可以避免实现和发布插件所需的所有步骤，并将这些功能作为项目或 IDE 配置文件的一部分提供。

在开始 IntelliJ 平台插件开发之前，请定义您的需求并验证它们是否可以通过下面描述的任何替代方案来满足。
仅当所描述的解决方案不足以满足您的情况并且有大量开发人员可以从中受益时，才考虑实现实际的插件。

## 结构搜索和替换检查 { id="结构搜索和替换检查" }

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

[IDE 脚本控制台](https://www.jetbrains.com/help/idea/ide-scripting-console.html) 可用于自动化 IDE 的功能并提取所需的信息，例如有关当前项目的信息。
脚本可以访问 IntelliJ 平台 API，并且默认可以用 Kotlin、JavaScript 或 Groovy 实现，但也可以使用符合 [JCR-223 规范](https://www.jcp.org/en/jsr/detail?id=223)。

创建的脚本存储在[IDE 配置目录](https://www.jetbrains.com/help/idea/directories-used-by-the-ide-to-store-settings-caches-plugins-and-logs.html#config-directory) 并且不能作为项目文件或配置的一部分进行共享。

## Flora 插件

[Flora](https://plugins.jetbrains.com/plugin/17669-flora-beta-) 插件允许将项目特定的扩展开发为 Kotlin 脚本 (<path>\*.kts</path>) 或 JavaScript (<path>\*.js</path>) 文件。
Flora 扩展可以访问所有可用的 IntelliJ 平台 API，就像常规插件一样。

每个扩展都由单个文件表示，并直接存储在项目的 <path>.plugins</path> 目录中。
通过将 <path>.plugins</path> 目录添加到 VCS，可以轻松地与其他团队成员共享扩展。
另外，在 <ui-path>Settings | Build, Execution, Deployment | Required Plugins</ui-path> 中添加Flora插件并将此配置作为项目的一部分共享，可以轻松地向您的团队提供额外的 IDE 功能，而无需任何手动设置。

> Please note that the Flora plugin is in an experimental state.

## LivePlugin

[实时插件](https://plugins.jetbrains.com/plugin/7282-liveplugin) 允许在运行时扩展基于 IntelliJ 的 IDE 功能，而无需重新启动 IDE。
它添加了一个新的 <control>Plugins</control> 工具窗口，其中列出了所有可用的扩展并允许你管理它们。
扩展可以在 Kotlin 或 Groovy 中实现，并直接在 IDE 中编辑。
扩展可以使用所有 IntelliJ 平台 API 和其他 实时插件 API，从而缩短常见用例。

创建的扩展存储在 IDE 层，并且可以作为纯文件、GitHub 要点或存储库与其他团队成员共享。
此外，如果它们存储在项目的 <path>.live-plugins</path> 目录中，并且启用了 LivePlugin 的 <control>Run Project Specific Plugins（运行项目特定插件）</control> 选项，该目录中的所有扩展将在项目打开时自动加载，并在项目关闭时卸载。

> 请参阅 实时插件 [说明](https://dmitrykandalov.com/liveplugin)、[演示](https://www.youtube.com/watch?v=GcYa4lMRta0) 和 [扩展示例](https://github.com/dkandalov/live-plugin#more-examples) 了解更多信息。
>
{style="note"}

## PhpStorm 高级元数据（advanced metadata）

[PhpStorm](https://www.jetbrains.com/phpstorm/) 支持特殊的[元数据文件](https://www.jetbrains.com/help/phpstorm/ide-advanced-metadata.html) 描述方法和功能。
此信息用于使用现有的 IDE 功能，例如代码完成、导航、查找用法等。
元数据文件可以是
[项目文件的一部分](https://www.jetbrains.com/help/phpstorm/ide-advanced-metadata.html#create-metadata-files-inside-your-project)，
这使得通过版本控制轻松在团队成员之间共享。
