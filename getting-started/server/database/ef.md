# 实体框架

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/ef/)
{% endhint %}

{% hint style="danger" %}
实体框架 (EF) 支持仍处于测试阶段，不适合生产型数据库。
{% endhint %}

{% hint style="info" %}
本页面指的是建立一个 Bitwarden 实例来进行开发，如需了解如何测试个人使用的 EF 部署（例如 Bitwarden Unified），请参阅[帮助文档](https://help.ppgg.in/self-hosting/install-and-deploy-guides/install-and-deploy-unified-beta)。
{% endhint %}

## 背景 <a href="#background" id="background"></a>

实体框架 (EF) 是一个 ORM 框架，充当数据库的包装器。它允许我们支持多个（非 MSSQL）数据库，而无需为每个数据库维护迁移和查询脚本。

我们的 EF 实现目前支持 Postgres、MySQL 和 SQLite3。

## 设置 EF 数据库 <a href="#setting-up-ef-databases" id="setting-up-ef-databases"></a>

这里的工作流程与普通的 MSSQL 实现大致相同：设置 docker 容器、配置用户机密，并按时间顺序针对相关数据库运行脚本文件夹中的脚本。

### 要求 <a href="#requirements" id="requirements"></a>

* 一个正常运行的本地开发服务器
* Docker
* 一种在服务器项目中管理用户机密的方法 - 请参阅[用户机密参考](../../../contributing/user-secrets.md)
* 数据库管理软件（参阅[工具推荐](../../tools.md)）
* `dotnet` cli
* `dotnet` cli [实体框架核心工具](https://learn.microsoft.com/zh-cn/ef/core/cli/dotnet)

您可以配置多个数据库，并通过改变 `globalSettings:databaseProvider` 用户机密的值以在它们之间切换。您不需要删除您的连接字符串。

### 数据库设置 <a href="#database-setup" id="database-setup"></a>

{% tabs %}
{% tab title="PostgreSQL" %}
在您的服务器存储库的 `dev` 文件夹中，运行：

```bash
docker compose --profile postgres up
```
{% endtab %}

{% tab title="MySQL" %}
在您的服务器存储库的 `dev` 文件夹中，运行：

```bash
docker compose --profile mysql up
```
{% endtab %}

{% tab title="SQLite" %}
选择数据库文件的位置。您可以使用服务器存储库的 `dev` 文件夹，为此目的，git 配置为忽略此存储库中的 `.db` 文件。
{% endtab %}
{% endtabs %}

### 用户机密 <a href="#user-secrets" id="user-secrets"></a>

将以下值添加到您的 API、身份和管理员用户机密中。

{% tabs %}
{% tab title="PostgreSQL" %}
请务必根据需要更改 root 密码等信息。如果您已经拥有这些机密，请确保更新现有的值，而不是创建一个新的值：

```json
"globalSettings:databaseProvider": "postgres",
"globalSettings:postgreSql:connectionString": "Host=localhost;Username=postgres;Password=example;Database=vault_dev;Include Error Detail=true",
```
{% endtab %}

{% tab title="MySQL" %}
请务必根据需要更改 root 密码等信息。如果您已经拥有这些机密，请确保更新现有的值，而不是创建一个新的值：

```json
"globalSettings:databaseProvider": "mysql",
"globalSettings:mySql:connectionString": "server=localhost;uid=root;pwd=example;database=vault_dev",
```
{% endtab %}

{% tab title="SQLite" %}
将以下值添加到您的 API、身份和管理员用户机密中。注意，您必须设置数据源路径。使用在[数据库设置](ef.md#database-setup)中选择的文件位置：

```json
"globalSettings:databaseProvider": "sqlite",
"globalSettings:sqlite:connectionString": "Data Source=/path/to/your/server/repo/dev/db/bitwarden.db",
```
{% endtab %}
{% endtabs %}

{% hint style="info" %}
更改 `secrets.json` 文件后，记得运行 `pwsh setup_secrets.ps1 -clear` 使更改生效。
{% endhint %}

### 迁移 <a href="#migrations" id="migrations"></a>

{% tabs %}
{% tab title="PostgreSQL" %}
在 `dev` 文件夹中运行以下命令将数据库更新到最新的迁移：

```bash
pwsh migrate.ps1 -postgres
```

`migrate.ps1` 中的 `-postgres` 标志将用于运行 `dotnet ef` 命令以执行迁移。

您还可以使用以下命令同时为所有数据库提供程序运行迁移：

```bash
pwsh migrate.ps1 -all
```
{% endtab %}

{% tab title="MySQL" %}
在 `dev` 文件夹中运行以下命令将数据库更新到最新的迁移：

```bash
pwsh migrate.ps1 -mysql
```

`migrate.ps1` 中的 `-mysql` 标志将用于运行 `dotnet ef` 命令以执行迁移。

您还可以使用以下命令同时为所有数据库提供程序运行迁移：

```bash
pwsh migrate.ps1 -all
```
{% endtab %}

{% tab title="SQLite" %}
在 `dev` 文件夹中运行以下命令将数据库更新到最新的迁移：

```bash
pwsh migrate.ps1 -sqlite
```

`migrate.ps1` 中的 `-sqlite` 标志将用于运行 `dotnet ef` 命令以执行迁移。

您还可以使用以下命令同时为所有数据库提供程序运行迁移：

```bash
pwsh migrate.ps1 -all
```
{% endtab %}
{% endtabs %}

### 验证（可选） <a href="#optional-verify" id="optional-verify"></a>

如果您想验证一切工作是否正常：

* 检查数据库表以确保所有内容均已创建
* 使用 `dotnet test` 从您的服务器项目的根部运行集成测试。注意：这需要一个已配置好的 MSSQL 数据库。您可能还需要设置其他 EF 提供程序才能通过测试。

## 测试 EF 更改 <a href="#testing-ef-changes" id="testing-ef-changes"></a>

在您的 `server/dev/secrets.json` 文件中查找，或在 json 结构的根部添加此机密块：

```
"databases:0:type": "Postgres",
"databases:0:connectionString": "Host=localhost;Username=postgres;Password=_________;Database=ef_test",
"databases:0:enabled": "true",
"databases:1:type": "Sqlite",
"databases:1:enabled": "true",
"databases:1:connectionString": "Data Source=_________",
"databases:2:type": "MySql",
"databases:2:connectionString": "server=localhost;uid=root;pwd=_________;database=ef_test",
"databases:2:enabled": "true",
"databases:3:type": "SqlServer",
"databases:3:connectionString": "Server=localhost;Database=ef_test;User Id=SA;Password=_________;Encrypt=True;TrustServerCertificate=True;",
"databases:3:enabled": "true"
```

{% hint style="info" %}
示例数据库索引 + 类型组合是工具运行所必需的，并可支持同一数据库的多个版本同时运行测试。
{% endhint %}

此块用于为每个支持的提供程序类型测试数据库。集成测试将连接到这些数据库。如果尚未更新，则应更新这些连接字符串的密码，以使其与现有数据库相匹配。如果您的 `server/dev/secrets.json` 文件中根本没有这些设置，只需将其添加到底部即可。这些设置不会出现在 `globalSettings` 中。然后运行 `pwsh setup_secrets.ps1 -clear` 将其应用到本地项目。

将连接字符串应用到项目后：使用 `pwsh server/dev/migrate.ps1 --all` 确保数据库已全部迁移。然后就可以使用 `dotnet test 从 test/Infrastructure.IntegrationTest` 文件夹运行 EF 测试了。
