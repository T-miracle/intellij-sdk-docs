# 将 DevKit 插件迁移到 Gradle

<!-- Copyright 2000-2022 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>将现有的基于 DevKit 的插件迁移到 Gradle 设置。</link-summary>

> 请参阅[改进插件 #3 – 从 DevKit 迁移到 Gradle 构建系统](https://blog.jetbrains.com/platform/2021/12/migration-from-devkit-to-the-gradle-build-system/)逐步演练的博客文章。

将使用旧 DevKit 方法创建的插件（可用于[创建主题](creating_theme_project.md)）转换为基于 Gradle 的插件项目，
可以使用 <control>New Project（新建项目）</control> 向导来创建围绕现有基于 DevKit 的项目的基于 Gradle 的项目：
* 确保包含基于 Devkit 的 IntelliJ 平台插件项目的目录可以在必要时完全恢复。
* 删除基于 DevKit 的项目的所有工件：
    * <path>.idea</path> 目录
    * <path>[modulename].iml</path> 文件
    * <path>out</path> 目录
* 以 Gradle [源集](https://docs.gradle.org/current/userguide/java_plugin.html#sec:java_project_layout) 格式排列项目目录中的现有源文件。
* 使用 <control>[新建项目](creating_plugin_project.md#create-ide-plugin)</control> 向导，就像从头开始创建一个新的基于 Gradle 的插件项目一样。在<control>New Project</control>页面上选择<control>IDE Plugin</control>生成器并设置以下值：
    * <control>Group</control> 到初始源集中的现有包。
    * <control>Artifact</control> 为现有插件的名称。
    * <control>Name</control> 为现有插件所在目录的名称，例如如果插件项目基目录是 <path>/Users/john/Projects/old_plugin</path>，则它所在目录的名称应该是 <path>old_plugin</path>。
    * <control>Location</control> 为插件父目录的名称，例如如果插件项目基目录是 <path>/Users/john/Projects/old_plugin</path>，则应该是 <path>/Users/john/Projects</path>。
* 单击 <control>Finish（完成）</control> 创建新的基于 Gradle 的插件。
* 使用 Gradle [源集](https://www.jetbrains.com/help/idea/gradle.html#gradle_source_sets) 根据需要 [添加更多模块](https://www.jetbrains.com/help/idea/gradle.html#gradle_add_module) 。
