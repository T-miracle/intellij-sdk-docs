<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 配置 Kotlin 支持

<link-summary>开发 Kotlin 插件的优势和所需配置。</link-summary>

<tldr>

**主页**: [Kotlin](https://kotlinlang.org)

**项目模板**: [](plugin_github_template.md)

</tldr>

<link-summary>使用或针对 Kotlin 编程语言开发插件。</link-summary>

这个页面描述了如何使用 [Kotlin](https://kotlinlang.org) 编程语言开发插件。

> 要在 IDE 中实现操作 Kotlin 代码的插件，请配置 Kotlin [插件依赖](plugin_dependencies.md) (`org.jetbrains.kotlin`)。
> 关于如何支持多种 JVM 语言（包括 Kotlin），请参阅[UAST](uast.md)页面。
>
{title="操作 Kotlin 代码"}

## 使用 Kotlin 开发插件的优势 {id=advantages-of-developing-a-plugin-in-kotlin}

使用 Kotlin 编写 IntelliJ 平台插件与使用 Java 编写插件非常相似。
可以使用 [J2K 转换器](https://kotlinlang.org/docs/mixing-java-kotlin-intellij.html#converting-an-existing-java-file-to-kotlin-with-j2k)（Kotlin 插件的一部分）将现有的 Java 类转换为 Kotlin 等价类。

除了 [空安全](https://kotlinlang.org/docs/null-safety.html)、[类型安全构建器](https://kotlinlang.org/docs/type-safe-builders.html) 和 [](kotlin_coroutines.md)，Kotlin 语言为插件开发提供了许多便利功能，使插件更易于阅读和维护。
类似于 [Kotlin for Android](https://kotlinlang.org/docs/android-overview.html)，IntelliJ 平台广泛使用回调，这些回调在 Kotlin 中可以简洁地表示为 [lambda 表达式](https://kotlinlang.org/docs/lambdas.html)。

Kotlin 类可以与项目中的现有 Java 代码混合使用。当某些 API 需要使用上述的 Kotlin 协程时，这可能会非常方便。

### 添加扩展 {id=adding-extensions}

同样，可以使用 [扩展](https://kotlinlang.org/docs/extensions.html) 来定制 IntelliJ 平台中内部类的行为。
例如，通常会采用 [保护日志语句](https://www.slf4j.org/faq.html#logging_performance) 的做法，以避免参数构造的成本，使用日志时会产生如下的仪式感：

```java
if (logger.isDebugEnabled()) {
  logger.debug("..." + expensiveComputation());
}
```

在 Kotlin 中，我们可以通过声明以下扩展方法来更简洁地实现相同的结果：

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

IntelliJ 平台提供了一种 [类型安全的 DSL](kotlin_ui_dsl_version_2.md) 来以声明方式构建 UI 表单。

> 使用 Kotlin 的 _UI Designer_ 插件 [不受支持](https://youtrack.jetbrains.com/issue/KTIJ-791)。

### Kotlin Coroutines

[](kotlin_coroutines.md) are a lightweight and easy to implement alternative to threads with many [advantages](kotlin_coroutines.md#coroutines-advantages).

## Adding Kotlin Support

> 该插件提供了一个预配置的使用 Kotlin 的项目。

IntelliJ IDEA 包含必要的 Kotlin 插件，无需进一步配置。
有关详细说明，请参阅 [Kotlin 文档](https://kotlinlang.org/docs/getting-started.html)。

### Kotlin Gradle 插件 {id=kotlin-gradle-plugin}

向基于 Gradle 的项目添加 Kotlin 源文件编译支持需要添加并配置 [Kotlin JVM Gradle 插件](https://kotlinlang.org/docs/gradle.html#targeting-the-jvm)。

See the <path>build.gradle.kts</path> from [kotlin_demo](%gh-sdk-samples-master%/kotlin_demo) sample plugin using [](tools_gradle_intellij_plugin.md):

```kotlin
```

{src="kotlin_demo/build.gradle.kts" include-lines="2-" default-state="collapsed" collapsible="true" collapsed-title="build.gradle.kts"}

### Kotlin标准库（stdlib） {id=kotlin-standard-library}

Since Kotlin 1.4, a dependency on the standard library _stdlib_ is added automatically ([API Docs](https://kotlinlang.org/api/latest/jvm/stdlib/)).
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

插件 **必须** 总是使用目标IDE提供的捆绑库，而不是捆绑自己的版本。
请确保它也不是通过传递依赖添加的
（参见 [查看和调试依赖](https://docs.gradle.org/current/userguide/viewing_debugging_dependencies.html) Gradle 用户指南）。

自2024.2起，捆绑了一个带有额外补丁的自定义 [分支](https://github.com/JetBrains/intellij-deps-kotlinx.coroutines)。

查看 [](kotlin_coroutines.md) 关于如何在插件中使用它们。

| IntelliJ平台版本 | 捆绑的 _kotlinx-coroutines_ 版本 |
|--------------|-----------------------------|
| 2024.2       | 1.8.0                       |
| 2024.1       | 1.7.3                       |

### 其他捆绑的Kotlin库 {id=other-bundled-kotlin-libraries}

通常建议始终使用捆绑的库版本。

请参阅 [第三方软件和许可证](https://www.jetbrains.com/legal/third-party-software/) 获取所有捆绑库的概述。

### 增量编译 {id=incremental-compilation}

Kotlin Gradle插件支持 [增量编译](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#incremental-compilation)，可以追踪源文件的变更，使编译器仅处理更新的代码。

<tabs>

<tab title="Kotlin 1.9.0及更高版本">

无需采取任何操作。

如果存在额外的 `kotlin.incremental.useClasspathSnapshot=false` 属性，请从 <path>gradle.properties</path> 中移除。

</tab>

<tab title="Kotlin 1.8.20">

> 请考虑使用 Kotlin 1.9.0 或更高版本，此问题已得到解决。

Kotlin `1.8.20` 使用了一种 [新的增量编译方法](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#a-new-approach-to-incremental-compilation)，默认已启用。
然而，它与 IntelliJ 平台不兼容 — 当读取大型 JAR 文件（例如 <path>app.jar</path> 或 <path>3rd-party-rt.jar</path>）时，
可能会导致 `Out of Memory` 异常：

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

插件可以使用 [Kotlin 类](https://kotlinlang.org/docs/classes.html) (`class` 关键字) 在 [插件配置文件](plugin_configuration_file.md) 中实现声明。
在注册扩展时，平台使用依赖注入框架在运行时实例化这些类。
因此，插件不应使用 [Kotlin 对象](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview) (`object` 关键字) 来实现任何 <path>[plugin.xml](plugin_configuration_file.md)</path> 中的声明。
管理扩展的生命周期是平台的责任，将这些类实例化为 Kotlin 单例可能会引发问题。
一个显著的例外是 `com.intellij.openapi.fileTypes.FileType` (`com.intellij.fileType` 扩展点)，还可以查看以下检查描述。

这些检查在 2023.2 版本中突出显示问题：

- <control>Plugin DevKit | Code | Kotlin object registered as extension</control> 适用于 Kotlin 代码
- <control>Plugin DevKit | Plugin descriptor | Extension class is a Kotlin object</control> 适用于 <path>plugin.xml</path>

### Do not use `companion object` in extensions

{id="companion-object-extensions"}

Kotlin 中的 `companion object` 会在加载其包含的类时创建，而 [扩展点的实现](plugin_extensions.md) 应该是廉价的创建。
为了避免不必要的类加载（从而减慢 IDE 的启动速度），在扩展中的 `companion object` 只应包含简单的常量或者 [logger](ide_infrastructure.md#logging)。
其他任何内容应该是顶级声明或存储在一个 `object` 中。

使用检查 <control>Plugin DevKit | Code | Companion object in extensions</control> 可以突出显示此类问题（2023.3 版本）。

## Kotlin 代码常见问题解答 {id=kotlin-code-faq}

[如何缩短引用](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010025120-Add-new-parameter-into-kotlin-data-class-from-IDEA-plugin?page=1#community_comment_360002950760)

## Kotlin 实现的示例插件 {id=example-plugins-implemented-in-kotlin}

有许多基于 IntelliJ 平台构建的 [开源 Kotlin 插件](https://jb.gg/ipe?language=kotlin)。
开发者可以参考以下项目，获取最新的 Kotlin 插件实现示例：

* [Rust](https://github.com/intellij-rust/intellij-rust)
* [TeXiFy IDEA](https://github.com/Hannah-Sten/TeXiFy-IDEA)
* [Deno](%gh-ij-plugins%/Deno)
