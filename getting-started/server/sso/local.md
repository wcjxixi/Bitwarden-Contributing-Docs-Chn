# 本地 IdP

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/sso/local)
{% endhint %}

本文将向您展示如何为测试目的设置本地 SSO 身份提供程序 (IdP)。

这里使用 [Docker Test SAML 2.0 Identity Provider。](https://github.com/kenchan0130/docker-simplesamlphp)

## 先决条件 <a href="#prerequisites" id="prerequisites"></a>

1. Bitwarden 服务器已安装和配置，并运行以下服务器项目：
   * Identity
   * API
   * SSO（位于 `server/bitwarden_license/src/Sso`)
2. 本地网络客户端正在运行

## 配置 IdP <a href="#configure-idp" id="configure-idp"></a>

1、打开本地网络密码库然后导航至「组织」→「设置」→「单点登录」

2、勾选「允许 SSO 身份验证」复选框

3、编写并输入一个 SSO 标识符

4、选择「SAML 2.0」作为 SSO 类型。先不要保存或退出此页面，稍后再回来查看

5、打开一个新的终端，导航至服务器存储库中的 `dev` 文件夹，例如

```bash
cd ~/Projects/server/dev
```

6、打开 `.env` 文件，根据网络密码库 SSO 配置页面上的「SP 实体 ID」和「断言消费者服务 (ACS) URL」值设置以下环境变量：

```bash
IDP_SP_ENTITY_ID=http://localhost:51822/saml2
IDP_SP_ACS_URL=http://localhost:51822/saml2/yourOrgIdHere/Acs
```

{% hint style="info" %}
您应该已经在初始服务器设置期间创建了此 `.env` 文件。如果需要，您可以参考 `.env.example` 文件。
{% endhint %}

7、（可选）您可以生成证书来签署 SSO 请求。您可以使用为您选择的操作系统制作的脚本来完成此操作。

```bash
# Mac
./create_certificates_mac.sh

# Windows
.\create_certificates_windows.ps1

# Linux
./create_certificates_linux.sh
```

将指纹（例如 `0BE8A0072214AB37C6928968752F698EEC3A68B5`）粘贴到 `globalSettings` > `identityServer` > `certificateThumbprint` 下的 `Secrets.json` 文件中。按[此处所示](../guide.md#configure-user-secrets)更新您的机密。

8、复制提供的 `authsources.php.example` 文件，其中包含您的 IdP 用户配置。

```bash
cp authsources.php.example authsources.php
```

默认情况下，该文件配置了两个用户：`user1` 和 `user2`，并且都有密码`password`。您可以按照此格式添加或修改用户，或者仅使用默认值。有关自定义此文件的更多信息，请参阅[此处](https://github.com/kenchan0130/docker-simplesamlphp#advanced-usage)。

9、启动 docker 容器：

```bash
docker-compose --profile idp up -d
```

10、您可以通过导航至 [http://localhost:8090/simplesaml](http://localhost:8090/simplesaml)，然后「身份验证」→「测试已配置的身份验证源」→「`example-userpass`」来测试用户配置。您应该可以使用配置的用户登录了。

## 配置 Bitwarden <a href="#configure-bitwarden" id="configure-bitwarden"></a>

1、回到打开 SSO 配置页面的窗口

2、在「SAML 身份提供程序配置」(SAML Identity Provider Configuration) 部分填写以下值：

* 实体 ID：

```
http://localhost:8090/simplesaml/saml2/idp/metadata.php
```

* 单点登录服务 URL：

```
http://localhost:8090/simplesaml/saml2/idp/SSOService.php
```

* X509 公共证书：打开一个新标签页，导航到上述实体 ID URL，即可获得该证书。它将打开（或下载）一个 XML 文件。复制并粘贴 `<ds:X509Certificate>` 标记之间的值（它看起来应该像一个 B64 编码的字符串）

3、保存 SSO 配置

现在，您的 SSO 已准备就绪！

## 更新 IdP 配置 <a href="#updating-the-idp-configuration" id="updating-the-idp-configuration"></a>

### 用户 <a href="#users" id="users"></a>

要添加或更改用户，只需编辑 `authsources.php`。您的更改将立即生效，但当前已通过身份验证的用户必须退出登录，其账户更改才能生效。

要注销用户身份，请访问 [http://localhost:8090/simplesaml/module.php/core/authenticate.php?as=example-userpass](http://localhost:8090/simplesaml/module.php/core/authenticate.php?as=example-userpass) 然后点击「注销」。或者，您也可以使用私密浏览会话。

### SAML 配置 <a href="#saml-configuration" id="saml-configuration"></a>

要更改实体 ID 或 ACS URL，请编辑 `.env` 文件，然后重启 Docker 容器：

```bash
docker-compose --profile idp up -d
```

## 故障排除 <a href="#troubleshooting" id="troubleshooting"></a>

### Bitwarden 服务器显示 "unknown userId" 错误 <a href="#bitwarden-server-thows-unknown-userid-error" id="bitwarden-server-thows-unknown-userid-error"></a>

`authsources.php` 中缺少用户的 `uid` 声明。
