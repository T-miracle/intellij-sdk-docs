<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 发布插件

<link-summary>发布插件到 JetBrains Marketplace 手动或使用 Gradle IntelliJ Plugin。</link-summary>

当插件准备就绪时，可以将其发布到 [JetBrains Marketplace](https://plugins.jetbrains.com) 插件仓库，以便其他用户可以在他们的 IDE 中安装它。

> 当将插件发布到 _非_ [JetBrains Marketplace](https://plugins.jetbrains.com) 的仓库时，请参阅 [](custom_plugin_repository.md) 文档。

首次发布插件时，必须始终 [手动上传](#uploading-a-plugin-to-jetbrains-marketplace)。

<procedure title="发布前检查清单">

在发布插件之前，请确保它：

- 符合 [](plugin_user_experience.md) 的所有建议
- 符合 [插件概述页面](https://plugins.jetbrains.com/docs/marketplace/plugin-overview-page.html) 的所有要求

网络研讨会 _繁忙的插件开发者。第二集_ 更详细地讨论了 [优化 JetBrains Marketplace 插件页面的 5 个技巧](https://youtu.be/oB1GA9JeeiY?t=52)。

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

新版本可以通过插件的详细页面手动上传，详情请参见 [Marketplace 文档](https://plugins.jetbrains.com/docs/marketplace/plugin-updates.html)。  
有关如何使用 Gradle 发布新版本，请参见 [](#deploying-plugin-with-gradle)。

## 使用 Gradle 发布插件 {id=publishing-plugin-with-gradle}

一旦 [Gradle 支持](creating_plugin_project.md) 配置完成，并且插件至少被 [手动上传](#uploading-a-plugin-to-jetbrains-marketplace) 至插件仓库一次，  
就可以使用专用的 Gradle 任务自动构建并部署到 [JetBrains Marketplace](https://plugins.jetbrains.com)。

> **2.x** 指的是 [](tools_intellij_platform_gradle_plugin.md)，**1.x** 指的是 [](tools_gradle_intellij_plugin.md) 在以下章节中。
>

### 构建分发包 {id=building-distribution}

对于初次上传、手动分发或本地安装，调用 `buildPlugin` Gradle 任务  
(参考：[2.x](tools_intellij_platform_gradle_plugin_tasks.md#buildPlugin)，[1.x](tools_gradle_intellij_plugin.md#tasks-buildplugin)) 来创建插件分发包。  
如果项目配置为依赖 [](plugin_signing.md)，请使用 `signPlugin` 任务代替  
(参考：[2.x](tools_intellij_platform_gradle_plugin_tasks.md#signPlugin)，[1.x](tools_gradle_intellij_plugin.md#tasks-signplugin))。

生成的 ZIP 文件位于 <path>build/distributions</path>，然后可以通过 [<ui-path>Install Plugin from Disk...</ui-path>](https://www.jetbrains.com/help/idea/managing-plugins.html#install_plugin_from_disk) 操作安装到 IDE 中，  
或上传到 [](custom_plugin_repository.md)。

### 提供个人访问令牌给 Gradle {id=providing-personal-access-token-to-gradle}

要将插件部署到 JetBrains Marketplace，提供个人访问令牌（Personal Access Token），该令牌可以在您的个人资料页面的 [My Tokens](https://plugins.jetbrains.com/author/me/tokens) 部分找到。

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

```bash
export ORG_GRADLE_PROJECT_intellijPlatformPublishingToken='YOUR_TOKEN'
```

> 在 macOS 系统上，设置在 <path>.bash_profile</path> 中的环境变量只对从 bash 运行的进程可见。
> 要让所有进程可见的环境变量需要在 [Environment.plist](https://developer.apple.com/library/archive/qa/qa1067/_index.html) 中定义。
>
{style="note" title="macOS Note"}

现在，在运行 `publishPlugin` 任务的本地运行配置中提供环境变量  
(参考：[2.x](tools_intellij_platform_gradle_plugin_tasks.md#publishPlugin)，[1.x](tools_gradle_intellij_plugin.md#tasks-publishplugin))。  
为此，创建一个 Gradle 运行配置（如果尚未创建），选择 Gradle 项目，指定 `publishPlugin` 任务，然后添加环境变量。

<tabs group="gradlePluginVersion">

<tab title="IntelliJ Platform Gradle Plugin (2.x)" group-key="2.x">

```kotlin
intellijPlatform {
  publishing {
    token = providers.gradleProperty("intellijPlatformPublishingToken")
  }
}
```

</tab>

<tab title="Gradle IntelliJ Plugin (1.x)" group-key="1.x">

```kotlin
tasks {
  publishPlugin {
    token = providers.gradleProperty("intellijPlatformPublishingToken")
  }
}
```

</tab>
</tabs>

请注意，仍然需要在 Gradle 属性中设置一些默认值（可以为空）。否则可能会出现编译错误。

#### 使用 Gradle 任务的参数

#### 使用 Gradle 任务的参数 {id=using-parameters-for-the-gradle-task}

与使用环境变量类似，令牌也可以作为参数传递给 Gradle 任务。  
例如，可以在命令行中提供该参数，或者将其放入 Gradle 运行配置的参数中。

```bash
-PintellijPlatformPublishingToken=YOUR_TOKEN
```

请注意，在这种情况下，仍然需要在 Gradle 属性中设置一些默认值（可以为空）。

### 使用 Gradle 部署插件 {id=deploying-plugin-with-gradle}

在部署插件时的第一步是确认其能够正确运行。
通过在目标IDE的新实例中[从磁盘安装插件](https://www.jetbrains.com/help/idea/managing-plugins.html)，来验证插件的正确性。

#### 签署插件 {id=signing-a-plugin}

Marketplace 签名旨在确保插件在发布和交付过程中不会被修改。  
`signPlugin` Gradle 任务  
(参考：[2.x](tools_intellij_platform_gradle_plugin_tasks.md#signPlugin)，[1.x](tools_gradle_intellij_plugin.md#tasks-signplugin))，  
将在 `publishPlugin` 任务  
(参考：[2.x](tools_intellij_platform_gradle_plugin_tasks.md#publishPlugin)，[1.x](tools_gradle_intellij_plugin.md#tasks-publishplugin)) 之前自动执行。

有关生成正确证书和配置 `signPlugin` 任务的更多细节，请参见 [](plugin_signing.md)。

#### 发布插件 {id=publishing-a-plugin}

一旦插件按预期工作，请确保插件版本已更新，因为 JetBrains Marketplace 不会接受具有相同版本的多个构件。

要将插件的新版本部署到 JetBrains Marketplace，调用 `publishPlugin` Gradle 任务。

现在在[JetBrains Marketplace](https://plugins.jetbrains.com/)上检查插件的最新版本。
如果成功部署，任何已安装该插件的用户（在可用的 IntelliJ 平台版本上）将在验证更新后立即收到新版本可用的通知。

### 指定发布通道 {id=specifying-a-release-channel}

<tabs group="gradlePluginVersion">

<tab title="IntelliJ Platform Gradle Plugin (2.x)" group-key="2.x">

可以通过配置 [`intellijPlatform.publishing.channels`](tools_intellij_platform_gradle_plugin_extension.md#intellijPlatform-publishing-channels) 扩展属性，将插件部署到指定的发布渠道。

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
intellijPlatform {
  publishing {
    channels = listOf("beta")
  }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
intellijPlatform {
  publishing {
    channels = ['beta']
  }
}
```

</tab>
</tabs>

</tab>
<tab title="Gradle IntelliJ Plugin (1.x)" group-key="1.x">

可以通过配置 [`publishPlugin.channels`](tools_gradle_intellij_plugin.md#tasks-publishplugin-channels) 任务属性，将插件部署到指定的发布渠道。

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
tasks {
  publishPlugin {
    channels = listOf("beta")
  }
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
tasks {
  publishPlugin {
    channels = ['beta']
  }
}
```

</tab>
</tabs>

</tab>
</tabs>

当通道为空时，默认使用所有[JetBrains Marketplace](https://plugins.jetbrains.com/)用户可用的默认插件仓库。
然而，也可以将其发布到任意命名的通道。
这些非默认发布通道被视为独立的仓库。

当使用非默认发布渠道时，用户需要在其 IDE 中配置一个新的 [自定义插件仓库](https://www.jetbrains.com/help/idea/managing-plugins.html#repos) 来安装插件。

例如，当指定 `'canary'` 作为渠道名称时，用户需要添加 `https://plugins.jetbrains.com/plugins/canary/list` 仓库，以便安装插件并接收更新。

常见的通道名称包括：

* `alpha`: https://plugins.jetbrains.com/plugins/alpha/list
* `beta`: https://plugins.jetbrains.com/plugins/beta/list
* `eap`: https://plugins.jetbrains.com/plugins/eap/list
