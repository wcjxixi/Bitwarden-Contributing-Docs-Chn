# \*高级服务器设置

{% hint style="info" %}
任意对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/advanced-setup)
{% endhint %}

您的服务器启动并运行后，可能还需要一些额外的配置才能激活 Bitwarden 的所有功能。

## 高级功能​ <a href="#premium-features" id="premium-features"></a>

用户机密文件包括一个 Stripe 测试密钥，它允许您「购买」高级功能而无需实际付费。

1. 使用 Web 客户端连接到本地服务器
2.  购买订阅或高级功能时，请使用 Stripe 文档中的测试数据所使用的信用卡付款方式：

    | 品牌         | 卡号                    | CVC      | Date    |
    | ---------- | --------------------- | -------- | ------- |
    | Visa       | `4111 1111 1111 1111` | 任意 3 位数字 | 任意将来的日期 |
    | Mastercard | `5555 5555 5555 4444` | 任意 3 位数字 | 任意将来的日期 |
3. 您的信用卡详细信息只需通过前端表单验证（例如，您的信用卡号必须是正确的字符数量）。
4. 购买您想要的任意高级功能

{% hint style="info" %}
Stripe 的[策略](https://support.stripe.com/questions/test-mode-subscription-data-retention)是在 90 天后**自动取消**测试订阅，然后在 30 天后**删除**已取消的测试订阅。这会导致本地服务器上长期存在的高级用户和组织出现异常的计费行为。

要纠正这种情况，您必须将组织/用户重新订阅到高级计划，以创建新的测试订阅。

1. 从 Bitwarden 门户，删除组织/用户网关信息，然后将其计划设置为「免费」
2. 从 Web 客户端为组织/用户添加新的测试支付方式
3. 像之前一样重新购买所需的高级功能
{% endhint %}

## 电子邮件​ <a href="#emails" id="emails"></a>

Docker compose 将启动一个可以使用的本地 SMTP 服务器，但也可以使用 Mailtrap 或 Amazon 等其他服务来调试 amazon 集成。

* Amazon Simple Email Service - 用户机密密码库项目包含一个名为 `additional-keys-for-cloud-services.json` 的单独附件。将 `amazon` 密钥添加到您的用户机密中以使用 Amazon 的邮件服务。注意：这会将电子邮件发送到真实的电子邮件地址！
* [bytemark/docker-smtp](https://github.com/BytemarkHosting/docker-smtp) - 在 Docker 上运行的本地 SMTP 服务器。

## 文件上传（文件 Send 和附件） <a href="#file-uploads-file-sends-and-attachments" id="file-uploads-file-sends-and-attachments"></a>

使用两种方式之一来存储上传的文件：

* 使用我们的生产环境云实例所使用的 Azure 存储。
  * Docker 将创建一个模拟 Azure 存储 API 的本地 [Azurite](https://github.com/Azure/Azurite) 实例。并用于基本测试。
  * 我们还有一个测试 Azure 存储账户供开发使用。此用户机密附加到“服务器用户机密”共享密码库项目。您需要将 `send` 密钥和 `attachment` 密钥复制到您自己的用户机密中。
* 使用自托管实例所使用的直接上传用功能，将文件直接存储在服务器上。以下设置将允许您上传文件，但不允许下载它们。将以下设置放在 `globalSettings` 下（根据需要更新路径）：

```json
"send": {
    "baseDirectory": "/Users/<your name>/Projects/localStorageDev",
    "baseUrl": "file:///Users/<your name>/Projects/localStorageDev"
},
"attachment": {
    "baseDirectory": "/Users/<your name>/Projects/localStorageDev",
    "baseUrl": "file:///Users/<your name>/Projects/localStorageDev"
}
```

{% hint style="info" %}
要正确测试直接上传所使用的上传和下载文件功能，您需要设置本地文件服务器。如果您这样做，请在此处添加说明:)
{% endhint %}

## PayPal <a href="#paypal" id="paypal"></a>

如果您只需要高级功能，则通过卡支付会更容易（请参阅上面的说明）。但是，您可能需要专门测试 PayPal 集成。

1. 确保您使用的是y已共享的工程集合中的 `secrets.json`。这些机密提供对 PayPal 沙盒账户的访问，在本地运行服务器时将自动使用该账户。
2. 您需要 2 个 PayPal 沙盒账户来进行测试：&#x20;
   * 卖家 - 这将是我们的沙盒账户。登录详细信息可在共已享的工程集合中找到。您可以登录此账户查看 Bitwarden 在您处理的任何交易中收到的（假）资金记录。
   * 买家 - 这将是您的沙盒账户。
3. 使用您的工作电子邮件地址[创建一个新的 PayPal 沙盒账户](https://www.sandbox.paypal.com/)。然后，您可以使用您想要的任何特定个人信息（例如国家/地区、付款方式）[创建虚假买家账户](https://developer.paypal.com/docs/api-basics/sandbox/accounts/)。这在测试销售税时特别有用。如果需要，您可以使用[信用卡生成器](https://developer.paypal.com/developer/creditCardGenerator/)生成「有效」的虚假信用卡详细信息。
4. 登录您的 Web 密码库然后导航至付款页面。所有 PayPal 付款功能都应使用您的沙盒账户或您已创建的虚假买家账户来运行。

注意：如果您正在测试销售税，您首先必须通过「管理员门户」创建销售税率。

## YubiKey 2FA <a href="#yubikey-2fa" id="yubikey-2fa"></a>

要在本地测试 YubiKey 2FA，您必须首先使用 Yubico 的 ClientId 和 Key 配置本地用户机密。这用于验证对 Yubico 的 API 调用，以验证所提供的 OTP。

设置本地服务器进行 YubiKey 验证的步骤如下：

1. 通过[这里](https://upgrade.yubico.com/getapikey/)从 Yubico 获取一组 ClientId 和 Key。请注意，这要求您拥有一个 YubiKey 才能提供 OTP。如果您没有 YubiKey，请联系您的经理。
2. 更新 `Identity` 项目中的 `globalSettings:yubico:key` 和 `globalSettings:yubico:clientid` 用户机密。您可以使用[更新脚本](secrets.md)或手动更新：

```bash
   dotnet user-secrets set globalSettings:yubico:key [Key]
   dotnet user-secrets set globalSettings:yubico:clientid [ClientId]
```

## 反向代理设置​ <a href="#reverse-proxy-setup" id="reverse-proxy-setup"></a>

运行反向代理可用于模拟以分布式方式运行多个服务器服务。`/dev` 文件夹中的 [Docker Compose](https://docs.docker.com/compose/) 配置已经为 Api 和 Identity 服务准备好了配置（可为其他服务扩展）。

1、反向代理容器被设置为使用位于 `dev/reverse-proxy.conf` 的 [nginx](https://nginx.org/en/docs/beginners\_guide.html#conf\_structure) 配置文件。复制反向代理配置示例：

```bash
cd dev
cp reverse-proxy.conf.example reverse-proxy.conf
```

2、（可选）修改 reverse-proxy.conf 以支持所需的服务数量及其端口。默认情况下，它支持分别在 **4000/4002** 和 **33656/33658** 端口上运行的两个 **Api** 和两个 **Identity** 服务。

3、确保环境变量 `API_PROXY_PORT` 和 `IDENTITY_PROXY_PORT` 存在于 `dev/.env` 中。有关其默认值，请参阅 `dev/.env.example`。

4、使用 docker compose 启动反向代理。

```bash
docker compose --profile proxy up -d
```

5、为每个正在运行的实例使用特定的端口，在本地启动所需的服务。**这些端口必须与 `upstream` 配置块中的 `reverse-proxy.conf` 中的端口匹配。**

* **命令行**（_在单独的终端中_）

```bash
# 1st instance
cd src/Api
dotnet run --urls=http://localhost:4000/
```

```bash
# 2nd instance: --no-build can avoid conflicts with the first instance
cd src/Api
dotnet run --urls=http://localhost:4002/ --no-build
```

* **Rider** - 为所需服务创建新的启动配置，每个配置在 `ASPNETCORE_URLS` 环境变量中使用不同的端口。然后同时运行/调试每个配置。
* **Visual Studio** - 您可以为每个服务添加额外的运行配置以使用特定的端口，这类似于 Rider。_您可能需要运行多个 Visual Studio 实例才能运行/调试同一个项目_。

> _特别小心由于启动配置更改而导致的意外提交_

6、更新所有客户端以使用反向代理而不是直接使用服务。这些端口在 `dev/.env` 和 `dev/reverse-proxy.conf` 中定义。

* **Api** - `http://localhost:4100`
* **Identity** - `http://localhost:33756`

> 如果您需要添加其他服务（除 Api 和 Identity 外），请将它们添加到 `dev/reverse-proxy.conf` 中，并确保在 `dev/docker-compose.yml` 文件中为反向代理容器暴露必要的端口。
