# 发布插件

<!-- Copyright 2000-2023 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

<link-summary>手动或使用 Gradle IntelliJ 插件将插件发布到 JetBrains Marketplace。</link-summary>

当你的插件准备好后，你可以将它发布到[JetBrains Marketplace](https://plugins.jetbrains.com)插件仓库，这样其他用户就可以在IDE中安装它。
即使项目使用Gradle设置，首次插件发布也必须[手动上传](#上传插件到JetBrainsMarketplace)。

在发布插件之前，请确保它遵循[](plugin_user_experience.md)中的所有建议。
为了获得最佳展示效果，请参考[插件概述页](https://plugins.jetbrains.com/docs/marketplace/plugin-overview-page.html)的准则。
"繁忙的插件开发者 第2集" 中更详细地讨论了[优化JetBrains Marketplace插件页面的5个提示](https://youtu.be/oB1GA9JeeiY?t=52)。
有关小部件和徽章，请参考[](marketing.md)。

> 如果你计划将插件发布到[JetBrains Marketplace](https://plugins.jetbrains.com)之外的仓库，请参考[](custom_plugin_repository.md)文档。

> 如果你的插件随附[额外的库](plugin_content.md#有依赖插件)，请不要将它们重新打包到主插件存档中。
> 否则，[Plugin Verifier](verifying_plugin_compatibility.md)将出现未解析类和方法的误报。
>
{style="warning"}

## 将插件上传到 JetBrains Marketplace {id="上传插件到JetBrainsMarketplace"}

在发布插件之前，请确保它已经签名。
关于生成正确证书和配置签名流程的详细信息，请参考[插件签名](plugin_signing.md)文章。

<procedure title="创建 JetBrains 帐户">

为了将你的插件上传到[JetBrains Marketplace](https://plugins.jetbrains.com)，你必须使用你的个人 JetBrains 帐户登录。

1. 打开[JetBrains 帐户中心](https://account.jetbrains.com)，然后点击<control>Create Account</control>（<control>创建帐户</control>）。
2. 在打开的<control>Create JetBrains Account</control>（<control>创建 JetBrains 帐户</control>）表单中填写所有字段，然后点击<control>Register</control>（<control>注册</control>）。

</procedure>

<procedure title="上传插件">

要将你的插件上传到 JetBrains Marketplace：

1. 使用你的个人 JetBrains 帐户[登录 JetBrains Marketplace](https://plugins.jetbrains.com/author/me)。
2. 在打开的个人资料页面上，点击<control>Add new plugin</control>（<control>添加新插件</control>）。
3. 填写打开的<control>Add new plugin</control>（<control>添加新插件</control>）表单，然后点击<control>Add the plugin</control>（<control>添加插件</control>）按钮来上传你的插件。

另请参阅[Marketplace Docs](https://plugins.jetbrains.com/docs/marketplace/uploading-a-new-plugin.html)（[市场文档](https://plugins.jetbrains.com/docs/marketplace/uploading-a-new-plugin.html)）。

</procedure>

### 上传新版本

新版本可以在插件的详细页面上手动上传，请参阅[Marketplace 文档](https://plugins.jetbrains.com/docs/marketplace/plugin-updates.html)以获取详细信息。

## 使用 Gradle 发布插件 { id="使用Gradle发布插件" }

一旦你已经[配置了 Gradle 支持](configuring_plugin_project.md) 并且至少上传了一次插件到插件存储库，你就可以自动构建和部署你的插件到[JetBrains Marketplace](https://plugins.jetbrains.com)。

### 构建分发 {id="构建分发"}

对于初始上传、手动分发或本地安装，请调用[`buildPlugin`](tools_gradle_intellij_plugin.md#tasks-buildplugin) Gradle 任务以创建插件分发包。
如果你已经配置了你的项目依赖于[](plugin_signing.md)，则应使用[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) 任务。

生成的 ZIP 文件位于<path>build/distributions</path>目录下，然后可以通过[<ui-path>从磁盘安装插件</ui-path>](https://www.jetbrains.com/help/idea/managing-plugins.html#install_plugin_from_disk)操作进行安装，
或者上传到[](custom_plugin_repository.md)。

### 向 Gradle 提供个人访问令牌

要将插件部署到 JetBrains Marketplace，你需要提供你的个人访问令牌，你可以在你的个人资料页面的[My Tokens](https://plugins.jetbrains.com/author/me/tokens)部分找到它。

要创建一个新的令牌，请提供其名称，然后点击<control>Generate Token</control>（<control>生成令牌</control>) 按钮。
一个新的令牌将被创建并显示在下方。

> 在你关闭此页面之前复制它并保存在安全的位置。
> 这是令牌可见的唯一时间。
>
{style="note"}

这部分描述了通过 Gradle 使用两种选项提供你的 _个人访问令牌_ ：

* 环境变量，
* 作为 Gradle 任务的参数。

#### 使用环境变量

首先定义一个环境变量，例如：

```bash
export ORG_GRADLE_PROJECT_intellijPublishToken='YOUR_TOKEN'
```

> 在 macOS 系统上，设置在<path>.bash_profile</path>中的环境变量只对你从 bash 启动的进程可见。
> 可以被所有进程看到的环境变量需要在[Environment.plist](https://developer.apple.com/library/archive/qa/qa1067/_index.html)中定义。
>
{style="note"}

现在在运行本地[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin)任务的运行配置中提供环境变量。
要做到这一点，创建一个 Gradle 运行配置（如果还没有创建），选择你的 Gradle 项目，指定[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin)任务，然后添加环境变量。

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


请注意，您仍然需要在 Gradle 属性中放置一些默认值（可以为空），否则您将收到编译错误。

#### 使用 Gradle 任务的参数 {id="使用Gradle任务的参数"}

与使用环境变量一样，你也可以将你的令牌作为参数传递给Gradle任务。
例如，你可以提供参数：

```bash
-Dorg.gradle.project.intellijPublishToken=YOUR_TOKEN
```
在这种情况下，你仍然需要在 Gradle 属性中设置一些默认值。

你可以通过命令行传递参数，或者将参数放在你的 Gradle 运行配置的参数中。

### 使用 Gradle 部署插件 {id="使用Gradle部署插件"}

在部署插件时的第一步是确认它能够正常工作。
你可以通过在目标IDE的新实例上[从磁盘安装你的插件](https://www.jetbrains.com/help/idea/managing-plugins.html)来验证这一点。

#### 签署插件

市场签名旨在确保在发布和交付流程中插件不会被修改。
在版本`1.x`中，Gradle IntelliJ 插件提供了[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin)任务，该任务将在[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin)之前自动执行。

有关生成正确证书和配置[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin)任务的更多详细信息，请查看[Plugin Signing](plugin_signing.md)文章。

#### 发布插件

一旦你确信插件按照预期工作，确保插件的版本已更新，因为 JetBrains Marketplace 不会接受具有相同版本的多个构件。

要将插件的新版本部署到 JetBrains Marketplace，请调用[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) Gradle 任务。

现在，检查你的插件在[JetBrains Marketplace](https://plugins.jetbrains.com/)上的最新版本。
如果部署成功，任何目前在可用版本的 IntelliJ 平台上安装了你的插件的用户在验证后将被通知有新的可用更新。

### 指定发布渠道 {id="指定发布渠道"}

你还可以通过配置[`publishPlugin.channels`](tools_gradle_intellij_plugin.md#tasks-publishplugin-channels)属性将插件部署到你选择的发布通道。
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

当为空时，这将使用默认的插件存储库，对所有[JetBrains Marketplace](https://plugins.jetbrains.com/)用户可用。
然而，你可以将它发布到一个任意命名的通道。
这些非默认的发布通道被视为独立的存储库。

在使用非默认发布通道时，用户需要在他们的 IDE 中配置一个新的[自定义插件存储库](https://www.jetbrains.com/help/idea/managing-plugins.html#repos)来安装你的插件。
例如，如果你指定`publishPlugin.channels = ['canary']`，那么用户需要添加 `https://plugins.jetbrains.com/plugins/canary/list` 存储库以安装插件并接收更新。

流行的通道名称包括：
* `alpha`: https://plugins.jetbrains.com/plugins/alpha/list
* `beta`: https://plugins.jetbrains.com/plugins/beta/list
* `eap`: https://plugins.jetbrains.com/plugins/eap/list

关于可用配置选项的更多信息，请参阅[IntelliJ Gradle 插件的文档](tools_gradle_intellij_plugin.md#tasks-publishplugin)。
