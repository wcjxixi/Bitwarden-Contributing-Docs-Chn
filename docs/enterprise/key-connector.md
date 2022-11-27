# Key Connector

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/enterprise/key-connector)
{% endhint %}

{% hint style="info" %}
如果您是 Key Connector 的新手，您应该首先阅读[帮助中心文档](https://help.ppgg.in/login-with-sso/about-key-connector)以了解其工作原理。
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* 运行在自托管配置中的[本地开发服务器](../server/guide.md)
* 配置了 SSO 的企业组织
* 本地运行的 [Web 密码库](../clients/web-vault/)
* [.NET Core 5.0 SDK](https://www.microsoft.com/net/download/core)

### macOS <a href="#macos" id="macos"></a>

macOS 需要更新 SSL 库，否则您将收到「No usable version of libssl was found」的错误。

1、安装 [Homebrew](https://brew.sh/)

2、安装 OpenSSL 包：

```bash
brew install openssl
```

3、将所需的环境变量设置为指向 OpenSSL 库：

```bash
echo 'DYLD_LIBRARY_PATH="/usr/local/opt/openssl@1.1/lib"' >> ~/.zshrc
```

4、如果您是从终端运行的 Key Connector，请重新启动终端以确保更新的 `.zshrc` 设置被应用

## 设置和配置 <a href="#setup-and-configuration" id="setup-and-configuration"></a>

克隆存储库：

```bash
git clone https://github.com/bitwarden/key-connector.git
```

### 配置密钥和用户机密 <a href="#configure-keys-and-user-secrets" id="configure-keys-and-user-secrets"></a>

{% hint style="danger" %}
这些是推荐的开发设置，不适合生产使用。如果需要，[README](https://github.com/bitwarden/key-connector/blob/master/README.md) 中提供了更多配置选项。
{% endhint %}

1、打开终端并导航到本地 Key Connector 存储库中的 `dev` 文件夹

2、生成一个新的 RSA 密钥对（如果它们位于 `dev` 文件夹中，git 将忽略它们）：

```bash
openssl req -x509 -newkey rsa:4096 -sha256 -nodes -keyout bwkc.key -out bwkc.crt -subj "/CN=Bitwarden Key Connector" -days 36500

openssl pkcs12 -export -out ./bwkc.pfx -inkey bwkc.key -in bwkc.crt -passout pass:{Password}}
```

3、创建您自己的示例用户机密副本：

```bash
cp secrets.json.example secrets.json
```

4、编辑 `secrets.json` 并插入缺失的信息，包括本地存储库的路径和数据库文件的密码。

5、（可选）默认情况下，Key Connector 将使用本地自托管端点 - `https://localhost:8081` 用于 Web 密码库，`http://localhost:33657` 用于身份验证。如果您遵循本文档，则无需进行任何更改。但是，如果您的设置需要不同的端点，您可以在您的用户机密中设置它们，如下所示：

```json
  "keyConnectorSettings": {
    "webVaultUri": "https://localhost:8081",
    "identityServerUri": "http://localhost:33657"
  }
```

6、保存并应用用户机密：

```bash
pwsh setup_secrets.ps1
```

{% hint style="info" %}
如果您在设置用户机密时需要帮助，请参阅[用户机密参考](../server/user-secrets.md)。
{% endhint %}

### 配置组织 <a href="#configure-organization" id="configure-organization"></a>

打开您的本地 Web 密码库并将您的企业组织配置为使用以下设置：

* 策略：单一组织和单一登录身份验证
* 单点登录：
  * 成员解密选项：Key Connector
  * Key Connector URL：`http://localhost:5000`

## 运行和调试 <a href="#running-and-debugging" id="running-and-debugging"></a>

您现在已准备好开始在您的开发环境中使用 Key Connector 了！

{% tabs %}
{% tab title="Visual Studio" %}
使用 Visual Studio 打开解决方案文件 (`bitwarden-key-connector.sln`)，然后点击「Play」按钮。

启动 Key Connector 后，使用非管理员或所有者的帐户使用 SSO 登录。新用户将自动加入 Key Connector，现有用户将被提示删除其主密码。
{% endtab %}

{% tab title="CLI" %}
从存储库根目录运行以下命令：

```bash
dotnet run --project src/KeyConnector --configuration Development
```

macOS 需要 `--configuration` 标志才能使用正确的 SSL 库。

启动 Key Connector 后，使用非管理员或所有者的帐户使用 SSO 登录。新用户将自动加入 Key Connector，现有用户将被提示删除其主密码。
{% endtab %}
{% endtabs %}
