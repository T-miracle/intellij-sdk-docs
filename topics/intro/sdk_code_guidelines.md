<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# SDK 代码示例指南

<link-summary>IntelliJ SDK 代码示例中贡献的代码的编码指南。</link-summary>

本文档描述了用于编写开源 IntelliJ 平台 SDK 代码示例的编码准则。
在开始之前，请彻底阅读本页面，以及 [行为准则](intellij-sdk-docs-original_CODE_OF_CONDUCT.md) 和 [许可](https://github.com/JetBrains/intellij-sdk-docs/blob/main/LICENSE.txt) 文档。

有关贡献到 IntelliJ 平台本身的信息，请访问[贡献给 IntelliJ 平台](platform_contributions.md)。

> 也请参阅 [](code_samples.md) 进行概览。

## 目标

在编写 SDK 代码示例时，请牢记以下考虑因素：
* SDK 示例的目的是演示 IntelliJ 平台的实现模式。
* SDK 代码示例是教学性代码，旨在教授知识。
  * 教学性代码在某些关键方面与生产代码不同：
    * 为简洁和简短而牺牲了一些健壮性。
      只在需要强调实现陷阱时使用错误检查。
    * 保持实现尽可能简单，但使用完整的 IntelliJ 平台、编程语言和库的功能。
    * 为接口、类、字段、方法和扩展点使用有意义的名称。
    * 编写教学性 Javadoc 注释。
  * 代码示例替代了大量文档。
* 目标是创建两个级别的 SDK 示例：
  * _基本_ 示例侧重于通过演示 IntelliJ 平台的有限领域来展示特定主题。
    它应该显示所有组件和架构，但最终要完成一些基本任务。
    例如，通过仅存储一个 `String` 来演示持久性。
  * _高级_ 示例演示了 IntelliJ 平台的不同部分如何集成和协同工作，例如将检查或意向与非平凡的 PsiTree 操作相结合。

最终目标是为开发人员提供在基于 IntelliJ 平台的插件中实现功能的路线图。
每个路线图应包括：
* 指向关于实现功能所需的 IntelliJ 平台 API 的 SDK 文档的指针。
* 指向相关的 _基本_ SDK 示例插件的指针。
* 指向相关的 _高级_ SDK 示例插件的指针。

## 插件版权声明

在由 JetBrains 编写的所有示例插件中使用标准的 intellij-community 版权声明：

```text
Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license.
```

> 版权声明必须出现在每个源文件的顶部。
> 使用 [IntelliJ Platform SDK](https://github.com/JetBrains/intellij-sdk-docs/tree/main/.idea/copyright) 版权配置文件。
>
{style="note"}

## SDK 插件的目录命名约定 {id="sdk插件的目录命名约定"}

对于 _基本_ 示例，插件目录名称是根据演示的 IntelliJ 平台扩展点派生的。
例如，<path>foo_basics</path>，其中 _foo_ 对应于 IntelliJ 平台框架、扩展点或组件。
一些命名示例包括 <path>facet_basics</path> 和 <path>editor_basics</path>。
每个 IntelliJ 平台 API 领域只有一个 _基本_ 示例。

对于 _高级_ 代码示例，名称应反映插件提供的复杂功能，而不是 IntelliJ 平台 API。
高级示例将与示例中演示的 IntelliJ 平台 API 进行交叉引用。

插件根目录名称中允许的唯一符号字符是下划线，例如 <path>foo_basics</path> 或 <path>max_opened_projects</path>。
然而，下划线不应出现在任何其他符号名称中，如 `Artifact ID`、插件 ID、包名称等。
相反，应将长名称连接为驼峰式，如 <path>maxOpenedProjects</path>。

## 组和工件的 ID

在创建基于Gradle的IntelliJ平台插件时，定义了插件的Maven坐标（`Group ID`，`Artifact ID`，`Version`）。

SDK插件的`Group ID`始终为`org.intellij.sdk`。

`Artifact ID`是插件目录名称的简短派生。
`Artifact ID`不应包含符号。

对于_基本_代码示例，不需要在`Artifact ID`中包含“basic”。
例如，`foo_basics`目录名称的`Artifact ID`应为`foo`。

具有较长目录名称的插件，如<path>conditional_operator_intention</path>，可以具有更简洁的`Artifact ID`，如`conditionalOperatorIntention`。
（由于遗留原因，<path>conditional_operator_intention</path>插件使用更简洁的`Artifact ID`。）

## 插件 ID 约定 {id="插件ID约定"}

插件ID出现在<path>[plugin.xml](plugin_configuration_file.md)</path>文件中的[`<id>`](plugin_configuration_file.md#idea-plugin__id)元素之间。

通常情况下，插件ID是`Group ID`与`Artifact ID`连接在一起。
例如，像<path>facet_basics</path>这样的插件的插件ID是`org.intellij.sdk.facet`。

插件ID不包含下划线。

## 插件包名称

插件中的包应以插件ID开头。
如果插件中只有一个包，那么包名称与插件ID相同。
可以添加附加后缀单词，用"."字符分隔，以形成更具体的包名称。
包名称不包含下划线。

## 插件目录结构

SDK示例代码应具有标准的目录结构。
标准化的结构不仅使示例更容易浏览和理解，还基于默认的Gradle插件项目结构。

请注意，插件根目录下的目录不应包含下划线字符，如有必要，应使用驼峰命名。
以下是`foo_basics`插件的示例目录结构：

```text
code_samples/
  foo_basics/
    gradle/
    src/
      main/
        java/
          org.intellij.sdk.foo/
          icons/
            SdkIcons.java     # The standard SDK icon class
        resources/
          icons/
            sdk_16.svg        # The standard SDK icon for menus, etc.
          META-INF/
            plugin.xml        # The plugin configuration file
            pluginIcon.svg    # The standard SDK plugin icon
      test/                   # Omit if there are no tests
        java/
          org.intellij.sdk.foo/
        resources/
    build.gradle.kts
    gradlew
    gradle.bat
    settings.gradle.kts
    README.md
```

## Gradle 构建脚本约定

SDK 代码示例必须[使用 Gradle](creating_plugin_project.md) 开发。
截至目前，SDK 代码示例中的 Gradle 使用仍然严重依赖于<path>plugin.xml</path>来指定插件配置。
在后续的第二阶段，SDK 代码示例将更多地依赖于 Gradle 配置。

Gradle 构建脚本文件的默认内容是由[New Project Wizard](creating_plugin_project.md#create-ide-plugin)生成的。
对于 SDK 代码示例的 Gradle 构建脚本文件，一致的结构对于清晰性至关重要，它是基于项目向导生成的默认结构的。
SDK 代码示例的 Gradle 构建脚本中的注释应仅引起对插件特有的 Gradle 配置部分的注意。

对于SDK代码示例，需要对插件向导生成的默认<path>build.gradle.kts</path>文件进行一些修改：
* 保留Gradle属性`version`（`project.version`）和`group`（`project.group`）。
  请参阅[插件Gradle属性](creating_plugin_project.md#插件Gradle属性和插件配置文件元素)部分，了解这些Gradle属性与<path>plugin.xml</path>中的元素之间的关系。
* 在[Patching DSL](tools_gradle_intellij_plugin.md#tasks-patchpluginxml)（`patchPluginXml {...}`）部分添加以下语句：

  ```kotlin
  // Patches <version> value in plugin.xml
  version.set(project.version)
  sinceBuild.set("221")
  untilBuild.set("223.*")
  ```

## plugin.xml 约定

对于 SDK 代码示例的<path>plugin.xml</path> [配置文件](plugin_configuration_file.md)，一致的结构非常重要，因为我们希望引起对插件的文件中唯一部分的注意。
对于唯一的元素和配置进行详细的注释，对于其他部分进行适度的注释。

SDK代码示例的插件配置文件（plugin.xml）文件中元素的顺序如下：
* [`<id>`](plugin_configuration_file.md#idea-plugin__id) 使用完全合格的[插件ID](#插件ID约定)。
* [`<name>`](plugin_configuration_file.md#idea-plugin__name) 名称值不必与[插件目录名称](#sdk插件的目录命名约定)相匹配。
  名称用于显示目的，应反映插件的功能。
  名称必须以"SDK: "开头。
* [`<version>`](plugin_configuration_file.md#idea-plugin__version) SDK示例的版本，格式为 MAJOR.MINOR.FIX。
  * MAJOR 对应于功能的重大升级。
  * MINOR 对应于小的重构和功能的小改进。
  * FIX 对应于在没有重大重构的情况下修复问题的更改。
* [`<depends>`](plugin_configuration_file.md#idea-plugin__depends) 至少包含一个依赖项，其中包含模块`com.intellij.modules.platform`，以指示插件与基于IntelliJ平台的产品的基本兼容性。
  根据需要添加包含模块FQN的`<depends>`元素，以描述更多专业的[与多个产品的兼容性](plugin_compatibility.md)和任何其他[插件依赖项](plugin_dependencies.md)。
* [`<description>`](plugin_configuration_file.md#idea-plugin__description) 是对正在演示的内容以及用户如何访问功能的简洁解释。
  如果插件默认情况下覆盖了IDE的行为（如<path>tree_structure_provider</path>），必须在描述中进行说明。
* [`<change-notes>`](plugin_configuration_file.md#idea-plugin__change-notes) 是按版本号排序的列表，每个版本都有一段简短的更改说明。
* [`<vendor>`](plugin_configuration_file.md#idea-plugin__vendor) 将值设置为`IntelliJ Platform SDK`。
  设置以下属性：
  * `email` 忽略此属性。
  * `url` 设置为JetBrains Marketplace的URL `"https://plugins.jetbrains.com"`。
* [插件配置元素](plugin_configuration_file.md)的其余部分只有在特定插件需要时才应出现。

## README 文件

在IntelliJ平台SDK中提供的每个代码示例都应包含一个README文件，描述示例的目的和内容。
[`SAMPLE_README.md`](%gh-sdk-samples%/SAMPLE_README.md)文件包含一个模板，应作为进一步编写的初始草稿。

每个<路径>README.md</路径>文件应具有相同的结构，以实现更好的导航和可读性：
- 带有指向主要的IntelliJ SDK文档和示例引用的页面的链接的标题。
- _快速入门_ 部分简要描述示例的目的以及它实现的IntelliJ SDK的重要部分。
- _扩展点_ 部分（如果实现）列出了实现的扩展点，指向实现类的链接以及扩展类的名称。
- _操作_ 部分（如果实现）列出了实现的操作、操作ID，指向实现类的链接以及基本操作类的名称。
- _监听器_ 部分（如果实现）列出了实现的应用程序或项目级监听器、指向实现类的链接以及基本监听器类的名称。
- 文档中出现的每个链接都必须在文件的底部清晰列出，带有明确的链接ID和根据链接上下文的适当前缀（`docs:`、`file:`等）。

## 测试

IntelliJ Platform SDK代码示例应该根据`since-build`版本进行构建和测试。

代码示例应该可以干净地构建，没有警告或错误，并且新的代码示例应该通过所有默认的IntelliJ IDEA代码检查。

测试人员应完成以下检查表。
在此，"IDE"一词指的是插件设计运行的基于IntelliJ平台的IDE：
* 插件应该在IDE中加载。
* 关于插件的正确信息应该显示在<ui-path>设置 | 插件</ui-path>面板中。
* 如果适用，插件UI，如工具窗口、菜单添加等，应该正确显示。
* 应该使用示例文件测试插件的功能。
* 如果适用，插件应该通过单元测试。
