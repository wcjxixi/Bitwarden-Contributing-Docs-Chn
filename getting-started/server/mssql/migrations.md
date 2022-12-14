# 迁移

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/mssql/migrations)
{% endhint %}

{% hint style="info" %}
建议首先阅读[进化数据库设计](edd.md)，因为它对我们如何编写迁移有重大影响。
{% endhint %}

## 存储库 <a href="#repositories" id="repositories"></a>

我们使用[存储库模式](https://learn.microsoft.com/zh-cn/dotnet/architecture/microservices/microservice-ddd-cqrs-patterns/infrastructure-persistence-layer-design)和使用 [Dapper](https://github.com/DapperLib/Dapper) 编写的 MSSQL 存储库。每个存储库方法依次调用一个存储过程，该过程主要从视图中获取数据。

## 更改数据库 <a href="#changing-the-database" id="changing-the-database"></a>

当我们遵循[进化数据库设计](edd.md)时，每个更改都需要分成两部分。一个向后兼容的过渡阶段，一个非向后兼容的过渡阶段。

### 最佳实践 <a href="#best-practices" id="best-practices"></a>

在编写迁移脚本时，我们遵循了一些最佳实践。请查看 [T-SQL 代码样式](../../../code-style/t-sql.md)以获取更多详细信息。

### 向后兼容 <a href="#backwards-compatible" id="backwards-compatible"></a>

1. 修改 `src/Sql/dbo` 中的源 `.sql` 文件。
2. 编写一个迁移脚本，并将其放在 `util/Migrator/DbScripts` 中。每个脚本必须以当前日期为前缀。

### 不向后兼容 <a href="#non-backwards-compatible" id="non-backwards-compatible"></a>

1. 将相关的 `.sql` 文件从 `src/Sql/dbo` 复制到 `src/Sql/dbo_future`。
2. 删除不再需要的向后兼容。
3. 编写一个新的迁移并将其放在 `src/Migrator/DbScripts_future` 中，将其命名为 `YYYY-0M-FutureMigration.sql`。
   * 通常，迁移被设计为按顺序运行。但是，由于 DbScripts\_future 中的迁移可能会乱序运行，因此必须注意确保它们与 DbScripts 的更改保持兼容。为了实现这一点，我们只保留一个迁移，它执行所有向后不兼容的模式更改。
