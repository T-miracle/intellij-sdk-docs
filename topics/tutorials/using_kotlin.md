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

Using Kotlin to write plugins for the IntelliJ Platform is very similar to writing plugins in Java.
Existing Java classes can be converted to their Kotlin equivalents by using the [J2K converter](https://kotlinlang.org/docs/mixing-java-kotlin-intellij.html#converting-an-existing-java-file-to-kotlin-with-j2k) (part of the Kotlin plugin).

除了 [空安全](https://kotlinlang.org/docs/null-safety.html)、[类型安全构建器](https://kotlinlang.org/docs/type-safe-builders.html) 和 [](kotlin_coroutines.md)，Kotlin 语言为插件开发提供了许多便利功能，使插件更易于阅读和维护。
类似于 [Kotlin for Android](https://kotlinlang.org/docs/android-overview.html)，IntelliJ 平台广泛使用回调，这些回调在 Kotlin 中可以简洁地表示为 [lambda 表达式](https://kotlinlang.org/docs/lambdas.html)。

Kotlin classes can be mixed in a project with existing Java code.
This might come in handy when certain APIs require the use of mentioned Kotlin Coroutines.

### 添加扩展 {id=adding-extensions}

同样，可以使用 [扩展](https://kotlinlang.org/docs/extensions.html) 来定制 IntelliJ 平台中内部类的行为。
例如，通常会采用 [保护日志语句](https://www.slf4j.org/faq.html#logging_performance) 的做法，以避免参数构造的成本，使用日志时会产生如下的仪式感：

```java
if (logger.isDebugEnabled()) {
  logger.debug("..."+expensiveComputation());
}
```

We can achieve the same result more succinctly in Kotlin by declaring the following extension method:

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

The IntelliJ Platform provides a [type-safe DSL](kotlin_ui_dsl_version_2.md) to build UI forms declaratively.

> 使用 Kotlin 的 _UI Designer_ 插件 [不受支持](https://youtrack.jetbrains.com/issue/KTIJ-791)。

### Kotlin Coroutines

[](kotlin_coroutines.md) are a lightweight and convenient alternative to threads with many [advantages](kotlin_coroutines.md#coroutines-advantages).

## Adding Kotlin Support

> 该插件提供了一个预配置的使用 Kotlin 的项目。

IntelliJ IDEA bundles the necessary Kotlin IDE plugin, requiring no further configuration.
For detailed instructions, refer to the [Kotlin documentation](https://kotlinlang.org/docs/getting-started.html).

Using Kotlin 2.x is recommended for plugin targeting 2024.3 or later.

### Kotlin Gradle 插件 {id=kotlin-gradle-plugin}

Adding Kotlin source files compilation support to a Gradle-based project requires adding and configuring the [Kotlin JVM Gradle plugin](https://kotlinlang.org/docs/gradle.html#targeting-the-jvm).

### Kotlin标准库（stdlib） {id=kotlin-standard-library}

Starting with Kotlin 1.4, a dependency on the standard library _stdlib_ is added automatically ([API Docs](https://kotlinlang.org/api/latest/jvm/stdlib/)).
In nearly all cases, it is _not necessary_ to include it in the plugin distribution as the platform already bundles it.

要选择退出，请在 <path>gradle.properties</path> 中添加以下行：

```
kotlin.stdlib.default.dependency = false
```

#### Gradle check

The presence of this Gradle property is checked with the corresponding Gradle task:

<tabs>
<tab title="IntelliJ Platform Gradle Plugin (2.x)">

[`verifyPlugin`](tools_intellij_platform_gradle_plugin_tasks.md#verifyPluginProjectConfiguration) task

</tab>
<tab title="Gradle IntelliJ Plugin (1.x)">

[`verifyPluginConfiguration`](tools_gradle_intellij_plugin.md#tasks-verifypluginconfiguration) task

</tab>
</tabs>

If the property is not present, a warning will be reported during the plugin configuration verification.
To bundle _stdlib_ in the plugin distribution, specify explicitly `kotlin.stdlib.default.dependency = true`.

#### stdlib – Miscellaneous

If a plugin supports [multiple platform versions](build_number_ranges.md), it must either target the lowest bundled _stdlib_ version (see table below)
or the specific version must be [provided in plugin distribution](plugin_content.md#plugin-with-dependencies).

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

Plugins _must_ always use the bundled library from the target IDE and not bundle their own version.
Make sure it is not added via transitive dependencies either
(see [View and Debug Dependencies](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html) in the Gradle user guide).

自2024.2起，捆绑了一个带有额外补丁的自定义 [分支](https://github.com/JetBrains/intellij-deps-kotlinx.coroutines)。

查看 [](kotlin_coroutines.md) 关于如何在插件中使用它们。

| IntelliJ平台版本 | 捆绑的 _kotlinx-coroutines_ 版本 |
|--------------|-----------------------------|
| 2024.3                    | 1.8.0-intellij-11                    |
|2024.2       | 1.8.0  -intellij-9                     |
| 2024.1       | 1.7.3                       |

### 其他捆绑的Kotlin库 {id=other-bundled-kotlin-libraries}

通常建议始终使用捆绑的库版本。

See [Third-Party Software and Licenses](https://www.jetbrains.com/legal/third-party-software/) for an overview of all bundled libraries.

### 增量编译 {id=incremental-compilation}

The Kotlin Gradle plugin supports [incremental compilation](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#incremental-compilation), which allows tracking changes in the source files, so the compiler handles only updated code.

<tabs>

<tab title="Kotlin 1.9.0及更高版本">

No action is required.

Remove the additional `kotlin.incremental.useClasspathSnapshot=false` property in <path>gradle.properties</path> if present.

</tab>

<tab title="Kotlin 1.8.20">

> Consider using Kotlin 1.9.0 or later where this issue has been resolved.

Kotlin `1.8.20` has a [new incremental compilation approach](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#a-new-approach-to-incremental-compilation) which is enabled by default.
Unfortunately, it is not compatible with the IntelliJ Platform when reading large JAR files (like <path>app.jar</path> or <path>3rd-party-rt.jar</path>),
leading to an `Out of Memory` exception:

```
Execution failed for task ':compileKotlin'.
> Failed to transform app.jar to match attributes {artifactType=classpath-entry-snapshot, org.gradle.libraryelements=jar, org.gradle.usage=java-runtime}.
   > Execution failed for ClasspathEntrySnapshotTransform: .../lib/app.jar.
      > Java heap space
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

Plugins *may* use [Kotlin classes](https://kotlinlang.org/docs/classes.html) (`class` keyword) to implement declarations in the [plugin configuration file](plugin_configuration_file.md).
When registering an [extension](plugin_extensions.md), the platform uses a dependency injection framework to instantiate these classes at runtime.
For this reason, plugins *must not* use [Kotlin objects](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview) (`object` keyword)
to implement any <path>[plugin.xml](plugin_configuration_file.md)</path> declarations.
Managing the lifecycle of extensions is the platform's responsibility, and instantiating these classes as Kotlin singletons may cause issues.

A notable exception is [`FileType`](%gh-ic%/platform/core-api/src/com/intellij/openapi/fileTypes/FileType.java)
(`com.intellij.fileType` extension point), see also the inspection descriptions below.

- <control>Plugin DevKit | Code | Kotlin object registered as extension</control> 适用于 Kotlin 代码
- <control>Plugin DevKit | Plugin descriptor | Extension class is a Kotlin object</control> 适用于 <path>plugin.xml</path>

### Do not use `companion object` in extensions

{id="companion-object-extensions"}

A Kotlin 中的 `companion object` 会在加载其包含的类时创建，而 [扩展点的实现](plugin_extensions.md) 应该是廉价的创建。
为了避免不必要的类加载（从而减慢 IDE 的启动速度），在扩展中的 `companion object` 只应包含简单的常量或者 [logger](ide_infrastructure.md#logging)。
其他任何内容应该是顶级声明或存储在一个 `object` 中。

使用检查 <control>Plugin DevKit | Code | Companion object in extensions</control> 可以突出显示此类问题（2023.3 版本）。

## Kotlin 代码常见问题解答 {id=kotlin-code-faq}

### Testing K2 Mode

See [Testing in K2 Locally](https://kotlin.github.io/analysis-api/testing-in-k2-locally.html).

### Analysis API
<primary-label ref="2024.2"/>

The Analysis API is a powerful library for analyzing code in Kotlin.
Built on top of the Kotlin PSI syntax tree, it provides access to various semantic information, including reference targets, expression types, declaration scopes, diagnostics, and more.

See [Kotlin Analysis API Documentation](https://kotlin.github.io/analysis-api/index_md.html) for details.

### Miscellaneous

[如何缩短引用](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010025120-Add-new-parameter-into-kotlin-data-class-from-IDEA-plugin?page=1#community_comment_360002950760)

## Kotlin 实现的示例插件 {id=example-plugins-implemented-in-kotlin}

Browse all [open-source Kotlin plugins](https://jb.gg/ipe?language=kotlin) built on the IntelliJ Platform.
