# 单点登录 (SSO)

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/sso/)
{% endhint %}

## 设置和配置 <a href="#setup-and-configuration" id="setup-and-configuration"></a>

您可以使用以下方式为开发设置 SSO：

* [本地 IdP](local.md)（推荐）
* [Okta](okta.md)

## 桌面客户端 <a href="#desktop-client" id="desktop-client"></a>

{% hint style="info" %}
可能不需要这种变通方法 - 即使在开发版本中，SSO 也能正常工作。先试试看！
{% endhint %}

桌面客户端会打开浏览器以完成 SSO 身份验证流程。通过 IdP 验证后，浏览器将重定向到 `bitwarden://` URI。该 URI 通常会打开桌面客户端，但如果你的桌面客户端没有正确安装（例如，因为你是从源代码运行的），这可能不起作用。它可能只会打开一个空的 Electron 窗口（如果安装了正式版客户端，也可能会打开）。

您可以按以下方法解决这个问题：

1. 通过 SSO 流程导航，直到浏览器窗口打开
2. 打开开发工具，点击「网络」选项卡
3. 使用 IDP 完成登录
4. 当 Bitwarden 客户端无法启动时，请返回浏览器并点击最后一个网络请求。该请求应该是向 `localhost` 发送的，并以 `callback?client_id=desktop` 开始...
5. 复制 `location` 响应头中的 URI。它应以 `bitwarden://sso-callback?code=` 开头。

下面是一个示例：

{% embed url="https://contributing.bitwarden.com/assets/images/devtools-a7381ad4c1c96a9bfac9f42e7a285236.png" %}

1. 返回桌面客户端，打开开发工具
2. 在控制台中粘贴以下命令并按回车键：`window.location.href = '<paste the URI here>'`
3. 桌面客户端现在应该已完成了 SSO 登录
