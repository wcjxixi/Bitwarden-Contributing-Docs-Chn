# \*自托管指南

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/self-hosted/)
{% endhint %}

{% hint style="warning" %}
此页面仅在您需要测试自托管功能时才与您相关。大多数开发工作都**不**需要它。 如果您只有本地服务器，请转至[服务器设置指南](guide.md)部分。
{% endhint %}

本页面将阐述如何配置并运行自托管开发服务器以及云端开发服务器。这在以下情况下很有用：

* 您需要测试自托管实例如何与云端通信时
* 您需要在不扰乱正常开发环境的情况下开发自托管功能时

在最常见的配置中，云端和自托管开发服务器的运行都使用相同的基础架构配置：

* 服务运行在 `http://localhost:{port}` 上（云端与自托管使用不同的端口号）
* 本地 SQL 数据库（云端与自托管使用不同的数据库名称）

## 要求 <a href="#requirements" id="requirements"></a>

本指南假定您已完成并熟悉了[服务器设置指南](guide.md)中的技术细节。请确保您已经拥有一个运行中的云端配置服务器开发环境，然后再将自托管实例连接到它。

## 设置 <a href="#setup" id="setup"></a>

将服务器存储库克隆到一个名为 `server-selfhost` 的新文件夹中：

```bash
git clone git@github.com:bitwarden/server.git server-selfhost
```

接下来，我们将致力于让自托管服务器与本地云端配置服务器同时运行。

### 生成安装 ID 和安装 Key <a href="#generate-installation-id-and-key" id="generate-installation-id-and-key"></a>

每一个自托管实例都由安装 ID 和安装 Key 定义。它们被存储在这两个地方：

* 作为用户机密存储在自托管实例中，以及
* 在您的云端配置实例的 `Installations` 表中，以便它可以验证来自您的自托管服务器的请求

要获取 ID 和 Key，可以

* 从 [https://bitwarden.com/host/](https://bitwarden.com/host/) 获取它们，或者
* 生成一个 Guid (ID) 和随机字母数字字符串 (Key)

记录下它们以便在接下来的步骤中使用。

### 从云端实例复制文件 <a href="#copy-files-from-cloud-instance" id="copy-files-from-cloud-instance"></a>

您需要从云配置的存储库中复制两个文件（这俩个文件您应该已经设置好了）。它们将被复制到您的自托管存储库。它们都位于 `dev` 文件夹中。我们将它们复制过来以节省时间和精力；如果它们具有几乎所有相同的值时，则无需完成重新生成它们的工作。复制到自托管存储库后，我们将在下面的后续步骤中修改它们。

#### `secrets.json` 文件 <a href="#secretsjson-file" id="secretsjson-file"></a>

将 `secrets.json` 文件复制到您刚刚克隆的自托管存储库的 `dev` 文件夹中。

{% hint style="warning" %}
`.gitignore` 配置将阻止将 `dev` 文件夹中的 `secrets.json` 签入源代码管理。在任何情况下都不应将 `secrets.json` 推送到源。
{% endhint %}

#### `.env` 文件 <a href="#env-file" id="env-file"></a>

将 `.env` 文件复制到您刚刚克隆的自托管存储库的 `dev` 文件夹中。

### 自托管机密配置 <a href="#self-hosted-secrets-configuration" id="self-hosted-secrets-configuration"></a>

在您的自托管存储库中，导航到 `dev` 文件夹并打开您复制来的 `secrets.json` 文件。

我们必须配置用户机密的 `Dev:SelfHostOverride:GlobalSettings` 部分。本节指定的设置将覆盖本地自托管开发实例的设置。被覆盖部分中的任何内容都将被应用，而不是 `GlobalSettings` 中给出的值。

{% hint style="success" %}
如果您忘记了什么是用户机密，请回顾[用户机密](../../contributing/user-secrets.md)。
{% endhint %}

我们需要在此处执行此操作，因为我们需要能够定义在 Docker 容器中的环境变量中指定的真实的自托管实例的设置值。我们使用机密文件来执行此操作，而不是在我们的机器上设置环境变量以及让 .NET Core 配置中的构建为我们构建我们的设置。

目前，我们只覆盖 `GlobalSettings`。任何其他需要覆盖的用户机密都需要更改代码才能这样做。检查服务器存储库中的 `ServiceCollectionExtension.AddGlobalSettingsServices`，看看我们现在是怎么做的（[代码的脆弱链接](https://github.com/bitwarden/server/blob/master/src/SharedWeb/Utilities/ServiceCollectionExtensions.cs#L448-L463)）。

[内部用户机密](secrets.md)包含一个最小覆盖示例。您需要更新 `Dev:SelfHostOverride:GlobalSettings` 部分中的以下值：

* 安装 ID 和 Key，使用您刚刚生成的值
* 我们将在下面创建的新 SQL 数据库的 SQL Server 密码。它可以从您的 `secrets.json` 中已有的云配置设置中复制（即使用与您的云端配置服务器相同的密码），或者生成一个新的密码。
* 任何其他空白值

在自托管存储库中更新了 `secrets.json` 文件后，通过运行以下命令应用更改：

```bash
pwsh setup_secrets.ps1 -clear:$True
```

现在您已经完成更新自托管实例的用户机密的步骤。

## 数据库配置 <a href="#database-configuration" id="database-configuration"></a>

{% hint style="success" %}
在设置数据库之前，请确保您的 Docker 容器正在运行。
{% endhint %}

导航到自托管服务器存储库。我们将为我们的自托管配置创建第二个数据库，以便云端配置实例可以拥有用于开发的独立的数据集。

如果您在创建云端配置数据库时遵循了[服务器设置指南](guide.md)，则您只需运行带有 `-s` 参数的相同 PowerShell 迁移脚本即可：

```bash
pwsh migrate.ps1 -s
```

这将创建一个名为 `vault_dev_self_host` 的新数据库和/或对其运行未知迁移。

为了使您的自托管数据库保持最新，将来调用带有 `-s` 参数的脚本时将对 `vault_dev_self_host` 执行新的迁移。

### 为您的云数据库定义安装 ID 和密钥 <a href="#define-installation-id-and-key-for-your-cloud-database" id="define-installation-id-and-key-for-your-cloud-database"></a>

您需要手动将安装 Key 添加到您的云端配置实例，以便它知道您的自托管实例，并在需要在两者之间进行 API 调用时允许访问。随意使用您喜欢的任何工具，如 Azure Data Studio、sqlcmd、以及下面的脚本，执行此操作。

```sql
/opt/mssql-tools/bin/sqlcmd -S mssql -d vault_dev -U sa -P <<SA_PASSWORD>> -I -i <<SCRIPT_FILE>>
```

其中 `<<SA_PASSWORD>>` 是云数据库的 SQL SA 密码，`<<SCRIPT_FILE>>` 指向包含以下内容的文件：

```plsql
INSERT INTO [vault_dev].[dbo].[Installation]
(
    [Id]
    ,[Email]
    ,[Key]
    ,[Enabled]
    ,[CreationDate]
)
VALUES
(
    '<<YOUR_ID>>'
    ,'<<YOUR_DEV_EMAIL>>'
    ,'<<YOUR_KEY>>'
    ,1
    ,GETUTCDATE()
)
```

其中 `<<YOUR_ID>>` 是您的安装 ID，`<<YOUR_DEV_EMAIL>>` 是您的电子邮件地址，`<<YOUR_KEY>>` 是您的安装 Key。

## 客户端设置 <a href="#client-setup" id="client-setup"></a>

如果我们没有 Web 门户来与 API 交互，则自托管服务器的用途有限。同样，克隆一个新的存储库实例是最简单的：

```bash
git clone git@github.com:bitwarden/clients.git clients-selfhost
```

安装依赖并初始化 jslib：

```bash
cd apps/web
npm ci
```

## 运行 <a href="#running" id="running"></a>

### 服务器 <a href="#server" id="server"></a>

当服务在自托管配置中运行时，它将默认使用它们在云端配置实例中运行的端口号 +1 的端口号。

上面，我们设置了一系列用户机密覆盖，使我们能够运行我们的自托管实例。我们需要确保启动服务器以使用这些设置。如果满足以下两个条件，服务器代码将使用这些设置：

* 环境是开发
* `developSelfHosted` 为 `true`

我们基于您运行服务器的方式以不同方式执行此操作。在您的环境中，自托管启动配置（例如「Api-SelfHost」）将为您设置环境和 `developSelfHosted` 标志。

### VS Code <a href="#vs-code" id="vs-code"></a>

我们有多种启动配置以及组合配置用以轻松启动服务。默认情况下，个人自托管启动是隐藏的。导航到 `launch.json` 以取消隐藏。

{% embed url="https://contributing.bitwarden.com/assets/images/vs-code-a8f7bc4080c82a45457833355fb8c7bd.png" %}

### Visual Studio <a href="#visual-studio" id="visual-studio"></a>

已运行的配置用于在自托管模式下启动一个给定服务。

{% embed url="https://contributing.bitwarden.com/assets/images/visual-studio-dd6ac01d0f9a22b112ae3122a7d79a09.png" %}

### CLI

要从 CLI 运行自托管，您需要：

1、在自托管存储库的根目录中打开一个新的终端窗口。

2、恢复 Identity 服务所需的 nuget 包：

```bash
cd src/Identity
dotnet restore
```

3、启动 Identity 服务：

```bash
dotnet run --launch-profile Identity-SelfHost
```

4、通过导航到 [http://localhost:33657/.well-known/openid-configuration](http://localhost:33657/.well-known/openid-configuration) 测试 Identity 服务是否处于活动状态

5、在另一个终端窗口中，恢复 Api 服务所需的 nuget 包：

```bash
cd src/Api
dotnet restore
```

6、启动 API 服务：

```bash
dotnet run --launch-profile Api-SelfHost
```

7、通过导航到 [http://localhost:4001/alive](http://localhost:4001/alive) 测试 Api 服务是否处于活动状态

{% hint style="info" %}
如果您无法连接到 Api 或 Identity 项目，请检查终端输出信息以确认它们运行时所使用的端口。
{% endhint %}

要启动其他服务，请遵循相同的格式，记得跟随合适的自托管启动配置 `--launch-profile`。

## Web 客户端 <a href="#web-client" id="web-client"></a>

从 `clients-selfhost` 目录中，您可以执行

* `npm run build:bit:selfhost:watch`
* `npm run build:oss:selfhost:watch`

以启动 Bitwarden 许可或 OSS Web 服务器。默认端口为 `8081`，因此您可以同时运行云端配置和自托管配置的 Web 客户端。它还被配置为指向各种服务器项目的默认 `*-SelfHost` 端口。

<details>

<summary>配置 sausage 是如何生成的</summary>

我们的 Web 配置位于 `config/` 中。每一个都有一个名为 `dev` 的子对象。为此，config 对象将 `dev` 对象重新定义为 `dev: {cloud: {}, selfHosted: {}}`。在我们的 webpack 配置文件中，我们根据这些值更新代理和端口设置。

`dev` 对象同时包含云端和自托管开发环境的配置。

</details>
