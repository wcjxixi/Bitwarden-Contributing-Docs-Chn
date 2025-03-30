# 公共 API

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/public-api)
{% endhint %}

Bitwarden 公共 API 为组织提供了一套管理成员、集合、群组、事件日志和策略的工具。有关公共 API 的更多信息，请参阅[帮助中心 ](https://bitwarden.com/help/public-api/)。

## 与私有 API 的区别

大多数开发者可能更熟悉我们客户端应用程序所使用的私有 API。公共 API 与其在几个关键方面有所不同：

| 私有 API                                                                              | 公共 API                                                                  |
| ----------------------------------------------------------------------------------- | ----------------------------------------------------------------------- |
| 位于 [https://api.bitwarden.com](https://api.bitwarden.com/)                          | 位于 [https://api.bitwarden.com/public](https://api.bitwarden.com/public) |
| 由官方 Bitwarden 客户端应用程序使用                                                             | 由第三方使用，通常用于自定义集成                                                        |
| 广泛范围 -- 可用于任何事物                                                                     | 狭窄范围 -- 只能用于管理组织                                                        |
| 可随时更改（但受官方[支持周期](https://bitwarden.com/help/bitwarden-software-release-support/)约束） | 必须提前通知（如弃用警告）某些更改                                                       |
| 使用用户凭据进行身份验证                                                                        | 使用组织 API 密钥进行身份验证                                                       |

## 开发指引

1. 避免进行破坏性更改 -- 这些更改会要求现有 API 用户更新他们的集成以避免错误或意外行为 -- 例如，使新属性可选，这样现有集成就不必提供一个值
2. 如果必须进行破坏性更改，请考虑如何提前通知现有用户。与工程、产品和客户成功集成团队沟通，协调所需的通知并最小化影响
3. 不要使用与私有 API 相同的请求/响应模型
4. 使用 [xml 文档注释](https://learn.microsoft.com/zh-cn/dotnet/csharp/language-reference/xmldoc/)为您的端点和模型添加文档。这些将包含在 SwaggerUI 输出中

## 本地开发 <a href="#developing-locally" id="developing-locally"></a>

当以开发模式运行时，Bitwarden 服务器包含一个 [SwaggerUI](https://swagger.io/tools/swagger-ui/) 实例，类似于我们在[帮助中心](https://bitwarden.com/help/api/)的这一个。

SwaggerUI 可以帮助您测试对公共 API 所做的任何更改，而无需编写自己的 HTTP 请求。您还可以检查当帮助中心更新时，SwaggerUI 将如何呈现您的 xmldoc 注释。

要使用 SwaggerUI：

1. 启动您的本地开发服务器（API 和身份项目）以及 Web Vault
2. 导航到 [http://localhost:4000/docs](http://localhost:4000/docs)
3. 点击「授权」
4. 在另一个标签页中打开 Web Vault 并导航到您的组织设置页面。点击「查看 API 密钥」
5. 将您的组织中的 `client_id` 和 `client_secret` 从 Web Vault 中输入到 Swagger 中。现在您可以关闭 Web Vault 并在 Swagger 中继续操作
6. 在作用域部分，点击「全选」
7. 点击「授权」以关闭对话框
8. 您应该会收到一个确认对话框。点击「关闭」

您现在可以通过展开任何部分，点击「试一试」，编辑请求并点击执行来测试公共 API。响应将显示在下方。您还可以通过手动检查 Web Vault 中的组织来验证您的请求是否成功。
