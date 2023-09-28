# 系统管理门户

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/admin)
{% endhint %}

{% hint style="info" %}
**门户命名**

本文档涉及我们的 `server` 存储库中 `admin` 应用程序的部署。为了将该应用程序与 Bitwarden 中的其他应用程序区分开来，我们将其分别如下命名：

* 对于**云托管实例**（Bitwarden 内部）→ **Bitwarden 门户**
* 对于**自托管实例** → **系统管理门户**
{% endhint %}

## 设置 <a href="#setup" id="setup"></a>

1、导航到 `server/src/admin` 目录。

2、恢复 nuget 包：

```bash
dotnet restore
```

3、安装 npm 包：

```bash
npm ci
```

4、构建管理员项目：

```bash
dotnet build
```

5、使用必要的样式表和库构建 `wwwroot` 目录：

```bash
npx gulp build
```

6、启动服务器：

```bash
dotnet run
```

7、使用您喜欢的浏览器导航到您的管理页面（默认情况下是 [http://localhost:62911](http://localhost:62911)），以确认它正在工作。

## 配置访问权限 <a href="#configuring-access" id="configuring-access"></a>

### 身份验证 <a href="#authentication" id="authentication"></a>

门户身份验证通过使用电子邮件发送的链接完全无密码流程进行。要获得授权，电子邮件地址必须列在 adminSettings:admins 用户机密中。

如果您已遵循[服务器设置指南](guide.md)，则应该已经配置好，下列账户应该已经拥有了访问权限：

* `owner@localhost`
* `admin@localhost`
* `cs@localhost`
* `billing@localhost`
* `sales@localhost`

如果还没有，请立即返回然后配置它。

{% hint style="success" %}
有关如何配置用户机密的信息，请参阅[用户机密](broken-reference)。
{% endhint %}

## 登录 <a href="#logging-in" id="logging-in"></a>

1. 导航到您的控制台 URL。默认情况下是 [http://localhost:62911](http://localhost:62911)。
2. 输入 `admin@localhost` 作为电子邮件（或您在用户机密中配置的任何电子邮件）
3. 打开 MailCatcher（默认为 [http://localhost:1080](http://localhost:1080)），然后点击登录链接。
