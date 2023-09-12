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

### Components of a Wizard-Generated Gradle IntelliJ Platform Plugin

For the example `my_plugin` created with the steps describes above, the _IDE Plugin_ generator creates the following directory content:

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

* The default IntelliJ Platform <path>build.gradle.kts</path> file (see next paragraph).
* The <path>gradle.properties</path> file, containing properties used by Gradle build script.
* The <path>settings.gradle.kts</path> file, containing a definition of the `rootProject.name` and required repositories.
* The Gradle Wrapper files, and in particular the <path>gradle-wrapper.properties</path> file, which specifies the version of Gradle to be used to build the plugin.
  If needed, the IntelliJ IDEA Gradle plugin downloads the version of Gradle specified in this file.
* The <path>META-INF</path> directory under the default `main` [source set](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_project_layout) contains the plugin [configuration file](plugin_configuration_file.md) and [plugin icon](plugin_icon_file.md).
* The _Run Plugin_ [run configuration](https://www.jetbrains.com/help/idea/run-debug-configuration.html).

The generated `my_plugin` project <path>build.gradle.kts</path> file:

```kotlin
plugins {
  id("java")
  id("org.jetbrains.kotlin.jvm") version "1.9.10"
  id("org.jetbrains.intellij") version "1.15.0"
}

group = "com.example"
version = "1.0-SNAPSHOT"

repositories {
  mavenCentral()
}

// Configure Gradle IntelliJ Plugin
// Read more: https://plugins.jetbrains.com/docs/intellij/tools-gradle-intellij-plugin.html
intellij {
  version.set("2022.2.5")
  type.set("IC") // Target IDE Platform

  plugins.set(listOf(/* Plugin Dependencies */))
}

tasks {
  // Set the JVM compatibility versions
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

* Three Gradle plugins are explicitly declared:
  * The [Gradle Java](https://docs.gradle.org/current/userguide/java_plugin.html) plugin (`java`).
  * The [Kotlin Gradle](https://kotlinlang.org/docs/gradle-configure-project.html#apply-the-plugin) plugin (`org.jetbrains.kotlin.jvm`).
  * The [](tools_gradle_intellij_plugin.md) (`org.jetbrains.intellij`).
* The <control>Group</control> from the [New Project](#create-ide-plugin) wizard is the `project.group` value.
* The `sourceCompatibility` line is injected to enforce using Java 11 JDK to compile Java sources.
* The values of the [`intellij.version`](tools_gradle_intellij_plugin.md#intellij-extension-version) and [`intellij.type`](tools_gradle_intellij_plugin.md#intellij-extension-type) properties specify the version and type of the IntelliJ Platform to be used to build the plugin.
* The empty placeholder list for [plugin dependencies](tools_gradle_intellij_plugin.md#intellij-extension-plugins).
* The values of the [`patchPluginXml.sinceBuild`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-sincebuild) and [`patchPluginXml.untilBuild`](tools_gradle_intellij_plugin.md#tasks-patchpluginxml-untilbuild) properties specifying the minimum and maximum versions of the IDE build the plugin is compatible with.
* The initial [`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) and [`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) tasks configuration.
  See the [](publishing_plugin.md#publishing-plugin-with-gradle) section for more information.

> Consider using the [IntelliJ Platform Plugin Template](https://github.com/JetBrains/intellij-platform-plugin-template) which additionally provides CI setup covered with GitHub Actions.

#### Plugin Gradle Properties and Plugin Configuration File Elements

The Gradle properties `rootProject.name` and `project.group` will not, in general, match the respective [plugin configuration file](plugin_configuration_file.md) <path>plugin.xml</path> elements [`<name>`](plugin_configuration_file.md#idea-plugin__name) and [`<id>`](plugin_configuration_file.md#idea-plugin__id).
There is no IntelliJ Platform-related reason they should as they serve different functions.

The `<name>` element (used as the plugin's display name) is often the same as `rootProject.name`, but it can be more explanatory.

The `<id>` value must be a unique identifier over all plugins, typically a concatenation of the specified <control>Group</control> and <control>Artifact</control>.
Please note that it is impossible to change the `<id>` of a published plugin without losing automatic updates for existing installations.

## Running a Plugin With the `runIde` Gradle task

Gradle projects are run from the IDE's Gradle Tool window.

### Adding Code to the Project

Before running [`my_plugin`](#components-of-a-wizard-generated-gradle-intellij-platform-plugin), some code can be added to provide simple functionality.
See the [Creating Actions](working_with_custom_actions.md) tutorial for step-by-step instructions for adding a menu action.

### Executing the Plugin

The _IDE Plugin_ generator automatically creates the _Run Plugin_ run configuration that can be executed via the <ui-path>Run | Run...</ui-path> action or can be found in the <control>Gradle</control> tool window under the <control>Run Configurations</control> node.

To execute the Gradle `runIde` task directly, open the <control>Gradle</control> tool window and search for the <control>runIde</control> task under the <control>Tasks</control> node.
If it's not on the list, hit the re-import button in the [toolbar](https://www.jetbrains.com/help/idea/jetgradle-tool-window.html#gradle_toolbar) at the top of the Gradle tool window.
When the <control>runIde</control> task is visible, double-click it to execute.

To debug your plugin in a _standalone_ IDE instance, please see [How to Debug Your Own IntelliJ IDEA Instance](https://medium.com/agorapulse-stories/how-to-debug-your-own-intellij-idea-instance-7d7df185a48d) blog post.

> For more information about how to work with Gradle-based projects see the [Working with Gradle in IntelliJ IDEA](https://www.youtube.com/watch?v=6V6G3RyxEMk) screencast and working with [Gradle tasks](https://www.jetbrains.com/help/idea/work-with-gradle-tasks.html) in the IntelliJ IDEA help.
>
