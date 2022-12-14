# Secrets.json

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/secrets/)
{% endhint %}

服务器存储库带有它自己的 `dev/secrets.json` 文件，供社区贡献者使用。内部 Bitwarden 开发人员将需要不同的用户机密文件，以便正确模拟云端环境。

我们用于开发的用户机密文件可以在 Bitwarden 应用程序的开发集合中找到。如果您无权访问此集合，请联系您的管理员。

<figure><img src="../../.gitbook/assets/server-user-secrets.png" alt=""><figcaption></figcaption></figure>

此安全说明拥有 2 个附件：

1. `secrets.json` - 用于开发的默认推荐用户机密。这将使用[服务器设置指南](guide.md)中概述的本地 Docker 服务（例如 MailCatcher 和 Azurite）。
2. `additional-keys-for-cloud-services.json` - 如果您想启用测试云端服务而不是本地 Docker 服务，这些是可选的 Key，您可以将其添加到您的 `secrets.json` 中。它不是一个完整的机密文件，不能单独使用。

## 更新共享的用户机密

如果您需要更新共享的用户机密，请遵循以下规则：

* 新的用户机密应该只放在一个 `.json` 文件中。我们需要避免跨两个文件的重复 Key。
* 避免创建新的 `.json` 文件作为版本控制的手段。如果我们需要回滚，通常可以从原始来源检索信息（例如认证密钥）。如果您觉得必须创建备份，请将其标记为「DEPRECATED」并注明日期。`secrets.json` 和 `additional-keys-for-cloud-services.json` 应该始终包含最新的机密。
* 在 `#team-eng` Slack 频道中通告任何更新，以便每个人都知道更新他们的本地实例。
