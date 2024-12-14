<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件内容

<link-summary>插件分发文件的布局和内容。</link-summary>

插件分发是使用专门的 Gradle `buildPlugin` 任务构建的（参考：[2.x](tools_intellij_platform_gradle_plugin_tasks.md#buildPlugin)，
[1.x](tools_gradle_intellij_plugin.md#tasks-buildplugin)），或使用 [Plugin DevKit](deploying_theme.md)。

插件分发的 `<path>.jar</path>` 文件包含以下内容：

- 配置文件 `<path>META-INF/plugin.xml</path>` ([Plugin Configuration File](plugin_configuration_file.md))
- 实现插件功能的类文件
- 推荐的插件图标文件 `<path>META-INF/pluginIcon*.svg</path>` ([Plugin Logo](plugin_icon_file.md))

> 参见[](plugin_user_experience.md#distribution-size)获取优化插件分发文件的重要步骤。


无法将插件分发目标指定为特定的操作系统（[问题](https://youtrack.jetbrains.com/issue/MP-1896)）。

### 无依赖插件

由一个单一的 `<path>.jar</path>` 文件组成的插件被放置在 `<path>/plugins</path>` 目录中。

```plantuml
@startuml

skinparam TitleFontName JetBrains Sans
skinparam TitleFontStyle plain
skinparam TitleFontSize 16
skinparam DefaultTextAlignment left

title
  <IDE directory>
  |_ plugins
    |_ sample.jar // (Plugin distribution) //
      |_ com
        |_ company
          |_ Sample.class // (Class ""com.company.Sample"") //
      |_ ...
      |_ META-INF
        |_ plugin.xml // (Plugin Configuration File) //
        |_ pluginIcon.svg // (Plugin Logo) //
        |_ pluginIcon_dark.svg // (Plugin Logo, dark variant) //
end title
@enduml
```

### 有依赖插件 {id=plugin-with-dependencies}

插件 `<path>.jar</path>` 文件与所有必需的捆绑库一起放置在插件的 "root" 文件夹下的 `<path>/lib</path>` 文件夹中。

来自 `<path>/lib</path>` 文件夹的所有 JAR 文件会自动添加到类路径中（另见 [Plugin Class Loaders](plugin_class_loaders.md)）。

<snippet id="doNotRepackageLibraries">

> 不要将库重新打包到主插件 JAR 文件中。
> 否则，[Plugin Verifier](verifying_plugin_compatibility.md) 将会因未解析的类和方法产生误报。
>
{title="不要重新打包库" style="warning"}

</snippet>

```plantuml
@startuml

skinparam TitleFontName JetBrains Sans
skinparam TitleFontStyle plain
skinparam TitleFontSize 16
skinparam DefaultTextAlignment left

title
  <IDE directory>
  |_ plugins
    |_ sample
      |_ lib
        |_ lib_foo.jar // (Required bundled library #1) //
        |_ lib_bar.jar // (Required bundled library #2) //
        |_ ...
        |_ sample.jar // (Plugin distribution) //
          |_ com
            |_ company
              |_ Sample.class // (Class ""com.company.Sample"") //
          |_ ...
          |_ META-INF
            |_ plugin.xml // (Plugin Configuration File) //
            |_ pluginIcon.svg // (Plugin Logo) //
            |_ pluginIcon_dark.svg // (Plugin Logo, dark variant) //
end title
@enduml
```
