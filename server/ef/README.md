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

* 一个正常工作的本地开发服务器
* Docker
* 一种在服务器项目中管理用户机密的方法 - 请参阅[用户机密参考](../user-secrets.md)
* 数据库管理软件（例如用于 Postgres 的 pgAdmin4 或用于 mySQL 的 DBeaver）
* `dotnet` cli
* `dotnet` cli [实体框架核心工具](https://learn.microsoft.com/zh-cn/ef/core/cli/dotnet)

您可以配置多个数据库，并通过改变 `globalSettings:databaseProvider` 用户机密的值以在它们之间切换。您不需要删除您的连接字符串。

### Postgres <a href="#postgres" id="postgres"></a>

1、在服务器存储库的 dev 文件夹中，运行：

2、

3、

4、

### MySql <a href="#mysql" id="mysql"></a>

## 生成 EF 迁移 <a href="#generating-ef-migrations" id="generating-ef-migrations"></a>

### 指示 <a href="#instructions" id="instructions"></a>
