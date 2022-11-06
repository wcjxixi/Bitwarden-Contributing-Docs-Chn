# 实体框架

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/server/ef/)
{% endhint %}

{% hint style="danger" %}
实体框架支持仍处于测试阶段，不适合生产型数据库。
{% endhint %}

## 背景 <a href="#background" id="background"></a>

实体框架 (EF) 是一个 ORM 框架，充当数据库的包装器。它允许我们支持多个（非 MSSQL）数据库，而无需为每个数据库维护迁移和查询脚本。

我们的 EF 实现目前支持 Postgres 和 mySQL。

## 设置 EF 数据库 <a href="#setting-up-ef-databases" id="setting-up-ef-databases"></a>

这里的工作流程与普通的 MSSQL 实现大致相同：设置 docker 容器，配置用户机密，并按时间顺序针对相关数据库运行脚本文件夹中的脚本。

### 要求 <a href="#requirements" id="requirements"></a>

* 一个正常运行的本地开发服务器
* Docker
* 一种在服务器项目中管理用户机密的方法 - 请参阅[用户机密参考](../user-secrets.md)
* 数据库管理软件（例如用于 Postgres 的 pgAdmin4 或用于 mySQL 的 DBeaver）
* `dotnet` cli
* `dotnet` cli [实体框架核心工具](https://learn.microsoft.com/zh-cn/ef/core/cli/dotnet)

您可以配置多个数据库，并通过改变 `globalSettings:databaseProvider` 用户机密的值以在它们之间切换。您不需要删除您的连接字符串。

### Postgres <a href="#postgres" id="postgres"></a>

1、在服务器存储库的 `dev` 文件夹中，运行：

```
docker compose --profile postgres up
```

2、在您的 API 和个人信息的用户机密中添加以下值，根据需要更改 root 密码等信息。如果您已经拥有这些机密，请确保更新现有的值，而不是创建新的值：

```
"globalSettings:databaseProvider": "postgres",
"globalSettings:postgreSql:connectionString": "Host=localhost;Username=postgres;Password=example;Database=vault_dev;Include Error Detail=true",
```

3、在 `dev` 文件夹中运行以下命令将数据库更新到最新的迁移：

```
pwsh migrate.ps1 -postgres
```

4、可选：验证一切工作是否正常。

* 检查数据库表以确保所有内容都已创建
* 使用 `dotnet test` 从您的服务器项目的根部运行集成测试。注意：这需要一个已配置好的 MSSQL 数据库。您可能还需要设置一个 MySql 数据库才能通过测试

### MySql <a href="#mysql" id="mysql"></a>

如果您使用的是 M1 机器，则需要为 docker-compose.yml 文件的 mysql 服务添加 `platform:linux/amd64` 标签。

1、在您的服务器存储库的 `dev` 文件夹中，运行：

```
docker compose --profile mysql up
```

2

3

4

## 生成 EF 迁移 <a href="#generating-ef-migrations" id="generating-ef-migrations"></a>

### 指示 <a href="#instructions" id="instructions"></a>
