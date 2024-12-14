<!-- Copyright 2000-2024 JetBrains s.r.o. and contributors. Use of this source code is governed by the Apache 2.0 license. -->

# 插件签名

<link-summary>插件签名确保最终用户获得实际发布到JetBrains Marketplace的未经修改的插件分发。</link-summary>

插件签名是在2021.2版本发布周期中引入的一种机制，旨在提高[JetBrains Marketplace](https://plugins.jetbrains.com)以及我们所有基于IntelliJ的IDE的安全性。

市场签名的设计目的是确保插件在发布和交付流程中不被修改。
如果作者没有对插件进行签名或者其证书被撤销，安装过程中 IDE 将显示警告对话框。

在我们这一方面，我们将检查密钥的公共部分是否存在，并验证签名。
这类似于[Google上传密钥](https://developer.android.com/studio/publish/app-signing#generate-key)的机制。

## 签名如何运作

为确保文件未被修改，文件将被签名两次 - 首先由插件作者，然后由 JetBrains Marketplace。

插件作者的签名验证流程如下：

- 插件作者生成密钥对~~并将公共部分上传到 JetBrains Marketplace~~（此功能尚不可用）。
- 构建工具在组装过程中对插件文件进行签名。
- 用户将插件文件上传到 JetBrains Marketplace。
- JetBrains Marketplace 检查用户配置文件中是否存在公钥。
- JetBrains Marketplace 验证签名。
- JetBrains 签名验证流程如下：
  - JetBrains CA 在此处充当事实来源。
  - 其公共部分将被添加到 IDE Java TrustStore 中，而私有部分将仅被使用一次来生成一个中间证书。
  - JetBrains CA 的私钥是超级保密的；实际上，我们已经说得太多了。

中间证书颁发一个将用于签署插件的证书。
通过这种方式，将有可能重新生成这个证书，而不需要访问 JetBrains CA 的绝密私钥。
中间证书的私钥被颁发并存储在 AWS 证书管理器中，没有任何应用程序可以访问它；人们的访问权限也受到限制。
因此，现在我们有了一个基于 AWS 的中间 CA。
中间证书的公开部分将与签名证书一起添加到插件文件中。

签署插件的证书也被安全地存储。
JetBrains Marketplace 使用 AWS KMS 作为签名提供程序来签署插件文件。

## 签名方式

为了提供适合插件签名的方法，我们引入了 [Marketplace ZIP Signer](https://github.com/JetBrains/marketplace-zip-signer) 库。
如果您的项目基于Gradle，可以使用 [](tools_gradle_intellij_plugin.md#tasks-signplugin) 提供的 `signPlugin` 任务来执行它。
此外，也可以使用独立的 [CLI工具](#cli-tool)。

这两种方法都需要已存在私有证书密钥。

### 生成私钥 { id="生成私钥" }

要生成 RSA <path>private.pem</path> 私钥，请在终端中运行 `openssl genpkey` 命令，如下所示：

```bash
openssl genpkey\
  -aes-256-cbc\
  -algorithm RSA\
  -out private_encrypted.pem\
  -pkeyopt rsa_keygen_bits:4096
```

之后，需要将其转换为 RSA 形式：

```bash
openssl rsa\
  -in private_encrypted.pem\
  -out private.pem
````

此时，生成的`<path>private.pem</path>`内容应该被提供给[`signPlugin.privateKey`](tools_gradle_intellij_plugin.md#tasks-signplugin-privatekey)属性。
所提供的密码应该被指定为[`signPlugin.password`](tools_gradle_intellij_plugin.md#tasks-signplugin-password)属性在[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin)配置中。

下一步，我们将生成一个 <path>chain.crt</path> 证书链：

```bash
openssl req\
  -key private.pem\
  -new\
  -x509\
  -days 365\
  -out chain.crt
```

`<path>chain.crt</path>` 文件的内容将被用于[`signPlugin.certificateChain`](tools_gradle_intellij_plugin.md#tasks-signplugin-certificatechain)属性。

> 当上传公钥到JetBrains Marketplace可用时，将会添加关于基于私钥生成公钥的信息。

### Gradle IntelliJ Plugin {id=gradle-intellij-plugin}

在版本`1.x`中，Gradle IntelliJ 插件提供了[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin)任务，当指定了[`signPlugin.certificateChain`](tools_gradle_intellij_plugin.md#tasks-signplugin-certificatechain)和[`signPlugin.privateKey`](tools_gradle_intellij_plugin.md#tasks-signplugin-privatekey)签名属性时，该任务将在[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin)任务之前自动执行。
否则，它将被跳过。

示例 [`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) 任务配置可能如下所示：

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
signPlugin {
  certificateChain.set("""
    -----BEGIN CERTIFICATE-----
    MIIElgCCAn4CCQDo83LWYj2QSTANBgkqhkiG9w0BAQsFADANMQswCQYDVQQGEwJQ
    ...
    gdZzxCN8t1EmH8kD2Yve6YKGFCRAIIzveEg=
    -----END CERTIFICATE-----
  """.trimIndent())

  privateKey.set("""
    -----BEGIN RSA PRIVATE KEY-----
    MIIJKgIBAAKCAgEAwU8awS22Rw902BmwVDDBMlTREX440BAAVM40NW3E0lJ7YTJG
    ...
    EnNBfIVFhh6khisKqTBWSEo5iS2RYJcuZs961riCn1LARztiaXL4l17oW8t+Qw==
    -----END RSA PRIVATE KEY-----
  """.trimIndent())

  password.set("8awS22%#3(4wVDDBMlTREX")
}

publishPlugin {
  token.set("perm:a961riC....l17oW8t+Qw==")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
signPlugin {
  certificateChain = """
    -----BEGIN CERTIFICATE-----
    MIIElgCCAn4CCQDo83LWYj2QSTANBgkqhkiG9w0BAQsFADANMQswCQYDVQQGEwJQ
    ...
    gdZzxCN8t1EmH8kD2Yve6YKGFCRAIIzveEg=
    -----END CERTIFICATE-----
  """.stripIndent()

  privateKey = """
    -----BEGIN RSA PRIVATE KEY-----
    MIIJKgIBAAKCAgEAwU8awS22Rw902BmwVDDBMlTREX440BAAVM40NW3E0lJ7YTJG
    ...
    EnNBfIVFhh6khisKqTBWSEo5iS2RYJcuZs961riCn1LARztiaXL4l17oW8t+Qw==
    -----END RSA PRIVATE KEY-----
  """.stripIndent()

  password = "8awS22%#3(4wVDDBMlTREX"
}

publishPlugin {
  token = "perm:a961riC....l17oW8t+Qw=="
}
```

</tab>
</tabs>

> 不要将您的凭据提交给版本控制系统！为了避免这种情况，您可以使用环境变量，例如：
> ```
> token.set(providers.environmentVariable("PUBLISH_TOKEN"))
> password.set(providers.environmentVariable("PRIVATE_KEY_PASSWORD"))
> ```
>
{style="warning"}

与直接提供密钥和证书链内容的 [`signPlugin.privateKey`](tools_gradle_intellij_plugin.md#tasks-signplugin-privatekey) 和
[`signPlugin.certificateChain`](tools_gradle_intellij_plugin.md#tasks-signplugin-certificatechain) 属性不同，  
也可以指定包含密钥和证书链内容的文件路径。  
为此，请使用 [`signPlugin.privateKeyFile`](tools_gradle_intellij_plugin.md#tasks-signplugin-privatekeyfile) 和
[`signPlugin.certificateChainFile`](tools_gradle_intellij_plugin.md#tasks-signplugin-certificatechainfile) 属性。

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
signPlugin {
  certificateChainFile.set(file("certificate/chain.crt"))
  privateKeyFile.set(file("certificate/private.pem"))
  password.set("8awS22%#3(4wVDDBMlTREX")
}

publishPlugin {
  token.set("perm:a961riC....l17oW8t+Qw==")
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
signPlugin {
  certificateChainFile = file("certificate/chain.crt")
  privateKeyFile = file("certificate/private.pem")
  password = "8awS22%#3(4wVDDBMlTREX"
}

publishPlugin {
  token = "perm:a961riC....l17oW8t+Qw=="
}
```

</tab>
</tabs>

### 向 IDR 提供密匙

为了避免在项目配置中存储硬编码值，本地开发最合适的方法是使用 _Run/Debug Configuration (运行/调试配置)_ 中提供的环境变量。
为了指定像`PUBLISH_TOKEN`以及[`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin)任务所需的值，请修改您的Gradle配置如下：

<tabs group="languages">
<tab title="Kotlin" group-key="kotlin">

```kotlin
signPlugin {
  certificateChain.set(providers.environmentVariable("CERTIFICATE_CHAIN"))
  privateKey.set(providers.environmentVariable("PRIVATE_KEY"))
  password.set(providers.environmentVariable("PRIVATE_KEY_PASSWORD"))
}

publishPlugin {
  token.set(providers.environmentVariable("PUBLISH_TOKEN"))
}
```

</tab>
<tab title="Groovy" group-key="groovy">

```groovy
signPlugin {
  certificateChain = providers.environmentVariable("CERTIFICATE_CHAIN")
  privateKey = providers.environmentVariable("PRIVATE_KEY")
  password = providers.environmentVariable("PRIVATE_KEY_PASSWORD")
}

publishPlugin {
  token = providers.environmentVariable("PUBLISH_TOKEN")
}
```

</tab>
</tabs>

在[`publishPlugin`](tools_gradle_intellij_plugin.md#tasks-publishplugin) Gradle 任务的<control>Run/Debug Configuration（运行/调试配置）</control>中，使用相关的环境变量名称提供<control>Environment Variables（环境变量）</control>：

![运行/调试 配置环境变量](plugin_singing_env_variables.png)

> 注意，私钥和证书链都是多行值。
> 在提供单行字段到 <control>Environment Variables</control> 面板之前，需要先使用 Base64 编码进行转换。
>
> [`signPlugin.privateKey`](tools_gradle_intellij_plugin.md#tasks-signplugin-privatekey) 和 [`signPlugin.certificateChain`](tools_gradle_intellij_plugin.md#tasks-signplugin-certificatechain) 属性会自动检测并解码 Base64 编码的值。
>
> {style="warning"}

### CLI 命令行工具 {id=cli-tool}

如果您不依赖于Gradle IntelliJ插件，例如在处理[主题](developing_themes.md)时，需要使用CLI工具。

要获取最新的 Marketplace ZIP Signer CLI 工具，请访问[JetBrains/marketplace-zip-signer](https://github.com/JetBrains/marketplace-zip-signer/releases) GitHub 发布页面。
在下载了`<path>marketplace-zip-signer-cli.jar</path>`后，执行如下：

```bash
java -jar marketplace-zip-signer-cli.jar sign\
  -in "unsigned.zip"\
  -out "signed.zip"\
  -cert-file "/path/to/chain.crt"\
  -key-file "/path/to/private.pem"\
  -key-pass "PRIVATE_KEY_PASSWORD"
```

## 签署自定义存储库

在自定义仓库上签署托管的插件可以在仓库和安装之间增加信任。
不过，与市场不同，自定义仓库不会使用 JetBrains 密钥重新签署插件。
相反，可以使用受信任的私人 CA（证书颁发机构）或自签名证书来签署和验证插件。

### 验证

在查看如何签署插件之前，让我们首先回顾一下在使用非 JetBrains 证书时验证是如何工作的。
从 2021.2 版本开始，IntelliJ 基于的 IDE 在验证时会检查插件是否由 JetBrains CA 证书签署，或者用户通过 <ui-path>Settings（设置） | Plugins（插件） | Manage Plugin Certificates（管理插件证书）</ui-path> 提供的任何公钥签署。
在 2021.2.1 版本中，添加了一个系统属性：`intellij.plugins.truststore`，指向一个受信任的 JKS 信任存储。
在验证过程中，插件的公钥从签名中提取。
将链中的最后一个证书条目与上述存储中的证书进行匹配。

### 使用受信任的内部 CA

如果有内部 CA 可用，您可以使用它来生成用于签署的证书。
在选择这种方法时，证书链在链的末尾包括根 CA 的公钥。

采用这种方法，可能已经存在并可以使用现有的内部 TrustStore。
在选择 TrustStore 时，请确保 CA 受限于您信任的内部 CA。
使用带有公共 CA 的 TrustStore 可能会使用户面临攻击风险。

如果无法将 TrustStore 添加到用户的环境中，用户也可以将根 CA 的公钥添加到 <ui-path>Settings | Plugins | Manage Plugin Certificates</ui-path> 中。

### 使用自签名证书

如果没有内部 CA，使用自签名证书是一个选择。
要生成密钥和公钥，请参见：[生成私钥](#生成私钥)。

如果要向用户提供一个 TrustStore，您可以使用 `keytool` 生成一个，使用公钥：

```bash
keytool -import -alias IdeaPlugin -file chain.crt -keystore pluginKeystore.jks -storepass changeit
```
（注意：TrustStore 密码必须保持为 `changeit`）

否则，用户可以手动将公钥添加到 <ui-path>Settings | Plugins | Manage Plugin Certificates</ui-path>。

## 插件签名验证 {id=plugin-signature-verification}

要验证插件的签名，您可以使用 [](tools_gradle_intellij_plugin.md#tasks-verifypluginsignature) 提供的 `verifyPluginSignature` 任务。

要验证插件的签名，您可以使用 [`verifyPluginSignature`](tools_gradle_intellij_plugin.md#tasks-verifypluginsignature) 任务。

默认情况下，此任务将使用与前一部分中提供给 [`signPlugin`](tools_gradle_intellij_plugin.md#tasks-signplugin) 任务的相同证书链。

要使用[CLI 工具](#cli-tool)验证签名，请执行以下 `verify` 命令：

```bash
java -jar marketplace-zip-signer-cli.jar verify\
  -in "signed.zip"\
  -cert "/path/to/chain.crt"
```
