<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 创建插件 Gradle 项目

<link-summary>创建并运行基于 Gradle 的 IntelliJ 平台插件项目。</link-summary>

这个文档页面描述了一个基于gradle的插件项目，它是用[新建项目向导](https://www.jetbrains.com/help/idea/new-project-wizard.html)生成的，
但如果是用[](plugin_github_template.md)生成的项目将会涵盖了所有描述的文件和目录。

## 使用新建项目向导创建插件

<procedure title="创建IDE插件" id="create-ide-plugin">

通过 <ui-path>File（文件） | New（新建） | Project...（项目）</ui-path> 操作启动 <control>New Project（新项目）</control> 向导，并进行以下操作步骤：
1. 从左侧列表中选择 <control>IDE Plugin</control> 生成器类型。
2. 指定项目 <control>Name（名称）</control> 和 <control>Location（位置）</control>。
3. 在项目 <control>Type（类型）</control> 中选择 <control>Plugin（插件）</control> 选项。
4. _仅在 2023.1 之前的 IntelliJ IDEA 中：_

   选择将用于实现插件的<control>Language（语言）</control>。
   对于本示例，选择 <control>Kotlin</control> 选项。
   另请参阅 [面向插件开发者的 Kotlin](using_kotlin.md) 了解更多信息。

   > 使用 IntelliJ IDEA 2023.1 或更新版本生成的项目，既支持Kotlin源代码，也支持Java源代码。
   > 项目生成器会自动创建 <path>$PLUGIN_DIR$/src/main/kotlin</path> 源目录。
   > 如果要添加 Java 源代码，请创建 <path>$PLUGIN_DIR$/src/main/java</path> 目录。
   >
   {style="note"}

5. 提供一个 <control>Group（组织）</control> 名称，它通常是一个公司的反向域名（例如 `com.example.mycompany`）。
   它用于项目 Gradle 构建脚本中 Gradle 属性的 `project.group` 值。
6. 提供一个 <control>Artifact（工件）</control> 名称，它是构建项目工件的默认名称（没有版本）。
   它还用于项目的 <path>settings.gradle.kts</path> 文件中的 Gradle 属性 `rootProject.name` 值。
   对于本示例，输入 `my_plugin`。
7. 选择 <control>JDK</control> 11。
   该 JDK 将是用于运行 Gradle 的默认 JRE，以及用于编译插件源的 JDK 版本。

    <include from="snippets.md" element-id="apiChangesJavaVersion"/>

8. 提供所有信息后，单击 <control>Create（创建）</control> 按钮生成项目。

</procedure>

### 使用向导生成的 Gradle IntelliJ 平台插件的组件 { id="使用向导生成的Gradle-IntelliJ平台插件的组件" }

对于使用上述步骤创建的示例 `my_plugin`，_IDE Plugin_ 生成器会创建以下目录内容：

```text
my_plugin
├── .run
│   └── Run IDE with Plugin.run.xml
├── gradle
│   └── wrapper
│       ├── gradle-wrapper.jar
│       └── gradle-wrapper.properties
├── src
│   └── main
│       ├── kotlin
│       └── resources
│           └── META-INF
│               ├── plugin.xml
│               └── pluginIcon.svg
├── .gitignore
├── build.gradle.kts
├── gradle.properties
├── gradlew
├── gradlew.bat
└── settings.gradle.kts
```

* 默认的 IntelliJ 平台 <path>build.gradle.kts</path> 文件（请参阅下一段）。
* <path>gradle.properties</path> 文件，包含 Gradle 构建脚本使用的属性。
* <path>settings.gradle.kts</path> 文件，包含 `rootProject.name` 和所需存储库的定义。
* Gradle Wrapper 文件，特别是 <path>gradle-wrapper.properties</path> 文件，它指定用于构建插件的 Gradle 版本。
  如果需要，IntelliJ IDEA Gradle 插件会下载此文件中指定的 Gradle 版本。
* 默认 `main` [源集合](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_project_layout) 下的 <path>META-INF</path> 目录
  包含插件 [configuration文件](plugin_configuration_file.md) 和 [插件图标](plugin_icon_file.md)。
* _Run Plugin_ [运行配置](https://www.jetbrains.com/help/idea/run-debug-configuration.html)。

生成的 `my_plugin` 项目 <path>build.gradle.kts</path> 文件：

```kotlin
plugins {
  id("java")
  id("org.jetbrains.kotlin.jvm") version "1.9.10"
  id("org.jetbrains.intellij") version "1.16.0"
}

group = "com.example"
version = "1.0-SNAPSHOT"

repositories {
  mavenCentral()
}

// 配置 Gradle IntelliJ 插件
// 阅读更多：https://blog.namichong.com/translation-docs/intellij-platform-sdk/tools-gradle-intellij-plugin.html
intellij {
  version.set("2022.2.5")
  type.set("IC") // 目标 IDE 平台

  plugins.set(listOf(/* 插件依赖 */))
}

tasks {
  // 设置 JVM 兼容版本
  withType<JavaCompile> {
    sourceCompatibility = "17"
    targetCompatibility = "17"
  }
  withType<org.jetbrains.kotlin.gradle.tasks.KotlinCompile> {
    kotlinOptions.jvmTarget = "17"
  }

  patchPluginXml {
    sinceBuild.set("222")
    untilBuild.set("232.*")
  }

  signPlugin {
    certificateChain.set(System.getenv("CERTIFICATE_CHAIN"))
    privateKey.set(System.getenv("PRIVATE_KEY"))
    password.set(System.getenv("PRIVATE_KEY_PASSWORD"))
  }

  publishPlugin {
    token.set(System.getenv("PUBLISH_TOKEN"))
  }
}
```

* 显式声明了三个 Gradle 插件：
  * [Gradle Java](https://docs.gradle.org/current/userguide/java_plugin.html) 插件 (`java`)。
  * [Kotlin Gradle](https://kotlinlang.org/docs/gradle-configure-project.html#apply-the-plugin) 插件 (`org.jetbrains.kotlin.jvm`)。
  * [](tools_gradle_intellij_plugin.md) (`org.jetbrains.intellij`)。
* [新建项目](#create-ide-plugin) 向导中的 <control>Group</control> 是 `project.group` 值。
* 注入 `sourceCompatibility` 行以强制使用 Java 11 JDK 编译 Java 源代码。
* [`intellij.version`](tools_gradle_intellij_plugin.md#intellij-extension-version) 和
  [`intellij.type`](tools_gradle_intellij_plugin.md#intellij-extension-type) 属性的值指定 IntelliJ 的版本和类型，它们用于构建插件的平台。
* [插件依赖项](tools_gradle_intellij_plugin.md#intellij-extension-plugins) 的空占位符列表。
* [`patchPluginXml.sinceBuild`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-sincebuild)
  和 [`patchPluginXml.untilBuild`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-untilbuild) 属性的值指定的最小和最大版本IDE 构建插件兼容。
* 初始 [`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) 和 [`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) 任务配置。
  阅读 [](publishing_plugin.md#使用Gradle发布插件) 章节查看更多详细信息。

> 可以考虑使用 [IntelliJ 平台插件模板](https://github.com/JetBrains/intellij-platform-plugin-template)，它还提供了 GitHub Actions 涵盖的 CI 设置。

#### 插件 Gradle 属性 和 插件配置文件元素 { id="插件Gradle属性和插件配置文件元素" }

Gradle 的属性 `rootProject.name` 和 `project.group` 通常不会与 [插件配置文件](plugin_configuration_file.md) <path>plugin.xml</path> 元素的 [`<name>`](plugin_configuration_file.md#idea-plugin__name) 和 [`<id>`](plugin_configuration_file.md#idea-plugin__id) 相匹配。
它们没有理由匹配，因为它们具有不同的功能，与IntelliJ平台无关。

`<name>`元素（用作插件的显示名称）通常与`rootProject.name`相同，但可以更具解释性。

`<id>`值必须是所有插件中的唯一标识符，通常是指定的`<control>Group</control>`和`<control>Artifact</control>`的串联。
请注意，发布的插件无法更改`<id>`，否则会失去对现有安装的自动更新支持。

## 使用 `runIde` Gradle 任务运行插件 { id="使用runIde-Gradle任务运行插件" }

Gradle项目是从IDE的Gradle工具窗口中运行的。

### 向项目添加代码

在运行 [`my_plugin`](#使用向导生成的Gradle-IntelliJ平台插件的组件)之前，可以添加一些代码以提供简单的功能。
查看 [创建操作](working_with_custom_actions.md) 教程，以获取逐步添加菜单操作的说明。

### 执行插件 { id="执行插件" }

_IDE Plugin（IDE 插件）_ 生成器会自动创建一个 _Run Plugin（运行插件）_ 运行配置，可以通过 <ui-path>Run （运行）| Run（运行）...</ui-path> 操作执行，或者在 <control>Gradle</control> 工具窗口的 <control>Run Configurations（运行配置）</control> 节点下找到。

要直接执行 Gradle `runIde` 任务，请打开 <control>Gradle</control> 工具窗口，在 <control>Tasks</control> 节点下搜索 <control>runIde</control> 任务。
如果它不在列表中，请点击 Gradle 工具顶部的 [工具栏](https://www.jetbrains.com/help/idea/jetgradle-tool-window.html#gradle_toolbar) 上的重新导入按钮。
当 <control>runIde</control> 任务可见时，双击它来执行。

要在 _独立的_ IDE 实例中调试您的插件，请参阅 [如何调试您自己的 IntelliJ IDEA 实例](https://medium.com/agorapulse-stories/how-to-debug-your-own-intellij-idea-instance-7d7df185a48d) 博客文章。

> 要了解有关如何处理基于 Gradle 的项目的更多信息，请参阅 [在 IntelliJ IDEA 中使用 Gradle](https://www.youtube.com/watch?v=6V6G3RyxEMk) 的屏幕录像和 IntelliJ IDEA 帮助中的有关 [Gradle 任务](https://www.jetbrains.com/help/idea/work-with-gradle-tasks.html) 的内容。
>
