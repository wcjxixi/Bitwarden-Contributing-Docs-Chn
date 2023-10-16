# \*SCIM

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/scim)
{% endhint %}

SCIM 是跨域身份管理系统 (System for Cross-domain Identity Management) 的缩写。SCIM 使用 Bitwarden 服务器和身份提供商 (IdP) 之间的直接链接，将用户和群组从身份提供商同步到 Bitwarden 服务器。

{% hint style="info" %}
在某些方面，SCIM 与目录连接器 (Directory Connector) 相似。不过，Directory Connector 的工作方式是轮询更改（例如执行计划同步），而 SCIM 的工作方式是在发生更改时将更改直接推送到 Bitwarden。
{% endhint %}

## 要求​ <a href="#requirements" id="requirements"></a>

* 一个[本地开发服务器](guide.md)
* [Web 密码库](../clients/web-vault/)
* 一个企业组织
* Mailcatcher 或类似的本地邮件服务，这样您就不会在测试邀请时向真实电子邮件地址发送垃圾邮件（这包含在服务器设置指南中）

## 步骤​ <a href="#steps" id="steps"></a>

### 为您的组织启用 SCIM ​ <a href="#enable-scim-for-your-organization" id="enable-scim-for-your-organization"></a>

1、登录到 Web 密码库然后导航到您的组织 -> 设置 -> SCIM 配置

2、勾选「启用 SCIM」然后点击保存。此时您的 SCIM URL 和 API 密钥应该会显示出来。保留此窗口打开以供后面参考

### 启动 SCIM 项目​ <a href="#start-the-scim-project" id="start-the-scim-project"></a>

3、在您的本地服务器存储库中启动 SCIM 项目：

```bash
cd bitwarden_license/src/Scim
dotnet run
```

4、通过导航到 `http://localhost:44559/alive` 来验证 SCIM 项目是否已成功启动

### 公开您的本地端口​ <a href="#expose-your-local-port" id="expose-your-local-port"></a>

SCIM 要求 SCIM 项目和 IdP 之间的直接连接。因此，您需要将本地端口公开到互联网。请遵循 [Ingress Tunnels](tunnel.md) 上的任一个指南来执行此操作。默认公开的端口是 `44559`。

### 配置 IdP ​ <a href="#configure-idp" id="configure-idp"></a>

本指南使用 JumpCloud 作为测试 IdP。Okta 同样也适合用于测试，您可以使用任何支持 SCIM 的 IdP。

如果需要，您还可以参考 [JumpCloud SCIM 帮助文档](https://support.jumpcloud.com/support/s/article/Custom-SCIM-Identity-Management)。

1. 创建一个 JumpCloud 账户然后登录 [JumpCloud 管理界面](https://console.jumpcloud.com/login/admin)
2. 点击左侧的「SSO」，然后点击加号按钮以创建一个新应用程序
3. 在应用程序列表中搜索「Bitwarden」，然后点击「配置」
4. 在「常规信息」选项卡中，添加显示名称
5. 在「身份管理」选项卡中，向下滚动到「配置设置」部分然后完成如下内容：
   * **API 类型**：SCIM API
   * **SCIM 版本**：SCIM 2.0
   * **基本 URL**：使用 Web 密码库中的 SCIM URL，但要将 `localhost` 替换为您的 ngrok 转发 url。例如，`https://abcd-123-456-789.au.ngrok.io/v2/d24f1dcd-d3fb-4810-977e-adf00009f0ca`
   * **令牌密钥**：使用 Web 密码库中的 SCIM API 密钥
   * **测试用户电子邮件**：使用还没有关联用户账户的任何电子邮件地址。当您测试连接时，JumpCloud 将使用它来执行测试操作
6. &#x20;点击「测试连接」并等待 JumpCloud 完成测试。您应该在 ngrok 窗口中看到 HTTP 请求。
7. 测试通过后点击「激活」。
8. 在「用户群组」选项卡中，将此连接与「所有用户」群组链接。

### 测试​ <a href="#test" id="test"></a>

您应该已经设置好并准备就绪！您可以通过在 JumpCloud 中添加和移除用户来测试 SCIM 集成。确保您的用户属于「所有用户」群组。您应该能看到您的更改几乎立即反映在 Bitwarden 中。

您还可以在 JumpCloud 中挂起和激活用户，这对应于 Bitwarden 中的撤销和恢复操作。
