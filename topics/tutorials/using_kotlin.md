<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 配置 Kotlin 支持

<link-summary>开发 Kotlin 插件的优势和所需配置。</link-summary>

<tldr>

**主页**: [Kotlin](https://kotlinlang.org)

**项目模板**: [](plugin_github_template.md)

</tldr>

<link-summary>使用或针对 Kotlin 编程语言开发插件。</link-summary>

这个页面描述了如何使用 [Kotlin](https://kotlinlang.org) 编程语言开发插件。

> To implement a plugin _operating_ on Kotlin code ([PSI](psi.md)) in the IDE, see [](idea.md#kotlin).
>
{title="操作 Kotlin 代码"}

## 使用 Kotlin 开发插件的优势 {id=advantages-of-developing-a-plugin-in-kotlin}

使用 Kotlin 编写 IntelliJ 平台插件与使用 Java 编写插件非常相似。  
现有的 Java 类可以通过使用 [J2K 转换器](https://kotlinlang.org/docs/mixing-java-kotlin-intellij.html#converting-an-existing-java-file-to-kotlin-with-j2k)（Kotlin 插件的一部分）转换为 Kotlin 等效类。

除了 [空安全](https://kotlinlang.org/docs/null-safety.html)、[类型安全构建器](https://kotlinlang.org/docs/type-safe-builders.html) 和 [](kotlin_coroutines.md)，Kotlin 语言为插件开发提供了许多便利功能，使插件更易于阅读和维护。
类似于 [Kotlin for Android](https://kotlinlang.org/docs/android-overview.html)，IntelliJ 平台广泛使用回调，这些回调在 Kotlin 中可以简洁地表示为 [lambda 表达式](https://kotlinlang.org/docs/lambdas.html)。

Kotlin 类可以与现有的 Java 代码混合使用在同一个项目中。  
当某些 API 需要使用 Kotlin 协程时，这种做法可能会非常有用。

### 添加扩展 {id=adding-extensions}

同样，可以使用 [扩展](https://kotlinlang.org/docs/extensions.html) 来定制 IntelliJ 平台中内部类的行为。
例如，通常会采用 [保护日志语句](https://www.slf4j.org/faq.html#logging_performance) 的做法，以避免参数构造的成本，使用日志时会产生如下的仪式感：

```java
if (logger.isDebugEnabled()) {
  logger.debug("..."+expensiveComputation());
}
```

我们可以通过声明以下扩展方法，在 Kotlin 中更简洁地实现相同的结果：

```kotlin
inline fun Logger.debug(lazyMessage: () -> String) {
  if (isDebugEnabled) {
    debug(lazyMessage())
  }
}
```

现在，我们可以直接编写：

```kotlin
logger.debug { "..." + expensiveComputation() }
```

以享受轻量级日志记录的所有优势，同时减少代码的冗长。

通过实践，您将能够识别出 IntelliJ 平台中许多可以使用 Kotlin 简化的习惯用法。

### Kotlin 中的 UI 表单 {id=ui-forms-in-kotlin}

IntelliJ 平台提供了一个 [类型安全的 DSL](kotlin_ui_dsl_version_2.md)，用于声明性地构建 UI 表单。

> 使用 Kotlin 的 _UI Designer_ 插件 [不受支持](https://youtrack.jetbrains.com/issue/KTIJ-791)。

### Kotlin 协程 {id=kotlin-coroutines}

[](kotlin_coroutines.md) 是一种轻量级且便捷的线程替代方案，具有许多 [优点](kotlin_coroutines.md#coroutines-advantages)。

## 添加 Kotlin 支持 {id=adding-kotlin-support}

> 该插件提供了一个预配置的使用 Kotlin 的项目。

IntelliJ IDEA 已捆绑必要的 Kotlin IDE 插件，无需进一步配置。  
有关详细说明，请参阅 [Kotlin 文档](https://kotlinlang.org/docs/getting-started.html)。

建议使用 Kotlin 2.x 版本来开发面向 2024.3 或更高版本的插件。

### Kotlin Gradle 插件 {id=kotlin-gradle-plugin}

向基于 Gradle 的项目添加 Kotlin 源文件编译支持，需要添加并配置 [Kotlin JVM Gradle 插件](https://kotlinlang.org/docs/gradle.html#targeting-the-jvm)。

### Kotlin标准库（stdlib） {id=kotlin-standard-library}

从 Kotlin 1.4 开始，标准库 _stdlib_ 的依赖会自动添加（[API 文档](https://kotlinlang.org/api/latest/jvm/stdlib/)）。  
在几乎所有情况下，**不需要**将其包含在插件分发包中，因为平台已经捆绑了该库。

要选择退出，请在 <path>gradle.properties</path> 中添加以下行：

```
kotlin.stdlib.default.dependency = false
```

#### Gradle 检查 {id=gradle-check}

通过相应的 Gradle 任务检查此 Gradle 属性的存在：

<tabs>  
<tab title="IntelliJ Platform Gradle Plugin (2.x)">  

[`verifyPlugin`](tools_intellij_platform_gradle_plugin_tasks.md#verifyPluginProjectConfiguration) 任务

</tab>  
<tab title="Gradle IntelliJ Plugin (1.x)">  

[`verifyPluginConfiguration`](tools_gradle_intellij_plugin.md#tasks-verifypluginconfiguration) 任务

</tab>  
</tabs>

如果该属性不存在，将在插件配置验证期间报告警告。  
若要将 _stdlib_ 打包到插件分发包中，请显式指定 `kotlin.stdlib.default.dependency = true`。

#### stdlib – 其他事项 {id=stdlib-miscellaneous}

如果一个插件支持 [多个平台版本](build_number_ranges.md)，它必须要么以最低捆绑的 _stdlib_ 版本为目标（见下表），  
要么必须在 [插件分发包中提供特定版本](plugin_content.md#plugin-with-dependencies)。

有关更多详细信息，请参阅 [依赖于标准库](https://kotlinlang.org/docs/gradle.html#dependency-on-the-standard-library)。

> 如果您需要将Kotlin标准库添加到您的 **测试项目** 依赖项，请参阅 [](testing_faq.md#how-to-test-a-jvm-language) 部分。
>
{title="在测试中添加stdlib"}

| IntelliJ 平台版本（最新更新） | 捆绑的 **stdlib** 版本 |
|---------------------|-------------------|
| 2024.3                                    | 2.0.21                   |
| 2024.2              | 1.9.24            |
| 2024.1              | 1.9.22            |
| 2023.3              | 1.9.21            |
| 2023.2              | 1.8.20            |
| 2023.1              | 1.8.0             |

#### 更早版本
{collapsible="true"}

| IntelliJ 平台版本（最新更新） | 捆绑的 **stdlib** 版本 |
|------------------------------|-------------------------|
| 2022.3                       | 1.7.22                  |
| 2022.2                       | 1.6.21                  |
| 2022.1                       | 1.6.10                  |

有关更早版本，请参见 [这里](https://www.jetbrains.com/legal/third-party-software/)。

### Kotlin协程库（kotlinx.coroutines）
{id="coroutinesLibraries"}

插件 **必须** 始终使用目标 IDE 中捆绑的库，而不是捆绑它们自己的版本。  
确保该库也没有通过传递性依赖添加
（参见 Gradle 用户指南中的 [查看和调试依赖](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html)）。

自2024.2起，捆绑了一个带有额外补丁的自定义 [分支](https://github.com/JetBrains/intellij-deps-kotlinx.coroutines)。

查看 [](kotlin_coroutines.md) 关于如何在插件中使用它们。

| IntelliJ平台版本 | 捆绑的 _kotlinx-coroutines_ 版本 |
|--------------|-----------------------------|
| 2024.3                    | 1.8.0-intellij-11                    |
|2024.2       | 1.8.0  -intellij-9                     |
| 2024.1       | 1.7.3                       |

### 其他捆绑的Kotlin库 {id=other-bundled-kotlin-libraries}

通常建议始终使用捆绑的库版本。

有关所有捆绑库的概述，请参阅 [第三方软件和许可证](https://www.jetbrains.com/legal/third-party-software/)。

### 增量编译 {id=incremental-compilation}

Kotlin Gradle 插件支持 [增量编译](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#incremental-compilation)，可以跟踪源文件的变化，确保编译器仅处理更新的代码。

<tabs>

<tab title="Kotlin 1.9.0及更高版本">

无需任何操作。

如果存在，请删除 `gradle.properties` 中额外的 `kotlin.incremental.useClasspathSnapshot=false` 属性。

</tab>

<tab title="Kotlin 1.8.20">

> 建议使用 Kotlin 1.9.0 或更高版本，该问题已得到解决。

Kotlin `1.8.20` 引入了一个 [新的增量编译方法](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#a-new-approach-to-incremental-compilation)，并默认启用。  
不幸的是，在读取大型 JAR 文件（如 <path>app.jar</path> 或 <path>3rd-party-rt.jar</path>）时，它与 IntelliJ 平台不兼容，导致出现 `Out of Memory` 异常：

```
任务 ':compileKotlin' 执行失败。
> 无法转换 app.jar 以匹配属性 {artifactType=classpath-entry-snapshot, org.gradle.libraryelements=jar, org.gradle.usage=java-runtime}。
   > ClasspathEntrySnapshotTransform 执行失败: .../lib/app.jar。
      > Java 堆空间不足
```

为避免此异常，请在 <path>gradle.properties</path> 中添加以下行：

```
kotlin.incremental.useClasspathSnapshot=false
```

</tab>
</tabs>

## 插件实施说明 {id=plugin-implementation-notes}

### 不要使用 "object" 而是 "class"

{id="object-vs-class"}

插件 *可以* 使用 [Kotlin 类](https://kotlinlang.org/docs/classes.html)（`class` 关键字）在 [插件配置文件](plugin_configuration_file.md) 中实现声明。  
当注册 [扩展](plugin_extensions.md) 时，平台使用依赖注入框架在运行时实例化这些类。  
因此，插件 *不得* 使用 [Kotlin 对象](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview)（`object` 关键字）  
来实现任何 <path>[plugin.xml](plugin_configuration_file.md)</path> 声明。  
扩展的生命周期管理由平台负责，实例化这些类作为 Kotlin 单例可能会引发问题。

一个显著的例外是 [`FileType`](%gh-ic%/platform/core-api/src/com/intellij/openapi/fileTypes/FileType.java)  
（`com.intellij.fileType` 扩展点），详情请参见下方的检查描述。

- <control>Plugin DevKit | Code | Kotlin object registered as extension</control> 适用于 Kotlin 代码
- <control>Plugin DevKit | Plugin descriptor | Extension class is a Kotlin object</control> 适用于 <path>plugin.xml</path>

### 在扩展中不要使用 `companion object`
{id="companion-object-extensions"}

A Kotlin 中的 `companion object` 会在加载其包含的类时创建，而 [扩展点的实现](plugin_extensions.md) 应该是廉价的创建。
为了避免不必要的类加载（从而减慢 IDE 的启动速度），在扩展中的 `companion object` 只应包含简单的常量或者 [logger](ide_infrastructure.md#logging)。
其他任何内容应该是顶级声明或存储在一个 `object` 中。

使用检查 <control>Plugin DevKit | Code | Companion object in extensions</control> 可以突出显示此类问题（2023.3 版本）。

## Kotlin 代码常见问题解答 {id=kotlin-code-faq}

### 测试 K2 模式 {id=testing-k2-mode}

参见 [在 K2 中进行本地测试](https://kotlin.github.io/analysis-api/testing-in-k2-locally.html)。

### 分析 API {id=analysis-api}
<primary-label ref="2024.2"/>

分析 API 是一个强大的库，用于分析 Kotlin 代码。  
它建立在 Kotlin PSI 语法树之上，提供对各种语义信息的访问，包括引用目标、表达式类型、声明范围、诊断信息等。

详情请参见 [Kotlin 分析 API 文档](https://kotlin.github.io/analysis-api/index_md.html)。

### 杂项 {id=miscellaneous}

[如何缩短引用](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010025120-Add-new-parameter-into-kotlin-data-class-from-IDEA-plugin?page=1#community_comment_360002950760)

## Kotlin 实现的示例插件 {id=example-plugins-implemented-in-kotlin}

浏览所有基于 IntelliJ 平台构建的 [开源 Kotlin 插件](https://jb.gg/ipe?language=kotlin)。
