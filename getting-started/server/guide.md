# 设置指南

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/guide)
{% endhint %}

此页面将向您展示如何为开发目的设置本地 Bitwarden 服务器。

Bitwarden 服务器由多个可以独立运行的服务组成。对于基本的开发设置，需要 **Api** 和 **Identity** 服务。

{% hint style="info" %}
开始之前：确保您已经安装好了推荐的[工具和库](../tools.md)，包括：

* Docker Desktop
* Visual Studio
* Powershell
* [NET 6.0 SDK](https://dotnet.microsoft.com/download)
* Azure Data Studio
{% endhint %}

## 克隆存储库 <a href="#clone-the-repository" id="clone-the-repository"></a>

1、克隆 Bitwarden 服务器项目：

```bash
git clone https://github.com/bitwarden/server.git
```

2、打开终端并导航到已克隆的存储库的根目录。

## 配置 Git <a href="#configure-git" id="configure-git"></a>

1、配置 Git 以忽略 Prettier 修订：

```bash
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

2、_（可选）_设置预提交 `dotnet format` 钩子：

```bash
git config --local core.hooksPath .git-hooks
```

格式化需要一次完整的构建，而每次提交都这样做可能会太慢。作为替代方案，您可以在方便的时候（例如在请求 PR 审核之前）从命令行运行 `dotnet format`。

## 配置 Docker <a href="#configure-docker" id="configure-docker"></a>

我们提供了一个 Docker Compose 配置，用于在开发过程提供所需的依赖。这被分成多个服务配置文件，以方便自定义。

1、一些 Docker 设置被配置在环境文件 `dev/.env` 中。复制示例环境文件：

```bash
cd dev
cp .env.example .env
```

2、使用您喜欢的编辑器打开 `.env`。

3、设置 `MSSQL_PASSWORD` 变量。这将是用于您的 MSSQL 数据库服务器的密码。

{% hint style="danger" %}
您的 MSSQL 密码必须符合以下[密码复杂性准则](https://learn.microsoft.com/zh-cn/sql/relational-databases/security/password-policy?view=sql-server-ver15#password-complexity)：

* 它必须至少有八个字符。
* 它必须包含来自以下四个类别中的三个类别的字符：
  * 拉丁文大写字母 (A-Z)
  * 拉丁文小写字母 (a-z)
  * 基本的 10 位数数字 (0-9)
  * 非字母数字字符，例如：感叹号 (!)、美元符号 ($)、数字符号 (#) 或百分比 (%)。
{% endhint %}

4、您可以更改其他变量，也可以使用它们的默认值。保存并退出此文件。

5、启动 Docker 容器。

使用 PowerShell 导航到已克隆的服务器存储库位置，进入 `dev` 文件夹然后运行下面的 docker 命令。

```bash
docker compose --profile mssql --profile mail up -d
```

这将启动 MSSQL 和本地邮件服务器容器，这应该适合大多数社区贡献。

运行 `docker compose` 命令后，您可以使用 [Docker Dashboard](https://docs.docker.com/desktop/dashboard/) 来管理您的容器。您应该能看到您的容器运行在 `bitwardenserver` 组下了。

{% hint style="danger" %}
首次运行 docker compose 后更改 `MSSQL_PASSWORD` 变量将需要重新创建存储卷。

**警告：这将删除您的开发数据库。**

为此，请运行

`docker compose --profile mssql down docker volume rm`

`bitwardenserver_edgesql_dev_data`

之后，重新运行步骤 5 中的 docker compose 命令。
{% endhint %}

### SQL Server <a href="#sql-server" id="sql-server"></a>

为了支持基于 ARM 的开发环境，例如 M1 Mac，我们使用 [Azure SQL Edge](https://hub.docker.com/\_/microsoft-azure-sql-edge) docker 容器而不是普通的 [Microsoft SQL Server](https://hub.docker.com/\_/microsoft-mssql-server) 容器。它的行为与常规 SQL Server 实例基本相同，并运行在 1433 端口上。

您可以使用 Azure Data Studio 并通过以下凭据连接到它：

* 服务器：localhost
* 用户名：sa
* 密码：（您在 `dev/.env` 中设置的密码）

### Mailcatcher <a href="#mailcatcher" id="mailcatcher"></a>

此服务器使用电子邮件进行许多用户交互。我们提供了一个预配置的 [MailCatcher](https://mailcatcher.me/) 实例，它可以捕获任何出站电子邮件并防止将其被发送到真实的电子邮件地址。您可以通过 `http://localhost:1080` 打开其 Web 界面。

### Azurite <a href="#azurite" id="azurite"></a>

{% hint style="info" %}
本部分仅适用于 Bitwarden 开发人员。
{% endhint %}

[Azurite](https://github.com/Azure/Azurite) 是一个 Azure 存储 API 的模拟器，支持 Blob、队列和表存储。我们使用它来最小化在云环境中开发所需的在线依赖。

要引导本地 Azurite 实例：

1、安装 Az 模块。在不提供任何用户反馈的情况下，这可能需要几分钟才能完成（它可能会显示为冻结）。

```bash
pwsh -Command "Install-Module -Name Az -Scope CurrentUser -Repository PSGallery -Force"
```

2、运行安装脚本：

```bash
pwsh setup_azurite.ps1
```

## 创建数据库 <a href="#create-database" id="create-database"></a>

现在 MSSQL 服务器已经运行了。下一步是创建 Bitwarden 服务器所使用的数据库。

我们提供了一个帮助脚本用于创建开发数据库 `vault_dev` 并同时运行所有迁移。

1、创建数据库并运行所有迁移：

```bash
pwsh migrate.ps1
```

2、您应该会收到各个迁移脚本已成功运行的确认信息：

```
Performing /mnt/migrator/DbScripts/2017-08-19_00_InitialSetup.sql
Performing /mnt/migrator/DbScripts/2017-08-22_00_LicenseCheckScripts.sql
Performing /mnt/migrator/DbScripts/2017-08-30_00_CollectionWriteOnly.sql
[...]
```

如果迁移被跳过，即使这是一个新的数据库，请参阅 [MSSQL 数据库故障排除](database/mssql.md#troubleshooting)。

{% hint style="info" %}
您需要定期重新运行迁移帮助程序脚本，以使您的本地开发数据库保持最新。有关详细信息，请参阅 [MSSQL 数据库](database/mssql.md)。
{% endhint %}

## 生成证书 <a href="#generate-certificates" id="generate-certificates"></a>

下一步是为本地开发创建两个自签名证书。我们提供了一个帮助脚本，用于生成这些证书并将它们添加到您系统的钥匙串或证书存储中。

### macOS

1、生成证书并将它们保存到您的钥匙串中：

```bash
sh create_certificates_mac.sh
```

2、系统将提示您输入 3 次密码。请创建一个复杂的密码，并在每次提示时将其输入 `Export Password` 字段中。您将无法复制/粘贴。

3、您将收到类似于以下内容的输出。下一部分将需要此信息。

```
Certificate fingerprints:
Identity Server Dev: 0BE8A0072214AB37C6928968752F698EEC3A68B5
Data Protection Dev: C3A6CECAD3DB580F91A52FC9C767FE780300D8AB
```

4、打开钥匙串访问并转到您的登录钥匙串。

5、双击 `Bitwarden Data Protection Dev` 和 `Bitwarden Identity Server Dev` 证书。

6、将每个证书的信任设置更改为「始终信任」。

### Windows

1、生成证书并将它们保存到证书存储中：

```bash
.\create_certificates_windows.ps1
```

2、您将收到类似于以下内容的输出。下一部分将需要此信息。

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::CurrentUser\My

Thumbprint                                Subject
----------                                -------
0BE8A0072214AB37C6928968752F698EEC3A68B5  CN=Bitwarden Identity Server Dev
C3A6CECAD3DB580F91A52FC9C767FE780300D8AB  CN=Bitwarden Data Protection Dev
```

### Linux <a href="#linux" id="linux"></a>

1、生成证书并将其保存到证书存储区：

```
./create_certificates_linux.sh
```

2、您将收到类似下面的输出。您需要收集生成的证书指纹，以便在[配置用户机密](guide.md#configure-user-secrets)部分使用。

```
Certificate fingerprints:
Identity Server Dev: 0BE8A0072214AB37C6928968752F698EEC3A68B5
Data Protection Dev: C3A6CECAD3DB580F91A52FC9C767FE780300D8AB
```

## 配置用户机密 <a href="#configure-user-secrets" id="configure-user-secrets"></a>

[用户机密](https://learn.microsoft.com/zh-cn/aspnet/core/security/app-secrets?view=aspnetcore-6.0)是一种开发人员管理应用程序设置的方法。它们覆盖每一个项目的 `appsettings.json` 中的设置。您的用户机密文件应与您打算覆盖的设置的 `appsettings.json` 文件的结构相匹配。

我们提供了一个帮助脚本，可以简化为服务器存储库中的所有项目设置用户机密的步骤。

1、获取 `secrets.json` 模板。我们需要获取 `secrets.json` 的初始版本，根据您自己的机密值对其进行修改。

导航到服务器仓库中的 `dev` 文件夹，复制 `secrets.json` 示例文件。

```bash
cp secrets.json.example secrets.json
```

2、使用您自己的值更新 `secrets.json`：

* `sqlServer` > `connectionString`：在指示的地方插入您的密码
* `identityServer` > `certificateThumbprint`：插入上一步中您的的 Identity 证书指纹
* `dataProtection` > `certificateThumbprint`：插入上一步中您的数据保护证书指纹
* `installation` > `id` 和 `key`：[申请一个主机安装 ID 和密钥](https://bitwarden.com/host/)，然后在此处插入
* `licenseDirectory`：将其设为空目录，这是用于存储上传的许可证文件的位置

3、完成 `secrets.json` 后，运行下面的命令将机密添加到每个 Bitwarden 服务器项目中：

```bash
pwsh setup_secrets.ps1
```

此帮助脚本还支持一个可选标志，该标志用于在重新应用它们之前删除所有现有的设置：

```bash
pwsh setup_secrets.ps1 -clear:$True
```

## 构建并运行服务器 <a href="#build-and-run-the-server" id="build-and-run-the-server"></a>

您现在已准备好构建和运行您的开发服务器了。

1、在存储库的根目录中打开一个新的终端窗口。

2、恢复 Identity 服务所需的 nuget 包：

```bash
cd src/Identity
dotnet restore
```

3、启动 Identity 服务：

```bash
dotnet run
```

4、通过导航到 [http://localhost:33656/.well-known/openid-configuration](http://localhost:33656/.well-known/openid-configuration) 来测试 Identity 服务是否处于活动状态。

5、在另一个终端窗口中，恢复 API 服务所需的 nuget 包：

```bash
cd src/Api
dotnet restore
```

6、启动 API 服务：

```bash
dotnet run
```

7、通过导航到 [http://localhost:4000/alive](http://localhost:4000/alive) 来测试 API 服务是否处于活动状态。

8、通过配置客户端的 API 和 Identity 端点将客户端连接到您的本地服务器。请参阅[更改客户端环境](https://help.ppgg.in/on-premises-hosting/connect-clients-to-your-instance)以及贡献文档中各客户端的说明。

{% hint style="info" %}
如果您无法连接到 API 或 Identity 项目，请检查终端输出以确认它们所运行的端口。
{% endhint %}

{% hint style="info" %}
我们建议之后继续使用 [Web Vault](../clients/web-vault/)，因为许多管理操作只能在其中执行。
{% endhint %}

## 调试 <a href="#debugging" id="debugging"></a>

{% hint style="info" %}
在 macOS 上，您必须先为每个项目运行 `dotnet restore`，然后才能在调试器中启动它。
{% endhint %}

### Visual Studio <a href="#visual-studio" id="visual-studio"></a>

调试：

* 在 Windows 上，右键点击每个项目 → 点击 **Debug** → 点击 **Start New Instance**
* 在 macOS 上，右键点击每个项目 → 点击 **Start Debugging Project**

### Rider <a href="#rider" id="rider"></a>

通过分别点击每一个项目的「play」按钮来启动 Api 项目和 Identity 项目。
