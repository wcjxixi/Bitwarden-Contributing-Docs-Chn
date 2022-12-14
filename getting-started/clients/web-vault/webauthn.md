# WebAuthn

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/web-vault/webauthn)
{% endhint %}

{% hint style="info" %}
如果您需要在本地测试 WebAuthn 身份验证，此页面包含额外的设置说明。
{% endhint %}

[WebAuthn](https://webauthn.guide/) 规范要求使用一个有效的域名。由于 `localhost` 不满足这个要求，因此您需要将本地实例配置为使用域名。

有多种方法可以做到这一点。但是，最简单的方法是修改操作系统的主机文件，将其环回到 `127.0.0.1`。

## 配置 <a href="#configuration" id="configuration"></a>

Webpack 默认通过阻止主机名来防止 DNS 重新绑定攻击。但是，我们可以在 Web 环境配置 JSON 文件中指定被允许的特定主机名。

1、在 `web/config/` 文件夹中创建一个 `local.json` 文件

2、将「bitwarden.test」添加为 `allowedHosts` 实体：

```json
{
  "dev": {
    "allowedHosts": ["bitwarden.test"]
  }
}
```

！！！注意：如果您正在运行应用程序，则必须重新启动它才能使配置更改生效。

### 主机文件 <a href="#hosts-file" id="hosts-file"></a>

{% hint style="warning" %}
您需要管理员权限才能编辑此文件。
{% endhint %}

不同操作系统的主机文件的位置略有不同。

{% tabs %}
{% tab title="Windows" %}
```
C:\Windows\System32\drivers\etc\hosts
```
{% endtab %}

{% tab title="macOS" %}
```
/etc/hosts
```
{% endtab %}
{% endtabs %}

使用您选择的文本编辑器打开文件。并附加以下行。

```
127.0.0.1 bitwarden.test
```

### 用户机密 <a href="#user-secrets" id="user-secrets"></a>

除了修改主机文件外，还需要创建或更新服务器中 API 和 Identity 项目的[用户机密](../../server/user-secrets.md) `globalSettings:baseServiceUri:vault` 以映射域名。例如：

```json
{
  ...
   "globalSettings":{
      "baseServiceUri":{
         "vault":"https://bitwarden.test:8080"
      }
   },
   ...
}
```

### 测试 <a href="#testing" id="testing"></a>

您现在应该可以通过访问 [https://bitwarden.test:8080](https://bitwarden.test:8080) 在本地实例上测试 WebAuthn 了。
