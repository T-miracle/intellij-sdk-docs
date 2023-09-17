# 配置 Kotlin 支持

<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>在 Kotlin 中开发插件的优点和所需的配置。</link-summary>

<tldr>

**主页**: [Kotlin](https://kotlinlang.org)

**项目模板**: [](plugin_github_template.md)

</tldr>

<link-summary>使用或针对 Kotlin 编程语言开发插件。</link-summary>

这个页面介绍了如何使用 [Kotlin](https://kotlinlang.org) 编程语言来开发插件。

> 为了在 IDE 上实现一个 _操作_ Kotlin 代码的插件，配置 Kotlin 的[插件依赖](plugin_dependencies.md)（`org.jetbrains.kotlin`）。
> 请查看 [UAST](uast.md) 页面，了解有关如何支持多个 JVM 语言，包括 Kotlin 的信息。

## 使用 Kotlin 开发插件的优势

使用 Kotlin 编写 IntelliJ 平台的插件与使用 Java 编写插件非常相似。
现有的插件开发者可以通过使用 [J2K 转换器](https://kotlinlang.org/docs/mixing-java-kotlin-intellij.html#converting-an-existing-java-file-to-kotlin-with-j2k)（Kotlin 插件的一部分）将样板 Java 类转换为它们的 Kotlin 等效类来入门，而开发者可以轻松地将 Kotlin 类与其现有的 Java 代码混合使用。

除了 [空值（null）安全](https://kotlinlang.org/docs/null-safety.html) 和 [类型安全的构建器](https://kotlinlang.org/docs/type-safe-builders.html)，Kotlin 语言为插件开发提供了许多便捷的功能，这些功能使插件更易阅读和更简单维护。
与 [Kotlin 用于 Android](https://kotlinlang.org/docs/android-overview.html) 类似，IntelliJ 平台广泛使用回调，而在 Kotlin 中，可以轻松表达为[lambda表达式](https://kotlinlang.org/docs/lambdas.html)。

同样，可以使用 [扩展](https://kotlinlang.org/docs/extensions.html) 轻松定制 IntelliJ 平台中内部类的行为。
例如，常见做法是 [保护日志记录语句](https://www.slf4j.org/faq.html#logging_performance) 以避免参数构造的成本，从而导致在使用日志时出现以下繁文缛节：

```java
if (logger.isDebugEnabled()) {
  logger.debug("..."+expensiveComputation());
}
```

我们可以在 Kotlin 中更简洁地实现相同的结果，通过声明以下的扩展方法：

```kotlin
inline fun Logger.debug(lazyMessage: () -> String) {
  if (isDebugEnabled) {
    debug(lazyMessage())
  }
}
```

现在我们可以直接编写：

```kotlin
logger.debug { "..." + expensiveComputation() }
```

以此，您可以获得轻量级日志记录的所有优点，同时减少代码的冗长。

通过实践，您将能够识别出在 IntelliJ 平台中可以使用 Kotlin 简化的许多惯用语。

### Kotlin 中的 UI 表单

IntelliJ 平台提供 [类型安全 DSL](kotlin_ui_dsl_version_2.md)，允许以声明方式构建 UI 表单。

> 使用 Kotlin 的 _UI Designer_ 插件目前[不受支持](https://youtrack.jetbrains.com/issue/KTIJ-791)。
>

## 添加 Kotlin 支持 {id="添加Kotlin支持"}

> [](plugin_github_template.md) 提供了一个使用 Kotlin 预配置的项目。

IntelliJ IDEA 已经捆绑了必要的 Kotlin 插件，无需进一步配置。
有关详细说明，请参阅 [Kotlin文档](https://kotlinlang.org/docs/getting-started.html)。

### Kotlin Gradle 插件

要将 Kotlin 源文件编译支持添加到基于 Gradle 的项目中，需要添加并配置 [Kotlin JVM Gradle插件](https://kotlinlang.org/docs/gradle.html#targeting-the-jvm)。

请参考 [kotlin_demo](%gh-sdk-samples%/kotlin_demo)示例插件中的 `<path>build.gradle.kts</path>` 文件。

```kotlin
```

{src="kotlin_demo/build.gradle.kts" include-lines="2-"}

### Kotlin 标准库 {id="kotlin标准库"}

自 Kotlin 1.4 开始，标准库 `stdlib` 的依赖会自动添加（[API文档](https://kotlinlang.org/api/latest/jvm/stdlib/)）。
在大多数情况下，不需要将其包含在插件分发中，因为平台已经捆绑了它。

要选择不包括标准库，可以在 `<path>gradle.properties</path>` 中添加以下行：

```
kotlin.stdlib.default.dependency = false
```

这个 Gradle 属性的存在会被 [](tools_gradle_intellij_plugin.md)中的 [](tools_gradle_intellij_plugin.md#tasks-verifypluginconfiguration) 检查。
如果该属性不存在，那么在插件配置验证期间将会报告警告，因为当Kotlin stdlib捆绑在插件存档中时，这是一个常见的问题。
如果希望在最终存档中包含 Kotlin stdlib，请使用 `kotlin.stdlib.default.dependency = true` 显式指定它。

If a plugin supports [multiple platform versions](build_number_ranges.md), it must either target the lowest bundled `stdlib` version or provide the specific version in plugin distribution.

| IntelliJ 平台版本 | 捆绑式 `stdlib` 版 |
|---------------|----------------|
| 2023.2        | 1.8.20         |
| 2023.1        | 1.8.0          |
| 2022.3        | 1.7.0          |
| 2022.2        | 1.6.21         |
| 2022.1        | 1.6.20         |
| 2021.3        | 1.5.10         |
| 2021.2        | 1.5.10         |
| 2021.1        | 1.4.32         |
| 2020.3        | 1.4.0          |
| 2020.2        | 1.3.70         |
| 2020.1        | 1.3.70         |
| 2019.3        | 1.3.31         |
| 2019.2        | 1.3.3          |
| 2019.1        | 1.3.11         |

有关更多详细信息，请参阅 [依赖标准库](https://kotlinlang.org/docs/gradle.html#dependency-on-the-standard-library)。

> 如果您需要将Kotlin标准库添加到您的**测试项目**依赖项中，请参阅[此部分](testing_faq.md#how-to-test-a-jvm-language)。
>

### 增量编译

Kotlin Gradle 插件支持 [增量编译](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#incremental-compilation)，这允许跟踪源文件的更改，因此编译器仅处理已更新的代码。
这可以提高构建效率，因为不必重新编译所有代码。

<tabs>

<tab title="Kotlin 1.9.0">

如果存在额外的 `kotlin.incremental.useClasspathSnapshot=false` 属性，请将其从 `<path>gradle.properties</path>` 中移除。

</tab>

<tab title="Kotlin 1.8.20">

> 请考虑使用 Kotlin 1.9.0，其中此问题已经得到解决。

Kotlin `1.8.20` 发布版默认启用了[新的增量编译方法](https://kotlinlang.org/docs/gradle-compilation-and-caches.html#a-new-approach-to-incremental-compilation)。
然而，不幸的是，这种方法与 IntelliJ 平台不兼容，特别是在读取大型 JAR 文件（如 `<path>app.jar</path>` 或 `<path>3rd-party-rt.jar</path>`）时可能导致 `Out of Memory` 异常。

```
Execution failed for task ':compileKotlin'.
> Failed to transform app.jar to match attributes {artifactType=classpath-entry-snapshot, org.gradle.libraryelements=jar, org.gradle.usage=java-runtime}.
   > Execution failed for ClasspathEntrySnapshotTransform: .../lib/app.jar.
      > Java heap space
```

为了避免这个异常，可以将以下行添加到 `<path>gradle.properties</path>` 中：

```
kotlin.incremental.useClasspathSnapshot=false
```

您可以在 [KT-57757](https://youtrack.jetbrains.com/issue/KT-57757/Reduce-classpath-snapshotter-memory-consumption) 中找到该问题的当前状态。

</tab>
</tabs>

### 其他捆绑的 Kotlin 库 {id="其他捆绑的Kotlin库"}

请参阅 [第三方软件和许可](https://www.jetbrains.com/legal/third-party-software/)。

## 插件实现注意事项

### 请不要使用 "object" 而是使用 "class"。
{id="object-vs-class"}

插件*可以*使用 [Kotlin 类](https://kotlinlang.org/docs/classes.html)（使用`class`关键字）来实现 [插件配置文件](plugin_configuration_file.md) 中的声明。
在注册扩展时，平台使用依赖注入框架在运行时实例化这些类。
因此，插件*不得*使用 [Kotlin 对象](https://kotlinlang.org/docs/object-declarations.html#object-declarations-overview)（使用`object`关键字）来实现任何<path>[plugin.xml](plugin_configuration_file.md)</path>声明。
扩展的生命周期管理是平台的责任，将这些类实例化为Kotlin的单例可能会引发问题。

在 2023.2 版本中，问题可以通过以下检查进行突出显示：
- <control>Plugin DevKit（插件开发工具包） | Code（代码） | Kotlin object registered as extension（Kotlin 对象注册为扩展）</control> 用于 Kotlin 代码
- <control>Plugin DevKit（插件开发工具包）| Plugin descriptor（插件描述符） | Extension class is a Kotlin object（扩展类是一个 Kotlin 对象）</control> 用于 <path>plugin.xml</path>

### 请不要在扩展中使用 "companion object（伴生对象）"
{id="companion-object-extensions"}

Kotlin 的 `companion object` 会在尝试加载其包含的类时始终被创建，而[扩展点实现](plugin_extensions.md)应该是廉价的。
为了避免不必要的类加载（从而减缓 IDE 启动速度），扩展中的 `companion object` 必须只包含简单的常量或 [日志记录器](ide_infrastructure.md#logging)。
其他任何内容都必须是顶级声明或存储在一个 `object` 中。

可以使用检查 <control>Plugin DevKit（插件开发工具包） | Code（代码） | Companion object in extensions（扩展中的伴生对象）</control> 来突出显示这类问题（2023.3 版本）。

## Kotlin 代码常见问题解答 {id="Kotlin-代码常见问题解答"}

[如何缩短参考文献](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360010025120-Add-new-parameter-into-kotlin-data-class-from-IDEA-plugin?page=1#community_comment_360002950760)

## Kotlin 实现的示例插件 {id="Kotlin-实现的示例插件"}

有许多基于 IntelliJ 平台构建的 [开源 Kotlin 插件](https://jb.gg/ipe?language=kotlin)。
对于想要获取最新示例的开发者，可以参考这些项目以获取灵感：

* [Presentation Assistant](https://github.com/chashnikov/IntelliJ-presentation-assistant)
* [Rust](https://github.com/intellij-rust/intellij-rust)
* [TeXiFy IDEA](https://github.com/Hannah-Sten/TeXiFy-IDEA)
* [Deno](%gh-ij-plugins%/Deno)
