# \*自托管指南

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/server/self-hosted/)
{% endhint %}

{% hint style="warning" %}
此页面仅在您需要测试自托管功能时才与您相关。大多数开发工作都**不**需要它。 如果您只有本地服务器，请转至[服务器设置指南](guide.md)部分。
{% endhint %}

本页面将阐述如何配置并运行自托管开发服务器以及云端开发服务器。这在以下情况下很有用：

* 您需要测试自托管实例如何与云端通信
* 您需要在不扰乱正常开发环境的情况下开发自托管功能

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

### 生成安装 ID 和密钥 <a href="#generate-installation-id-and-key" id="generate-installation-id-and-key"></a>

每一个自托管实例都由安装 ID 和安装 Key 定义。它们被存储在这两个地方：

* 作为用户机密存储在自托管实例中，以及
* 在您的云端配置实例的 `Installations` 表中，以便它可以验证来自您的自托管服务器的请求

要获取 ID 和 Key，可以

* 从 [https://bitwarden.com/host/](https://bitwarden.com/host/) 获取它们，或者
* 生成一个 Guid (ID) 和随机字母数字字符串 (Key)

记录这些以便在接下来的步骤中使用。

### 从云端实例复制文件 <a href="#copy-files-from-cloud-instance" id="copy-files-from-cloud-instance"></a>

您需要从云配置的存储库中复制两个文件，这俩个文件您应该已经设置好了。它们将被复制到您的自托管存储库。它们都位于 `dev` 文件夹中。我们将它们复制过来以节省时间和精力；如果它们具有几乎所有相同的值时，则无需完成重新生成它们的工作。复制到自托管存储库后，我们将在下面的后续步骤中修改它们。

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
如果您还不熟悉用户机密，请回顾[用户机密](user-secrets.md)。
{% endhint %}

我们需要在此处执行此操作，因为我们需要能够定义真正的自托管实例在其 Docker 容器中的环境变量中指定的设置值。我们使用机密文件来执行此操作，而不是在我们的机器上设置环境变量并让 .NET Core 配置中的构建为我们构建我们的设置。

目前，我们只覆盖 `GlobalSettings`。任何其他需要覆盖的用户机密都需要更改代码才能这样做。查看服务器存储库中的 `ServiceCollectionExtension.AddGlobalSettingsServices`，看看我们今天是怎么做的（代码的脆弱链接）。

内部用户机密包含一个最小覆盖示例。您需要更新 `Dev:SelfHostOverride:GlobalSettings` 部分中的以下值：

* 安装 ID 和 Key，使用您刚刚生成的值
* 我们将在下面创建的新 SQL 数据库的 SQL Server 密码。它可以从您的 `secrets.json` 中已有的云配置设置中复制（即使用与您的云端配置服务器相同的密码），或者生成一个新的密码。
* 任何其他空白值

在自托管存储库中更新了 `secrets.json` 文件后，通过运行以下命令应用更改：

```bash
pwsh setup_secrets.ps1 -clear:$True
```

现在您已经完成更新自托管实例的用户机密的步骤。

## 数据库配置 <a href="#database-configuration" id="database-configuration"></a>

### 为您的云数据库定义安装 ID 和密钥 <a href="#define-installation-id-and-key-for-your-cloud-database" id="define-installation-id-and-key-for-your-cloud-database"></a>

## 客户端设置 <a href="#client-setup" id="client-setup"></a>

## 运行 <a href="#running" id="running"></a>

### 服务器 <a href="#server" id="server"></a>

### VS Code <a href="#vs-code" id="vs-code"></a>

### Visual Studio <a href="#visual-studio" id="visual-studio"></a>

### CLI

## Web 客户端 <a href="#web-client" id="web-client"></a>
