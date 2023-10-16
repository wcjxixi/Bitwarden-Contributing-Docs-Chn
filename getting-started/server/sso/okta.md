# \*Okta

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/sso/okta)
{% endhint %}

本指南将使用 Okta 设置基本的 SSO 验证。这仅用于测试，不应用于任何生产环境。

## 先决条件 <a href="#prerequisites" id="prerequisites"></a>

1. Bitwarden 服务器已安装和配置，并且以下服务器项目正运行：
   * Identity
   * API
   * SSO（位于 `server/bitwarden_license/src/Sso` )
2. 访问 Bitwarden Vault 中的开发集合

## 步骤 <a href="#steps" id="steps"></a>

在浏览器中执行以下步骤访问 Okta：

1. 在开发集合中启动「[oktapreview.com](http://oktapreview.com/)」登录项目
2. 使用这些凭据登录
3. 展开左侧菜单面板上的「目录」部分
4. 点击「人员」
5. 点击「添加人员」，为自己创建个人档案
6. 点击顶部菜单栏中的「应用程序」
7. 现在您应该会看到我们的测试应用程序列表。为了进行本地测试，请点击「Bitwarden Test 2」应用程序。该应用程序的客户端凭证和其他信息将被列出，您可以在后续步骤中使用。

打开一个单独的浏览器标签，在本地 Bitwarden 网络密码库中配置 SSO：

1. 登录网络密码库并导航到要为其启用 SSO 的组织
2. 点击 `Settings` ，输入组织的标识符。该标识符应是唯一的，可以只是组织名称。单击「保存」。
3. 转到 `Manage > Single Sign-On` 并输入以下信息：

| 类型                                 | OpenId Connect                                                            |
| ---------------------------------- | ------------------------------------------------------------------------- |
| Authority                          | [https://dev-836655.oktapreview.com](https://dev-836655.oktapreview.com/) |
| Client ID                          | 从 Okta 复制而来                                                               |
| Client Secret                      | 从 Okta 复制而来                                                               |
| OIDCS Redirect Behavior            | Redirect GET                                                              |
| Get Claims From User Info Endpoint | ✅                                                                         |

现在您可以使用 SSO 登录了。

{% hint style="info" %}
您必须在客户端端点中设置密码库 URL
{% endhint %}

## 示例配置 <a href="#example-configuration" id="example-configuration"></a>

未显示的字段应为空。

{% embed url="https://contributing.bitwarden.com/assets/images/config-7c9523f3f9b7edbf6d5651c0ae739346.png" %}
