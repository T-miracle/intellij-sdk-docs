<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 发布插件

<link-summary>发布插件到 JetBrains Marketplace 手动或使用 Gradle IntelliJ 插件。</link-summary>

当插件准备就绪时，可以将其发布到 [JetBrains Marketplace](https://plugins.jetbrains.com) 插件仓库，以便其他用户可以在他们的 IDE 中安装它。

第一次插件发布必须始终是 [手动上传](#uploading-a-plugin-to-jetbrains-marketplace)。

<procedure title="发布前检查清单">

在发布插件之前，请确保它：

- 符合 [](plugin_user_experience.md) 的所有建议
- 符合 [插件概述页面](https://plugins.jetbrains.com/docs/marketplace/plugin-overview-page.html) 的所有要求

_忙碌的插件开发者。第2集_ 讨论了优化 JetBrains Marketplace 插件页面的 [5 个提示](https://youtu.be/oB1GA9JeeiY?t=52)。

另请参阅 [](marketing.md) 关于小部件和徽章。

</procedure>

> 如果要发布到除 [JetBrains Marketplace](https://plugins.jetbrains.com) 之外的其他仓库，请参阅 [](custom_plugin_repository.md) 文档。

> 如果插件随附有 [额外的库](plugin_content.md#plugin-with-dependencies)，请勿将它们重新打包到主插件存档中。
> 否则，[插件验证器](verifying_plugin_compatibility.md) 将会因为未解析的类和方法而产生错误的结果。

{style="warning"}

## 将插件上传至 JetBrains Marketplace {id=uploading-a-plugin-to-jetbrains-marketplace}

在发布插件之前，请确保已签名插件。
有关生成适当证书和配置签名过程的详细信息，请查阅 [插件签名](plugin_signing.md) 文章。

<procedure title="创建 JetBrains 账户">

要将插件上传至 [JetBrains Marketplace](https://plugins.jetbrains.com)，请使用您的个人 JetBrains 账户登录。

1. 打开 [JetBrains 账户中心](https://account.jetbrains.com) 并点击 <control>创建账户</control>。
2. 填写打开的 <control>创建 JetBrains 账户</control> 表单中的所有字段，然后点击 <control>注册</control>。

</procedure>

<procedure title="上传插件">

将插件上传至 JetBrains Marketplace：

1. 使用您的个人 JetBrains 账户 [登录 JetBrains Marketplace](https://plugins.jetbrains.com/author/me)。
2. 在打开的个人资料页面上，点击 <control>添加新插件</control>。
3. 填写打开的 <control>添加新插件</control> 表单，并点击 <control>添加插件</control> 按钮来上传插件。

另请参阅 [Marketplace 文档](https://plugins.jetbrains.com/docs/marketplace/uploading-a-new-plugin.html)。

</procedure>

### 上传新版本 {id=upload-new-version}

可以在插件详细页面上手动上传新版本，请查看[市场文档](https://plugins.jetbrains.com/docs/marketplace/plugin-updates.html)获取详细信息。

## 使用 Gradle 发布插件 {id=publishing-plugin-with-gradle}

一旦完成了[Gradle支持的配置](configuring_plugin_project.md)，并且插件至少被[上传到](#uploading-a-plugin-to-jetbrains-marketplace) JetBrains 插件仓库一次，就可以自动构建并部署到[JetBrains Marketplace](https://plugins.jetbrains.com)。

### 构建分发包 {id=building-distribution}

对于初始上传、手动分发或本地安装，请调用[`buildPlugin`](tools_gradle_intellij_plugin.md#tasks-buildplugin) Gradle 任务来创建插件分发包。
如果项目配置依赖于[](plugin_signing.md)，则使用[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin)任务。

生成的 ZIP 文件位于 <path>build/distributions</path>，然后可以通过[<ui-path>从磁盘安装插件...</ui-path>](https://www.jetbrains.com/help/idea/managing-plugins.html#install_plugin_from_disk)操作安装，
或上传到[](custom_plugin_repository.md)。

### 提供个人访问令牌给 Gradle {id=providing-personal-access-token-to-gradle}

要将插件部署到 JetBrains Marketplace，请提供个人访问令牌，该令牌可以在您的[个人资料页面](https://plugins.jetbrains.com/author/me/tokens)的"My Tokens"部分找到。

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
export ORG_GRADLE_PROJECT_intellijPublishToken='YOUR_TOKEN'
```

> 在 macOS 系统上，设置在 <path>.bash_profile</path> 中的环境变量只对从 bash 运行的进程可见。
> 要让所有进程可见的环境变量需要在 [Environment.plist](https://developer.apple.com/library/archive/qa/qa1067/_index.html) 中定义。
>
{style="note"}

现在在本地运行[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin)任务时，提供这个环境变量作为运行配置。
为此，创建一个 Gradle 运行配置（如果尚未创建），选择 Gradle 项目，指定[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin)任务，然后添加环境变量。

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

与使用环境变量类似，令牌也可以作为参数传递给 Gradle 任务。
例如，在命令行上提供参数

```bash
-Dorg.gradle.project.intellijPublishToken=YOUR_TOKEN
```

或者将其放在 Gradle 运行配置的参数中。

请注意，在这种情况下，仍然需要在 Gradle 属性中设置一些默认值（可以为空）。

### 使用 Gradle 部署插件 {id=deploying-plugin-with-gradle}

在部署插件时的第一步是确认其能够正确运行。
通过在目标IDE的新实例中[从磁盘安装插件](https://www.jetbrains.com/help/idea/managing-plugins.html)，来验证插件的正确性。

#### 签署插件 {id=signing-a-plugin}

市场签署旨在确保在发布和交付流程中插件未被修改。
在版本 `1.x` 中，Gradle IntelliJ 插件提供了 [`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) 任务，该任务将在 [`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) 之前自动执行。

有关生成正确证书和配置 [`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) 任务的详细信息，请查阅 [插件签署](plugin_signing.md) 文章。

#### 发布插件 {id=publishing-a-plugin}

一旦插件按预期工作，请确保插件版本已更新，因为 JetBrains Marketplace 不会接受具有相同版本的多个构件。

要将插件的新版本部署到 JetBrains Marketplace，请调用 [`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) Gradle 任务。

现在在[JetBrains Marketplace](https://plugins.jetbrains.com/)上检查插件的最新版本。
如果成功部署，任何已安装该插件的用户（在可用的 IntelliJ 平台版本上）将在验证更新后立即收到新版本可用的通知。

### 指定发布通道 {id=specifying-a-release-channel}

可以通过配置 [`publishPlugin.channels`](tools_gradle_intellij_plugin.md#tasks-publishplugin-channels) 属性将插件部署到所选的发布通道。
例如：

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

使用非默认发布通道时，用户需要在他们的 IDE 中配置一个新的[自定义插件仓库](https://www.jetbrains.com/help/idea/managing-plugins.html#repos)来安装插件。
例如，当指定 `publishPlugin.channels = ['canary']` 时，用户需要添加 `https://plugins.jetbrains.com/plugins/canary/list` 仓库来安装插件并接收更新。

常见的通道名称包括：

* `alpha`: https://plugins.jetbrains.com/plugins/alpha/list
* `beta`: https://plugins.jetbrains.com/plugins/beta/list
* `eap`: https://plugins.jetbrains.com/plugins/eap/list

关于可用配置选项的更多信息，请参阅[IntelliJ Gradle 插件文档](tools_gradle_intellij_plugin.md#tasks-publishplugin)。
