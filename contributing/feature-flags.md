# \*功能标志

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/feature-flags)
{% endhint %}

## 背景​ <a href="#background" id="background"></a>

基于 [ADR 0018](../architecture/adr/0018-feature-management.md) 添加了对功能标记的支持。一些亮点：

* 当请求标记的状态时提供[上下文](https://github.com/bitwarden/server/blob/master/src/Core/Context/ICurrentContext.cs)。我们目前允许针对用户、组织和服务账户进行定位。仅将 ID 发送到 LaunchDarkly，以避免 PII 共享。
* 所有可用的功能标记状态都提供给调用[配置 API](https://github.com/bitwarden/server/blob/master/src/Api/Models/Response/ConfigResponseModel.cs) 的客户端。
* 环境（目前是生产、质量保证 (QA) 和开发）的存在可以进一步细分标记状态。这将根据代码运行的位置自动进行。

## 标记数据源​ <a href="#flag-data-sources" id="flag-data-sources"></a>

在客户端或服务器代码中使用功能标记时，了解标记的来源非常重要。

标志的来源取决于正在使用的 Bitwarden 服务器实例，对于客户端开发，标记由 Bitwarden API 提供。

| 服务器配置            | 标记来源                    |
| ---------------- | ----------------------- |
| 本地开发             | 本地应用程序设置、JSON 文件或代码修改   |
| 自托管              | 除非提供上述本地配置，否则标记为「off」   |
| QA Cloud         | LaunchDarkly QA         |
| Production Cloud | LaunchDarkly Production |

{% hint style="warning" %}
**自托管支持**

自托管客户未正式支持功能标记。在 Bitwarden 内部测试之外，不支持使用应用程序设置或 JSON 文件来获取功能标记值。请参阅[自托管注意事项](feature-flags.md#self-hosted-considerations)，了解功能标记如何应用于自托管。
{% endhint %}

本地开发服务器实例不会查询 LaunchDarkly 的功能标记值。

如果您需要在本地开发期间更改任何功能标记值的默认值，则需要设置本地应用程序设置或基于文件的数据源。**如果没有本地数据存储，所有标记值都将解析为其默认（「off」）值**。

### 本地配置：用户机密​ <a href="#local-configuration-user-secrets" id="local-configuration-user-secrets"></a>

要通过应用程序设置设置数据源，请将以下内容放入您的用户机密中：

```json
{
  "globalSettings": {
    "launchDarkly": {
      "flagValues": {
        "example-boolean-key": true,
        "example-string-key": "value"
      }
    }
  }
}
```

将 `example-boolean-key` 和 `example-string-key` 替换为您的标记名称，并相应地更新标记值。

请记住运行 `dev/setup_secrets.ps1` 并重新启动服务器以使新的机密生效。

环境变量也可以像其他应用程序设置覆盖一样使用。

### 本地配置：JSON 文件​ <a href="#local-configuration-json-file" id="local-configuration-json-file"></a>

要通过本地文件设置数据源，请创建一个 `flags.json` 文件，如下所示：

```json
{
  "flagValues": {
    "example-boolean-key": true,
    "example-string-key": "value"
  }
}
```

将 `example-boolean-key` 和 `example-string-key` 替换为您的标记名称，并相应地更新标记值。

默认情况下，LaunchDarkly 启动将在根项目目录中查找此文件（例如 `Api` 项目的 `/src/Api/`），并将其部署到构建输出目录。但是，如果您希望将文件存储在其他位置，则可以使用 `FlagDataFilePath` 配置设置来覆盖它。该文件必须在构建解决方案之前存在，但一旦存在，您就可以更改文件内容并在运行/调试代码时立即查看结果。

### 本地配置：代码修改​ <a href="#local-configuration-code-modification" id="local-configuration-code-modification"></a>

在某些情况下，可能需要在清理活动完全完成之前将功能标记值更改为默认状态以外的值，特别是当已部署的客户端仍然依赖于返回的标记值来确保某些功能时。在服务器代码库中，除了功能标记[常量定义](feature-flags.md#server)之外，还存在一个方法 `GetLocalOverrideFlagValues()` ，其中可以将覆盖作为字典键值对放置：

```csharp
return new Dictionary<string, string>()
{
    { ExampleBooleanKey, "true" }
};
```

这只能暂时使用，并作为功能标记清理过程的一部分，以及为不使用或不了解替代配置方法的安装启用快速功能可用性。

{% hint style="success" %}
**客户端中使用的标记的本地数据源**

为了在客户端中使用功能标记，上述设置应在 `Api` 项目中定义 - 这是因为客户端用来查询功能标记的 `/config` 端点位于 `Api`。这样做将确保检索到正确的标记值并将其发送到客户端。
{% endhint %}

## 创建一个新标记​ <a href="#creating-a-new-flag" id="creating-a-new-flag"></a>

当开始开发新功能时，请与您的团队讨论是否应将其放在功能标记后面。团队应该就标记内容的范围以及应该应用标记的位置（客户端和服务器端）达成一致。虽然对于“功能”的构成没有明确的规则，但应共同努力实现标记及其各自用途的最佳平衡。

一旦您确定需要一个功能标记，第一步就是确定一个名称。命名的建议是：

* 使用短横线命名该标记（小写字母和破折号分隔，例如 `enable-feature`）。
* 对于布尔标记，不必包含 `enable` 动词，因为它暗示它是一个功能标记。例如，建议使用 `new-feature` 而不是 `enable-new-feature`。
* 保持键名简洁。

名称确定后，将功能标记添加到服务器上的 [`FeatureFlagKeys`](https://github.com/bitwarden/server/blob/master/src/Core/Constants.cs) 常量文件中。这将允许通过您在下面配置的任何数据源从 LaunchDarkly 检索标记。

### 本地开发 <a href="#local-development" id="local-development"></a>

当您开始使用该功能时，请使用本地配置选项之一将标记显示给您的使用代码，以确保所有支持的标记值的行为都是正确的。由于初始开发的功能标记不必存在于 LaunchDarkly 中，因此**在确定最终实现之前不要在线创建它们**。

{% hint style="success" %}
**本地客户端开发**

请记住，对于客户端本地开发，功能标记的来源取决于您正在使用的服务器实例。例如，如果您正在开发客户端代码并引用 QA Cloud Bitwarden API，则必须在此处配置该标记，而不是在本地数据存储中。
{% endhint %}

### LaunchDarkly 中的定义​ <a href="#definition-in-launchdarkly" id="definition-in-launchdarkly"></a>

为了在任何部署的环境中测试功能标记，必须首先在 LaunchDarkly Web 应用程序中定义它。为此，请向您的工程经理请求标记- 他们将拥有适当的访问权限。你们应该讨论：

* 标记的数据类型。
* 标记的默认值。
* 标记的可能值（对于非布尔类型）。
* 任何应驱动标记行为的基于上下文的规则。

{% hint style="success" %}
**我什么时候应该在 LAUNCHDARKLY 中请求标记？**

作为一般规则，应请求在 LaunchDarkly 中创建功能标记，作为使用该标记将代码合并到主线分支的一部分。由于使用自托管实例进行本地开发和 QA 测试将使用本地数据源，因此第一次引用 LaunchDarkly 中的标记是在代码部署到云环境时。
{% endhint %}

## 在代码中使用功能标记​ <a href="#consuming-feature-flags-in-code" id="consuming-feature-flags-in-code"></a>

当针对功能标记进行编码时，尽可能默认为「off」状态 - 进行防御性编码，以便在标记完全不可用时维护现有功能。当接口支持它时，还应向功能标记访问器提供暗示「off」的默认值。

离线模式使默认值变得更加重要，本地开发以及自托管安装意味着离线。不仅在 LaunchDarkly 的在线标记定义中设置安全默认值，而且还要在代码中设置。

### 客户​端 <a href="#clients" id="clients"></a>

所有客户端都通过查询 Bitwarden API 上的 `/config` 端点来检索其功能标记。客户端不直接引用LaunchDarkly客户端SDK。

为了优化功能标记的使用，不会在每次请求标记值时从服务器检索它们。相反，按照以下时间间隔从服务器检索标记：

* 在应用程序启动时。
* 应用程序启动后每小时一次。
* 同步（自动和手动）。
* 在环境变化时。

从下面定义的服务请求标记值将为使用组件提供来自这些检索事件之一的最新值。

#### 网络​端 <a href="#web" id="web"></a>

通过 [`ConfigService`](https://github.com/bitwarden/clients/blob/master/libs/common/src/services/config/config.service.ts) 上的 `fetchServerConfig()` 方法检索功能标记值。

要使用功能标记，您应该首先将新功能标记定义为 [`FeatureFlags`](https://github.com/bitwarden/clients/blob/master/libs/common/src/enums/feature-flag.enum.ts) 枚举中的枚举值。

定义后，可以通过注入 `ConfigService` 并使用其中一种检索方法来检索该值：

* `getFeatureFlagBool()`
* `getFeatureFlagString()`
* `getFeatureFlagNumber()`

#### 移动端 <a href="#mobile" id="mobile"></a>

通过 `ConfigService` 上的 `GetAsync()` 方法检索功能标记值。

要使用功能标记，您应该首先在 `Constants` 文件中将新功能标记定义为字符串常量值。

定义后，可以通过注入 `IConfigService` 并使用其中一种检索方法来检索该值：

* `GetFeatureFlagBoolAsync()`
* `GetFeatureFlagStringAsync()`
* `GetFeatureFlagNumberAsync()`

### 服务器​ <a href="#server" id="server"></a>

1. 在需要功能标记的地方注入 `IFeatureService`。请注意，访问功能状态时您还需要 `ICurrentContext`。
2. 在 [`FeatureFlagKeys`](https://github.com/bitwarden/server/blob/master/src/Core/Constants.cs) 列表中查找您计划使用的密钥的常量。它应该在[创建新标记时](https://contributing.bitwarden.com/contributing/feature-flags/#creating-a-new-flag)添加。
3. 在要素服务上通过适当的方法使用上述关键常量：
   * `IsEnabled` 用于布尔值，`false` 为假定的默认值。
   * `GetIntVariation` 用于整数，`0` 为假定的默认值。
   * `GetStringVariation` 用于字符串，`null` 为假定的默认值。

## 功能标志生命周期​ <a href="#feature-flag-lifecycle" id="feature-flag-lifecycle"></a>

当您需要更改 LaunchDarkly 内的在线功能时，请让您的管理层知道。只有少数用户拥有 LaunchDarkly 账户，以节省许可成本。

功能标记不一定需要从 LaunchDarkly 中删除，只是未使用即可。将它们链接到 Jira 有助于创建该功能的历史记录，并且可以保留大量的在线日志和审计记录。长时间未访问的功能标记将自动转至「非活动」状态，这也有助于识别需要清理的技术债务。

在定义故事的子任务时，请务必包含一个清理任务，用于从代码中删除功能标记- 重要的是这些任务不要保留太久并假设永久存在。一旦功能成功启动，请在稍后阶段解决该任务。

### 自托管注意事项​ <a href="#self-hosted-considerations" id="self-hosted-considerations"></a>

自托管实例将无法访问 LaunchDarkly，因此从 API 检索的服务器配置会将所有功能标记评估为其默认状态，除非服务器进行了其他配置。这在实践中意味着，在该功能可用于自托管实例之前，必须从代码中删除该功能标记。这意味着分阶段的功能发布周期，如下所示：

1. 发布云并自托管，功能已关闭
2. 打开功能标记，**仅**针对云实例启用该功能
3. 发布云和移除功能标记的自托管，从而为自托管实例启用该功能

自托管安装可以选择配置替代[数据源](feature-flags.md#flag-data-sources)以更快地采用功能。
