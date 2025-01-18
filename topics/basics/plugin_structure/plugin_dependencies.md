<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件依赖

<!-- https://jb.gg/ij-plugin-dependencies -->

<link-summary>声明对其他基于 IntelliJ 平台的插件的依赖。</link-summary>

插件可能依赖于其他插件（无论是捆绑插件还是第三方插件）的 API 和类。

本文档描述了声明插件依赖和可选插件依赖的语法。  
有关 IntelliJ 平台模块依赖的更多信息，请参阅 [](plugin_compatibility.md)。

> 对于添加对第三方库的依赖，请使用常规的 [Gradle 依赖管理](https://docs.gradle.org/current/userguide/core_dependency_management.html)。
>
{style="note"}

<procedure title="必要步骤">

要表达对其他插件或模块类的依赖，请按照本页详细说明的以下三个必要步骤操作：

1. 定位插件 ID
2. 项目设置
3. 在 <path>[plugin.xml](plugin_configuration_file.md)</path> 中声明

> 如果在运行时出现 `java.lang.NoClassDefFoundError`，很可能是省略了步骤 3。
>
> 否则，加载插件依赖可能失败，请检查
> [开发实例](ide_development_instance.md#development-instance-settings-caches-logs-and-plugins) 中的日志文件。
>
{title="出现 java.lang.NoClassDefFoundError"}

</procedure>

## 1. 定位插件 ID 并准备沙箱 {id=locating-plugin-id-and-preparing-sandbox}

必须根据插件的 [兼容性](build_number_ranges.md) 仔细选择兼容版本。  
对于非捆绑插件，无法为依赖插件指定最小/最大版本。（[问题](https://youtrack.jetbrains.com/issue/IDEABKL-7906)）

### JetBrains Marketplace

对于发布在 [JetBrains Marketplace](https://plugins.jetbrains.com) 上的插件：

1. 打开插件的详情页面
2. 滚动到页面底部的 <control>附加信息</control> 部分
3. 复制 <control>插件 ID</control>

### 捆绑插件和其他插件 {id=bundled-and-other-plugins}

所有捆绑插件的 ID 都可以通过专用的 Gradle 任务收集。  
有关如何定位插件分发文件的插件 ID，请参阅 _其他_ 选项卡。

<tabs>

<tab title="IntelliJ Platform Gradle Plugin (2.x)">

使用 [`printBundledPlugins`](tools_intellij_platform_gradle_plugin_tasks.md#printBundledPlugins) 任务。

</tab>

<tab title="Gradle IntelliJ Plugin (1.x)">

使用 [`listBundledPlugins`](tools_gradle_intellij_plugin.md#tasks-listbundledplugins) 任务。

</tab>

<tab title="其他">

定位包含 <path>META-INF/plugin.xml</path> 描述文件的插件主 JAR 文件，其中包含 [`<id>`](plugin_configuration_file.md#idea-plugin__id) 标签（如果未指定 `<id>`，则使用 [`<name>`](plugin_configuration_file.md#idea-plugin__name)）。

捆绑插件位于 <path>\$PRODUCT_ROOT\$/plugins/\$PLUGIN_NAME\$/lib/\$PLUGIN_NAME\$.jar</path>。

</tab>

</tabs>

#### 捆绑插件的 ID

下表列出了一些常用的捆绑插件及其 ID。  
另请参阅 [](plugin_compatibility.md#modules-specific-to-functionality)。

<!-- 请不要添加更多条目，除非它明显是一个流行的插件 -->

| 插件名称               | 插件 ID                       | 相关文档         |
|---------------------------|---------------------------------|-------------------------------|
| Copyright                 | `com.intellij.copyright`        |                               |
| CSS                       | `com.intellij.css`              | [](webstorm.md)               |
| Database Tools and SQL    | `com.intellij.database`         | [](data_grip.md)              |
| Gradle                    | `com.intellij.gradle`           |                               |
| Groovy                    | `org.intellij.groovy`           |                               |
| IntelliLang               | `org.intellij.intelliLang`      | [](language_injection.md)     |
| Java                      | `com.intellij.java`             | [](idea.md#java)              |
| JavaScript and TypeScript | `JavaScript`                    | [](webstorm.md)               |
| Kotlin                    | `org.jetbrains.kotlin`          | [](idea.md#kotlin)            |
| Markdown                  | `org.intellij.plugins.markdown` |                               |
| Maven                     | `org.jetbrains.idea.maven`      |                               |
| Spring                    | `com.intellij.spring`           | [](spring_api.md)             |
| Spring Boot               | `com.intellij.spring.boot`      | [](spring_api.md#spring-boot) |
| YAML                      | `org.jetbrains.plugins.yaml`    |                               |

### 准备沙盒 {id=preparing-sandbox}

如果插件未与目标 IDE 捆绑，请运行目标 IDE 的（沙盒）[IDE 开发实例](ide_development_instance.md)并在其中安装插件。

## 2. 项目设置 {id=project-setup}

根据选择的开发工作流（Gradle 或 DevKit），以下是必要的步骤之一。

### IntelliJ Platform Gradle 插件 (2.x) {id=intellij-platform-gradle-plugin-2x}

{collapsible="true" default-state="expanded"}

在 `<path>build.gradle.kts</path>` 文件的 `dependencies {}` 块中使用提供的辅助函数定义插件依赖：

```kotlin
dependencies {
  intellijPlatform {
    bundledPlugin("<pluginId>")
    plugin("<nonBundledPluginId>:<version>")
  }
}
```

对于捆绑插件，使用 `bundledPlugin()`。对于非捆绑插件（例如来自 [JetBrains Marketplace](https://plugins.jetbrains.com)），使用 `plugin()`。

有关完整参考和其他选项，请参阅 [](tools_intellij_platform_gradle_plugin_dependencies_extension.md#plugins)。

### Gradle IntelliJ 插件 (1.x) {id=gradle-intellij-plugin-1x}

{collapsible="true" default-state="collapsed"}

<primary-label ref="Obsolete"/>

<include from="tools_gradle_intellij_plugin.md" element-id="gradlePluginObsolete"/>

> 请参阅 [`intellij.plugins`](tools_gradle_intellij_plugin.md#intellij-extension-plugins) 属性以了解可接受的值。
>
{style="note"}

将依赖添加到构建脚本中的 [`intellij.plugins`](tools_gradle_intellij_plugin.md#intellij-extension-plugins) 参数：

<tabs>
<tab title="Kotlin">

```kotlin
intellij {
  plugins.set(listOf("com.example.another-plugin:1.0"))
}
```

</tab>
<tab title="Groovy">

```groovy
intellij {
  plugins = ['com.example.another-plugin:1.0']
}
```

</tab>
</tabs>

> 目前，测试所需的传递依赖必须[显式指定](https://github.com/JetBrains/gradle-intellij-plugin/issues/38)。
>
{style="note"}

### 插件 DevKit {id=plugin-devkit}

{collapsible="true" default-state="collapsed"}

> 现有的基于 DevKit 的项目可以[转换为使用 Gradle 设置](migrating_plugin_devkit_to_gradle.md)，其中依赖管理是完全自动化的。
>
{style="note"}

将项目依赖的插件 JAR 添加到 [*IntelliJ Platform SDK*](setting_up_theme_environment.md#add-intellij-platform-plugin-sdk) 的 <control>Classpath</control> 中。

> 不要将插件 JAR 添加为库：这将在运行时失败，因为 IntelliJ Platform 会加载依赖插件类的两个独立副本。
>
{style="warning"}

<procedure title="在基于 DevKit 的插件中添加插件依赖">

1. 打开 <control>Project Structure</control> 对话框并转到 <ui-path>Platform Settings | SDKs</ui-path> 部分。
2. 选择项目中使用的 SDK。
3. 点击 <control>Classpath</control> 选项卡中的 <control>+</control> 按钮。
4. 根据插件是捆绑还是非捆绑插件选择插件 JAR：
    - 对于捆绑插件，插件 JAR 文件位于主安装目录下的 <path>plugins/\$PLUGIN_NAME\$</path> 或 <path>plugins/\$PLUGIN_NAME\$/lib</path>。
    - 对于非捆绑插件，根据平台版本，插件 JAR 文件位于：
        - [2020.1+ 版本的插件目录](https://www.jetbrains.com/help/idea/directories-used-by-the-ide-to-store-settings-caches-plugins-and-logs.html#plugins-directory)
        - [2020.1 之前版本的插件目录](https://www.jetbrains.com/help/idea/2019.3/tuning-the-ide.html#plugins-directory)

</procedure>

## 3. 在 plugin.xml 中声明依赖 {id=dependency-declaration-in-pluginxml}

无论插件项目使用的是 [](plugin_compatibility.md#modules-available-in-all-products) 还是 [](plugin_compatibility.md#modules-specific-to-functionality)，正确的模块都必须作为依赖项列在 <path>plugin.xml</path> 中。
如果项目依赖于另一个插件，则必须像 [模块](plugin_compatibility.md#modules) 一样声明依赖。
如果仅使用一般的 IntelliJ Platform 功能（API），则必须声明对 `com.intellij.modules.platform` 的默认依赖。

要显示可用的 IntelliJ Platform 模块列表，请在编辑插件项目的 <path>plugin.xml</path> 文件时，为 [`<depends>`](plugin_configuration_file.md#idea-plugin__depends) 元素内容调用 [代码补全](https://www.jetbrains.com/help/idea/auto-completing-code.html#4eac28ba) 功能。

在 <path>plugin.xml</path> 中，添加一个 `<depends>` 标签，并将依赖插件的 ID 作为其内容。
继续上面的 [项目设置](#project-setup) 示例，<path>plugin.xml</path> 中的依赖声明将如下所示：

```xml
<depends>com.example.another-plugin</depends>
```

## 可选插件依赖

插件还可以指定可选插件依赖。
在这种情况下，即使依赖的插件未安装或未启用，插件也会加载，但部分功能将不可用。

声明额外的 `optional="true"` 和必需的 `config-file` 属性，指向 [可选插件描述文件](plugin_configuration_file.md#additional-plugin-configuration-files)：

```xml
<depends
    optional="true"
    config-file="myPluginId-optionalPluginName.xml">dependency.plugin.id</depends>
```

> 额外的插件描述文件必须遵循命名模式 <path>myPluginId-\$NAME\$.xml</path>，以确保文件名唯一，防止测试中类加载器出现问题（[详情](https://youtrack.jetbrains.com/issue/IDEA-205964)）。
>
{style="note"}

### 示例 {id=sample}

该插件为 Java 和 Kotlin 文件添加了额外的语法高亮功能。
主 <path>plugin.xml</path> 定义了对 Java 插件（插件 ID `com.intellij.java`）的必需依赖，并注册了相应的 `com.intellij.annotator` 扩展。
此外，它还指定了对 Kotlin 插件（插件 ID `org.jetbrains.kotlin`）的可选依赖：

<path>plugin.xml</path>
```xml
<idea-plugin>
   ...
   <depends>com.intellij.java</depends>

   <depends
       optional="true"
       config-file="myPluginId-withKotlin.xml">org.jetbrains.kotlin</depends>

   <extensions defaultExtensionNs="com.intellij">
      <annotator
          language="JAVA"
          implementationClass="com.example.MyJavaAnnotator"/>
   </extensions>
</idea-plugin>
```

配置文件 <path>myPluginId-withKotlin.xml</path> 位于与主 <path>plugin.xml</path> 文件相同的目录中。
在该文件中，定义了 Kotlin 的注释器扩展：

<path>myPluginId-withKotlin.xml</path>

```xml
<idea-plugin>
  <extensions defaultExtensionNs="com.intellij">
    <annotator
            language="kotlin"
            implementationClass="com.example.MyKotlinAnnotator"/>
  </extensions>
</idea-plugin>
```
