# Web Vault

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/clients/web-vault/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* 在开始之前，您必须完成[客户端存储库设置说明](../)。
* 如果您想针对本地服务器实例进行开发，请参阅[服务器设置指南](../../server/guide.md)。

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

1、构建并运行 Web Vault。

{% tabs %}
{% tab title="社区开发人员" %}
```
cd apps/web
npm run build:oss:watch
```
{% endtab %}

{% tab title="Bitwarden 开发人员" %}
```
cd apps/web
npm run build:bit:watch
```
{% endtab %}
{% endtabs %}

这将针对本地的 bitwarden 实例。关于如何针对官方服务器的信息，请见下文。

2、打开浏览器并导航到 `https://localhost:8080`。

{% hint style="info" %}
您也可以使用 `build:bit:selfhost:watch` 和 `build:os:selfhost:watch` 命令在自托管模式下运行 Web Vault。
{% endhint %}

## 配置 API 端点 <a href="#configuring-api-endpoints" id="configuring-api-endpoints"></a>

默认情况下，Web Vault 将使用您的本地开发服务器（运行在默认端口的 `localhost` 上）。您也可以改用官方的 Bitwarden 服务器或配置自定义端点。

### 官方服务器 <a href="#official-server" id="official-server"></a>

要使用官方的 Bitwarden 服务器，请按照上面的构建说明进行操作，但使用以下命令运行 Web Vault：

{% tabs %}
{% tab title="社区开发人员" %}
```
ENV=cloud npm run build:oss:watch
```
{% endtab %}

{% tab title="Bitwarden 开发人员" %}
```
ENV=cloud npm run build:bit:watch
```
{% endtab %}
{% endtabs %}

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

* `dev`：来自 ex 的代理流量。 `/api -> <http://your-api-url>`。
* `urls`：直接调用远程服务。注意：这可能会导致 CORS 标头出现问题。 urls 遵循 [jslib 中的 Urls 类型](https://github.com/bitwarden/jslib/blob/master/common/src/abstractions/environment.service.ts)。
