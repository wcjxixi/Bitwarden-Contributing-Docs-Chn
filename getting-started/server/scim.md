# =\*SCIM

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/scim)
{% endhint %}

SCIM 是_跨域身份管理系统_ (System for Cross-domain Identity Management) 的缩写，使用 Bitwarden 服务器和身份提供商 (IdP) 之间的直接链接，将用户和群组从身份提供商同步到 Bitwarden 服务器。

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

本指南使用 JumpCloud 作为测试 IdP。Okta 同样也适合用于测试，您可以使用任何支持 SCIM 的 IdP。

如果需要，您还可以参考 [JumpCloud SCIM 帮助文档](https://support.jumpcloud.com/support/s/article/Custom-SCIM-Identity-Management)。

1. 创建z并登录JumpCloud管理界面
2. 单击左侧的「SSO」，然后单击加号按钮创建新应用程序
3. 在应用程序列表中搜索「Bitwarden」，然后单击「配置」
4. 在「常规信息」选项卡中，添加显示名称
5. 在「身份管理」选项卡中，向下滚动到「配置设置」部分并按如下所示完成：
   * API 类型：SCIM API
   * SCIM 版本：SCIM 2.0
   * 基本 URL：使用 Web 保管库中的 SCIM URL，但将 localhost 替换为您的 ngrok 转发 url。例如，https://abcd-123-456-789.au.ngrok.io/v2/d24f1dcd-d3fb-4810-977e-adf00009f0ca
   * 令牌密钥：使用网络保管库中的 SCIM API 密钥
   * 测试用户电子邮件：使用还没有用户帐户的任何电子邮件地址。当您测试连接时，JumpCloud将使用它来执行测试操作
6. &#x20;单击「测试连接」并等待 JumpCloud 完成测试。您应该在 ngrok 窗口中看到 HTTP 请求。
7. 测试通过后单击「激活」。
8. 在「用户组」选项卡中，将此连接与「所有用户」组链接。





### 配置 IdP ​ <a href="#configure-idp" id="configure-idp"></a>

### 测试​ <a href="#test" id="test"></a>
