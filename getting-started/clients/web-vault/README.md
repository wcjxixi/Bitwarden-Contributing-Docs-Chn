# Web Vault

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/web-vault/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* 在开始之前，您必须已完成[客户端存储库设置说明](../)。
* 如果您想针对本地服务器实例进行开发，请参阅[服务器设置指南](../../server/guide.md)。

### SSL 证书 <a href="#ssl-certificate" id="ssl-certificate"></a>

建议为本地开发生成有效的 SSL 证书，而不是使用共享的开发证书。这将防止浏览器发出无效证书的警告，并正确支持 WebAuthn，因为在证书出错的网站上，WebAuthn 会被阻止。请遵循 [mkcert 安装说明](https://github.com/FiloSottile/mkcert#installation)。

```bash
# 生成并安装根 CA
mkcert --install

# 生成对 localhost、127.0.0.1 和 bitwarden.test 有效的证书
mkcert -cert-file dev-server.local.pem -key-file dev-server.local.pem localhost 127.0.0.1 bitwarden.test
```

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

1、构建并运行 Web Vault。

```bash
cd apps/web
npm run build:oss:watch
```

以上针对本地 bitwarden 实例。关于如何针对官方服务器的信息，请参阅[官方服务器](./#official-server)。

2、打开浏览器并导航到 `https://localhost:8080`。您应该会看到 Bitwarden 登录界面。

3、如果您想更进一步，请启动本地服务器并设置好 MailCatcher。现在让我们创建一个账户并验证它。

* 创建一个新账户（这可以是一个假的邮箱）
* 登录新账户
* 点击「验证电子邮件地址」
* 导航至 `http://localhost:1080/`
* 打开无回复电子邮件然后验证您的电子邮件地址

{% hint style="info" %}
您也可以使用 `build:bit:selfhost:watch` 和 `build:os:selfhost:watch` 命令在自托管模式下运行 Web Vault。
{% endhint %}

## 配置 API 端点 <a href="#configuring-api-endpoints" id="configuring-api-endpoints"></a>

默认情况下，Web Vault 将使用您的本地开发服务器（运行在默认端口的 `localhost` 上）。您也可以改用官方的 Bitwarden 服务器或配置自定义端点。

### 官方服务器 <a href="#official-server" id="official-server"></a>

要使用官方的 Bitwarden 服务器，请按照上面的构建说明进行操作，但使用以下命令运行 Web Vault：

```bash
ENV=cloud npm run build:oss:watch
```

### 自定义端点 <a href="#custom-endpoints" id="custom-endpoints"></a>

您可以通过创建具有以下结构的 `config/local.json` 文件来手动设置 API 端点设置：

```json
{
    "dev": {
        "proxyApi": "<http://your-api-url>",
        "proxyIdentity": "<http://your-identity-url>",
        "proxyEvents": "<http://your-events-url>",
        "proxyNotifications": "<http://your-notifications-url>",
        "allowedHosts": ["hostnames-to-allow-in-webpack"],
    },
    "urls": {

    }
}
```

* `dev`：来自前面的 `/api -> <http://your-api-url>` 的代理流量。
* `urls`：直接调用远程服务 [<mark style="background-color:green;">注1</mark>](#user-content-fn-1)[^1]。注意：这可能会导致 CORS 标头出现问题。

***

<mark style="background-color:green;">注1</mark>：`urls`：遵守 [`EnvironmentService` 中的类型定义](https://github.com/bitwarden/clients/blob/master/libs/common/src/abstractions/environment.service.ts)。

[^1]: `urls`：遵守 [`EnvironmentService` 中的类型定义](https://github.com/bitwarden/clients/blob/master/libs/common/src/abstractions/environment.service.ts)。
