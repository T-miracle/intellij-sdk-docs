<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 发布插件

<link-summary>发布插件到 JetBrains Marketplace 手动或使用 Gradle IntelliJ 插件。</link-summary>

当插件准备就绪时，可以将其发布到 [JetBrains Marketplace](https://plugins.jetbrains.com) 插件仓库，以便其他用户可以在他们的 IDE 中安装它。

> When publishing a plugin to a repository _other than_ the [JetBrains Marketplace](https://plugins.jetbrains.com), refer to the [](custom_plugin_repository.md) documentation.

The first plugin publication must always be [uploaded manually](#uploading-a-plugin-to-jetbrains-marketplace).

<procedure title="发布前检查清单">

在发布插件之前，请确保它：

- 符合 [](plugin_user_experience.md) 的所有建议
- 符合 [插件概述页面](https://plugins.jetbrains.com/docs/marketplace/plugin-overview-page.html) 的所有要求

The webinar _Busy Plugin Developers. Episode 2_ discusses [5 tips for optimizing JetBrains Marketplace plugin page](https://youtu.be/oB1GA9JeeiY?t=52) in more detail.

另请参阅 [](marketing.md) 关于小部件和徽章。

</procedure>

<include from="plugin_content.md" element-id="doNotRepackageLibraries"/>

## 将插件上传至 JetBrains Marketplace {id=uploading-a-plugin-to-jetbrains-marketplace}

在发布插件之前，请确保已签名插件。
有关生成适当证书和配置签名过程的详细信息，请查阅 [插件签名](plugin_signing.md) 文章。

<procedure title="Creating a JetBrains Account">

要将插件上传至 [JetBrains Marketplace](https://plugins.jetbrains.com)，请使用您的个人 JetBrains 账户登录。

1. 打开 [JetBrains 账户中心](https://account.jetbrains.com) 并点击 <control>创建账户</control>。
2. 填写打开的 <control>创建 JetBrains 账户</control> 表单中的所有字段，然后点击 <control>注册</control>。

</procedure>

<procedure title="上传插件">

To upload a plugin to [JetBrains Marketplace](https://plugins.jetbrains.com):

1. 使用您的个人 JetBrains 账户 [登录 JetBrains Marketplace](https://plugins.jetbrains.com/author/me)。
2. 在打开的个人资料页面上，点击 <control>添加新插件</control>。
3. 填写打开的 <control>添加新插件</control> 表单，并点击 <control>添加插件</control> 按钮来上传插件。

另请参阅 [Marketplace 文档](https://plugins.jetbrains.com/docs/marketplace/uploading-a-new-plugin.html)。

</procedure>

### 上传新版本 {id=upload-new-version}

New versions can be uploaded manually on the plugin's detail page, see [Marketplace Docs](https://plugins.jetbrains.com/docs/marketplace/plugin-updates.html) for details.
See [](#deploying-a-plugin-with-gradle) on how to publish new versions using Gradle.

## 使用 Gradle 发布插件 {id=publishing-plugin-with-gradle}

Once [Gradle support](configuring_plugin_project.md) has been configured, and the plugin has been [uploaded manually](#uploading-a-plugin-to-jetbrains-marketplace) to the plugin repository at least once,
it can be built and deployed to the [JetBrains Marketplace](https://plugins.jetbrains.com) automatically using dedicated Gradle tasks.

> **2.x** refers to [](tools_intellij_platform_gradle_plugin.md) and **1.x** to [](tools_gradle_intellij_plugin.md) in the following sections.
>

### 构建分发包 {id=building-distribution}

For the initial upload, manual distribution, or local installation, invoke the `buildPlugin` Gradle task
(Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#buildPlugin), [1.x](tools_gradle_intellij_plugin.md#tasks-buildplugin)) to create the plugin distribution.
If the project is configured to rely on [](plugin_signing.md), use the `signPlugin` task instead
(Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#signPlugin), [1.x](tools_gradle_intellij_plugin.md#tasks-signplugin)).

The resulting ZIP file is located in <path>build/distributions</path> and can then be installed in the IDE via [<ui-path>Install Plugin from Disk...</ui-path>](https://www.jetbrains.com/help/idea/managing-plugins.html#install_plugin_from_disk) action
or uploaded to a [](custom_plugin_repository.md).

### 提供个人访问令牌给 Gradle {id=providing-personal-access-token-to-gradle}

To deploy a plugin to the JetBrains Marketplace, supply the Personal Access Token, which can be found on your profile page in the [My Tokens](https://plugins.jetbrains.com/author/me/tokens) section.

要创建新令牌，请提供其名称并点击<control>生成令牌</control>按钮。
新令牌将被创建并显示在下方。

> 在关闭此页面之前，请复制令牌并保存在安全的位置。
> 这是令牌可见的唯一时机。
>
{style="warning"}

本节描述了两种通过 Gradle 向 _个人访问令牌_ 提供方式：

* 环境变量，
* Gradle 任务的参数。

#### 使用环境变量 {id=using-environment-variables}

首先定义一个环境变量，例如：

<tabs group="gradlePluginVersion">

<tab title="IntelliJ Platform Gradle Plugin (2.x)" group-key="2.x">

```bash
export ORG_GRADLE_PROJECT_intellijPlatform.publishing.token='YOUR_TOKEN'
```

</tab>

<tab title="Gradle IntelliJ Plugin (1.x)" group-key="1.x">

```bash
export ORG_GRADLE_PROJECT_intellijPublishToken='YOUR_TOKEN'
```

</tab>
</tabs>

> 在 macOS 系统上，设置在 <path>.bash_profile</path> 中的环境变量只对从 bash 运行的进程可见。
> 要让所有进程可见的环境变量需要在 [Environment.plist](https://developer.apple.com/library/archive/qa/qa1067/_index.html) 中定义。
>
{style="note" title="macOS Note"}

Now provide the environment variable in the run configuration for running the `publishPlugin` task locally
(Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#publishPlugin), [1.x](tools_gradle_intellij_plugin.md#tasks-publishplugin)).
To do so, create a Gradle run configuration (if not already done), select the Gradle project, specify the
`publishPlugin` task, and then add the environment variable.

#### IntelliJ Platform Gradle Plugin (2.x)

```kotlin
publishPlugin {
  token.set(System.getenv("ORG_GRADLE_PROJECT_intellijPlatform.publishing.token"))
}
```

#### Gradle IntelliJ Plugin (1.x)

{collapsible="true" default-state="collapsed"}

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
publishPlugin {
  token.set(System.getenv("ORG_GRADLE_PROJECT_intellijPublishToken"))
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
publishPlugin {
  token = System.getenv("ORG_GRADLE_PROJECT_intellijPublishToken")
}
```

</tab>
</tabs>

请注意，仍然需要在 Gradle 属性中设置一些默认值（可以为空）。否则可能会出现编译错误。

#### 使用 Gradle 任务的参数 {id=using-parameters-for-the-gradle-task}

#### Using Parameters for the Gradle Task

Like using environment variables, the token can also be passed as a parameter to the Gradle task.
For example, provide the parameter on the command line or by putting it in the arguments of the Gradle run configuration.

<tabs group="gradlePluginVersion">

<tab title="IntelliJ Platform Gradle Plugin (2.x)" group-key="2.x">

```bash
-Dorg.gradle.project.intellijPlatform.publishing.token=YOUR_TOKEN
```

</tab>

<tab title="Gradle IntelliJ Plugin (1.x)" group-key="1.x">

```bash
-Dorg.gradle.project.intellijPublishToken=YOUR_TOKEN
```

</tab>
</tabs>

请注意，在这种情况下，仍然需要在 Gradle 属性中设置一些默认值（可以为空）。

### 使用 Gradle 部署插件 {id=deploying-plugin-with-gradle}

在部署插件时的第一步是确认其能够正确运行。
通过在目标IDE的新实例中[从磁盘安装插件](https://www.jetbrains.com/help/idea/managing-plugins.html)，来验证插件的正确性。

#### 签署插件 {id=signing-a-plugin}

The Marketplace signing is designed to ensure that plugins are not modified over the course of the publishing and delivery pipeline.
The `signPlugin` Gradle task
(Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#signPlugin), [1.x](tools_gradle_intellij_plugin.md#tasks-signplugin)),
will be executed automatically right before the `publishPlugin` task
(Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#publishPlugin), [1.x](tools_gradle_intellij_plugin.md#tasks-publishplugin)).

For more details on generating a proper certificate and configuring the `signPlugin` task, see [](plugin_signing.md).

#### 发布插件 {id=publishing-a-plugin}

一旦插件按预期工作，请确保插件版本已更新，因为 JetBrains Marketplace 不会接受具有相同版本的多个构件。

To deploy a new version of the plugin to the JetBrains Marketplace, invoke the `publishPlugin` Gradle task.

现在在[JetBrains Marketplace](https://plugins.jetbrains.com/)上检查插件的最新版本。
如果成功部署，任何已安装该插件的用户（在可用的 IntelliJ 平台版本上）将在验证更新后立即收到新版本可用的通知。

### 指定发布通道 {id=specifying-a-release-channel}

It's possible to deploy plugins to a chosen release channel by configuring the `publishPlugin.channels` property
(Reference: [2.x](tools_intellij_platform_gradle_plugin_tasks.md#publishPlugin-channels), [1.x](tools_gradle_intellij_plugin.md#tasks-publishplugin-channels)).

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
publishPlugin {
  channels.set(listOf("beta"))
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
publishPlugin {
  channels = ['beta']
}
```

</tab>
</tabs>

当通道为空时，默认使用所有[JetBrains Marketplace](https://plugins.jetbrains.com/)用户可用的默认插件仓库。
然而，也可以将其发布到任意命名的通道。
这些非默认发布通道被视为独立的仓库。

When using a non-default release channel, users need to configure a new [custom plugin repository](https://www.jetbrains.com/help/idea/managing-plugins.html#repos) in their IDE to install the plugin.

For example, when specifying `'canary'` as channel name, users will need to add the `https://plugins.jetbrains.com/plugins/canary/list` repository to install the plugin and receive updates.

常见的通道名称包括：

* `alpha`: https://plugins.jetbrains.com/plugins/alpha/list
* `beta`: https://plugins.jetbrains.com/plugins/beta/list
* `eap`: https://plugins.jetbrains.com/plugins/eap/list
