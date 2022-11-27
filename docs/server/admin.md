# 管理门户

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/server/admin)
{% endhint %}

## 配置用户 <a href="#configuring-users" id="configuring-users"></a>

使用电子邮件发送的链接，管理门户认证完全通过无密码流程完成。电子邮件地址必须是位于 `adminSettings:admins` 用户机密中的才能获得授权。

如果您遵循了[服务器设置指南](guide.md)，这应该已经被配置并且默认为 `admin@localhost`。如果没有，请立即返回并配置它。

{% hint style="info" %}
有关如何配置用户机密的信息，请参阅[用户机密参考](user-secrets.md)。
{% endhint %}

## 设置 <a href="#setup" id="setup"></a>

1、导航到 `server/src/admin` 目录。

2、恢复 nuget 包：

```
dotnet restore
```

3、安装 npm 包：

```
npm ci
```

4、构建管理项目：

```
dotnet build
```

5、使用必要的样式表和库构建 `wwwroot` 目录：

```
npx gulp build
```

6、启动服务器：

```
dotnet run
```

7、使用您喜欢的浏览器导航到您的管理页面（默认情况下，这是 [http://localhost:62911](http://localhost:62911)），以确认它正在工作。

## 登录 <a href="#logging-in" id="logging-in"></a>

1. 导航到您的管理站点。默认情况下，这是 [http://localhost:62911](http://localhost:62911)。
2. 输入 `admin@localhost` 作为电子邮件（或您在用户机密中配置的任何电子邮件）
3. 打开 MailCatcher（默认为 [http://localhost:1080](http://localhost:1080)），然后点击登录链接。
