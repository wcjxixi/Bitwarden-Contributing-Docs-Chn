# 实体框架

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/server/ef/)
{% endhint %}

{% hint style="danger" %}
实体框架支持仍处于测试阶段，不适合生产型数据库。
{% endhint %}

## 背景 <a href="#background" id="background"></a>

实体框架 (EF) 是一个 ORM 框架，充当数据库的包装器。它允许我们支持多个（非 MSSQL）数据库，而无需为每个数据库维护迁移和查询脚本。

我们的 EF 实现目前支持 Postgres 和 MySQL。

## 设置 EF 数据库 <a href="#setting-up-ef-databases" id="setting-up-ef-databases"></a>

这里的工作流程与普通的 MSSQL 实现大致相同：设置 docker 容器，配置用户机密，并按时间顺序针对相关数据库运行脚本文件夹中的脚本。

### 要求 <a href="#requirements" id="requirements"></a>

* 一个正常运行的本地开发服务器
* Docker
* 一种在服务器项目中管理用户机密的方法 - 请参阅[用户机密参考](../user-secrets.md)
* 数据库管理软件（例如用于 Postgres 的 pgAdmin4 或用于 MySQL 的 DBeaver）
* `dotnet` cli
* `dotnet` cli [实体框架核心工具](https://learn.microsoft.com/zh-cn/ef/core/cli/dotnet)

您可以配置多个数据库，并通过改变 `globalSettings:databaseProvider` 用户机密的值以在它们之间切换。您不需要删除您的连接字符串。

### Postgres <a href="#postgres" id="postgres"></a>

1、在服务器存储库的 `dev` 文件夹中，运行：

```docker
docker compose --profile postgres up
```

2、在您的 API 和个人信息的用户机密中添加以下值，根据需要更改 root 密码等信息。如果您已经有这些机密，请确保更新现有的值，而不是创建新的值：

```systemd
"globalSettings:databaseProvider": "postgres",
"globalSettings:postgreSql:connectionString": "Host=localhost;Username=postgres;Password=example;Database=vault_dev;Include Error Detail=true",
```

3、在 `dev` 文件夹中运行以下命令将数据库更新到最新的迁移：

```
pwsh migrate.ps1 -postgres
```

4、可选：验证一切工作是否正常。

* 检查数据库表以确保所有内容都已创建
* 使用 `dotnet test` 从您的服务器项目的根部运行集成测试。注意：这需要一个已配置好的 MSSQL 数据库。您可能还需要设置一个 MySQL 据库才能通过测试

### MySQL <a href="#mysql" id="mysql"></a>

如果您使用的是 M1 机器，则需要为 docker-compose.yml 文件的 mysql 服务添加 `platform:linux/amd64` 标签。

1、在您的服务器存储库的 `dev` 文件夹中，运行：

```docker
docker compose --profile mysql up
```

2、在您的 API 和个人信息的用户机密中添加以下值，根据需要更改 root 密码等信息。如果您已经有这些机密，请确保更新现有的值，而不是创建新的值：

```systemd
"globalSettings:databaseProvider": "mysql",
"globalSettings:mySql:connectionString": "server=localhost;uid=root;pwd=example;database=vault_dev",
```

3、在 `dev` 文件夹中运行以下命令将数据库更新到最新的迁移：

```
pwsh migrate.ps1 -mysql
```

{% hint style="info" %}
`pwsh migrate.ps1 -all` 将为所有数据库提供程序运行迁移
{% endhint %}

4、可选：验证一切工作是否正常。

* 检查数据库表以确保所有内容都已创建
* 使用 `dotnet test` 从您的服务器项目的根部运行集成测试。注意：这需要一个已配置好的 MSSQL 数据库。您可能还需要设置一个 Postgres 数据库才能通过测试

## 生成 EF 迁移 <a href="#generating-ef-migrations" id="generating-ef-migrations"></a>

如果您更改了数据库架构，则必须创建一个 EF 迁移脚本，以确保 EF 数据库与这些变化保持同步。开发人员必须这样做，并且要在他们的 PR 中包含迁移的内容。

### 步骤 <a href="#instructions" id="instructions"></a>

1、根据需要更新您的 `Core/Entities` 中的数据模型

2、确保您 API 中的用户机密具有正确的提供程序（该提供程序在 `databaseProvider` 字段中有配置）。该值应与您为其生成迁移的提供程序相匹配

3、在您要为其生成迁移的项目（`util/PostgresMigrations` 或 `util/MySqlMigrations`）的根目录中打开一个终端

4、生成迁移。这应该与相应的 MSSQL 迁移具有相同的名称（日期除外，该工具将自动添加该日期前缀）：

```
dotnet ef migrations add [NAME_OF_MIGRATION]
```

5、生成迁移 SQL 脚本：

```
dotnet ef migrations script [LATEST_PREVIOUS_MIGRATION]
```

6、针对您的数据库运行 SQL 脚本

7、将脚本输出保存在 `Scripts` 文件夹中
