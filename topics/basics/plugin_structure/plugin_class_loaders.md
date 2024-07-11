<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 类加载器

<link-summary>插件类加载器的介绍和使用技巧。</link-summary>

为加载每个插件的类使用了独立的类加载器。
这允许每个插件使用不同的库版本，即使相同的库被 IDE 本身或其他插件使用。

## 捆绑的库

[第三方软件和许可](https://www.jetbrains.com/legal/third-party-software/) 列出了每个产品的所有捆绑库及其版本。

## 覆盖 IDE 依赖项

Gradle 7 引入了 `implementation` 作用域，取代了 `compile` 作用域。
对于这个设置，要使用项目定义的依赖项而不是捆绑的 IDE 版本，将以下代码片段添加到您的 Gradle 构建脚本中：

<tabs>
<tab title="Kotlin">

```kotlin
configurations.all {
  resolutionStrategy.sortArtifacts(ResolutionStrategy.SortOrder.DEPENDENCY_FIRST)
}
```

</tab>
<tab title="Groovy">

```groovy
configurations.all {
  resolutionStrategy.sortArtifacts(ResolutionStrategy.SortOrder.DEPENDENCY_FIRST)
}
```

</tab>
</tabs>

## 插件依赖项中的类

默认情况下，主 IDE 类加载器加载在插件类加载器中找不到的类。
但是，在 <path>[plugin.xml](plugin_configuration_file.md)</path> 文件中，您可以使用 [`<depends>`](plugin_configuration_file.md#idea-plugin__depends) 元素来指定一个 [插件依赖于](plugin_dependencies.md) 一个或多个其他插件。
在这种情况下，那些插件的类加载器将用于在当前插件中找不到的类。
这允许一个插件引用其他插件中的类。

## Using `ServiceLoader`

一些库使用 [`ServiceLoader`](https://docs.oracle.com/javase/8/docs/api/index.html?java/util/ServiceLoader.html) 来检测和加载实现。
为了使插件中正常工作，上下文类加载器必须设置为插件的类加载器，并在初始化代码周围使用原始类加载器进行恢复：

```java
Thread currentThread = Thread.currentThread();
ClassLoader originalClassLoader = currentThread.getContextClassLoader();
ClassLoader pluginClassLoader = this.getClass().getClassLoader();
try {
  currentThread.setContextClassLoader(pluginClassLoader);
  // 在这里使用 ServiceLoader 的代码
} finally {
  currentThread.setContextClassLoader(originalClassLoader);
}
```
