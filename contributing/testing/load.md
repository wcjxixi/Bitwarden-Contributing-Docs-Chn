# \*负载测试

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/testing/load)
{% endhint %}

负载测试以 k6 脚本的形式提供，可在本地或云中执行，重点是对平台进行大规模演练。

## 配置和参数​ <a href="#configuration-and-parameters" id="configuration-and-parameters"></a>

所有测试都使用两个环境变量：

* `IDENTITY_URL` ：用于身份验证的 [Identity](https://github.com/bitwarden/server/tree/master/src/Identity) 实例的 URL。
* `API_URL` ：经过身份验证后用于负载测试操作的 [API](https://github.com/bitwarden/server/tree/master/src/Api) 实例的 URL。
* `CLIENT_ID` ：所有请求的 `X-ClientId` 标头值，用于跟踪唯一客户端并管理速率限制。

根据所测试的 API，使用密码或客户端凭据授权。

对于密码：

* `AUTH_USER_EMAIL` ：用户电子邮件地址。
* `AUTH_USER_PASSWORD_HASH` ：用户主密码的哈希值。

对于客户端凭据：

* `AUTH_CLIENT_ID` ：OAuth 客户端 ID。
* `AUTH_CLIENT_SECRET` ：OAuth 客户端密钥。

Grafana 的在线状态用于在云中托管脚本，并配置了上述所有内容。对于本地测试，您可能需要[生成](https://help.ppgg.in/admin-console/bitwarden-public-api#authentication)一组 ID 和密钥。

## 入门 <a href="#getting-started" id="getting-started"></a>

1. 在本地[安装](https://k6.io/docs/get-started/installation/)并配置 k6。
2. （可选）使用令牌[登录](https://k6.io/docs/cloud/creating-and-running-a-test/cloud-tests-from-the-cli/)您的云账户。
3. 运行您的脚本！

对于本地运行，这很简单：

```bash
k6 run script.js
```

如果您想将结果传输到云端，请添加 `--out=cloud` 参数。要传递环境变量，请使用 `-e` 参数，例如 `-e IDENTITY_URL="http://localhost:4000"` 。

对于云，直接运行：

```bash
k6 cloud script.js
```

计划的运行会在云端自动进行。

## 创建新脚本​ <a href="#creating-new-scripts" id="creating-new-scripts"></a>

有些示例已经存在，可以复制。要进行简单的 `GET` 操作，请查看 `/config` 测试。要了解更全面的 CRUD 套件，请查看 `/public/groups` 测试。

### 最佳实践​ <a href="#best-practices" id="best-practices"></a>

检查应简单明了，查找状态代码和继续操作所需的元素（例如 ID）。避免功能测试，因为在大多数情况下，单元测试和自动化测试已经涵盖了功能测试；负载测试的目的是对系统施加压力，而不是确保其正确性。

每个脚本顶部的 `options` 应至少包含在云中使用的 `ext` 信息。选择一个好的 `name`，并在 `params` 的 `tags` 元素中设置相同的名称；这将用于对请求进行分组和收集准确的指标。

k6 提供了已包含的[实用程序脚本](https://k6.io/docs/javascript-api/jslib/utils/)，并且常用的 [`uuidv4`](https://k6.io/docs/javascript-api/jslib/utils/uuidv4/) 帮助程序可以生成用于放置在请求属性中的 UUID。k6 HTTP 库并不假定请求应被格式化为 JSON，因此请使用 `JSON.stringify` 作为正文。

身份验证助手已经可用，并且预计几乎所有测试都需要它。

### Stages <a href="#stages" id="stages"></a>

[Stages](https://k6.io/docs/using-k6/k6-options/reference/#stages) 用于在脚本执行时配置变量负载：

```javascript
stages: [
  { duration: "30s", target: 10 },
  { duration: "1m", target: 20 },
  { duration: "2m", target: 25 },
  { duration: "30s", target: 0 },
];
```

上述程序在 30 秒内将负载提升到 10 VU，然后在一分钟内提升到 20 VU，再在两分钟内提升到 25 VU，最后在 30 秒内将负载降至空载。

根据目标，这可能会改变，通常所有测试都将使用相同的 Stages 以实现一致性。

### Thresholds <a href="#thresholds" id="thresholds"></a>

[Thresholds](https://k6.io/docs/using-k6/thresholds/) 也类似用于设定负载测试成功的预期值：

```javascript
thresholds: {
  http_req_failed: ["rate<0.01"],
  http_req_duration: ["p(95)<1500"]
  }
```

失败的请求计数可以设置为零，但对于短暂的异常，允许 1% 的失败率。超过 95% 的请求 (P95) 的持续时间将通过多次运行来测量，以作为基准线。

不仅脚本内的检查会建立指标，而且还可以参考核心指标和自定义指标（如果需要）作为 thresholds。同样，通常期望所有测试都将使用相同的 thresholds 作为基础。

## 结果​ <a href="#results" id="results"></a>

最好在提供交互式图形和图表的云中查看结果。结果包含：

* P95 响应时间。
* 请求总数和每秒请求数。
* 正在使用的 VU。
* HTTP 请求详细信息及其结果检查。
* Thresholds。

脚本创建并稳定后，就会为其设定基准线，并可用于随时间的推移进行比较。
