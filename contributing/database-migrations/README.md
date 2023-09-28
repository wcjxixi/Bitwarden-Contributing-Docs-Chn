# 数据库迁移

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/database-migrations/)
{% endhint %}

## 应用迁移 <a href="#applying-migrations" id="applying-migrations"></a>

我们使用 `migrate.ps1` PowerShell 脚本将迁移应用到本地开发数据库。此脚本可以处理我们支持的不同数据库提供程序。

有关如何使用 `migrate.ps1` 的说明，请参阅 [MSSQL](../../getting-started/server/mssql/#updating-the-database) 和[实体框架](../../getting-started/server/ef/#migrations)的入门部分。

## 为新的更改创建迁移 <a href="#creating-migrations-for-new-changes" id="creating-migrations-for-new-changes"></a>

任何数据库的更改都必须编写为我们的主 DBMS - MSSQL 以及实体框架的迁移脚本。针对每个提供商，请按照以下说明进行操作。

### MSSQL 迁移 <a href="#mssql-migrations" id="mssql-migrations"></a>

{% hint style="success" %}
我们建议首先阅读[进化的数据库设计](../../getting-started/server/mssql/edd.md)和 [T-SQL 代码样式](../../code-style/t-sql.md)，因为它们对我们如何编写迁移的有很大的影响。
{% endhint %}

根据[进化数据库设计](../../getting-started/server/mssql/edd.md)的原则，每个更改都需要考虑分为两部分：

1. 向后兼容的过渡迁移
2. 非向后兼容的最终迁移

更改可能无需非向后兼容的结束阶段（即所有更改的最终形式可能是向后兼容的）。在这种情况下，只需要进行一个阶段的更改即可。

#### 向后兼容迁移 <a href="#backwards-compatible-migration" id="backwards-compatible-migration"></a>

1. 修改 `src/Sql/dbo` 中的源 `.sql` 文件。
2. 编写迁移脚本，并将其放在 `util/Migrator/DbScripts` 中。每个脚本必须以当前日期为前缀。

#### 非向后兼容迁移 <a href="#non-backwards-compatible-migration" id="non-backwards-compatible-migration"></a>

1. 将相关的 `.sql` 文件从 `src/Sql/dbo` 复制到 `src/Sql/dbo_finalization` 。
2. 移除不再需要的向后兼容性。
3. 编写一个新的 Migration 并将其放置在 `src/Migrator/DbScripts_finalization` 中。将其命名为 `YYYY-0M-FinalizationMigration.sql` 。
   * 通常，迁移被设计为按顺序运行。但是，由于 DbScripts\_future 中的迁移可能会无序运行，因此必须小心确保它们与 DbScripts 的更改保持兼容。为了实现这一目标，我们只保留一个迁移，它执行所有向后不兼容的架构更改。

### EF 迁移 <a href="#ef-migrations" id="ef-migrations"></a>

如果更改数据库架构，则必须创建 EF 迁移脚本以确保 EF 数据库跟上这些更改。开发人员必须这样做，并将迁移包含在他们的 PR 中。

要创建这些脚本，您必须首先根据需要更新 `Core/Entities` 中的数据模型。这将用于为我们的每个 EF 目标生成迁移。

模型更新后，导航到 `server` 存储库中的 `dev` 目录并执行 `ef_migrate.ps1` PowerShell 命令。您应该提供迁移的名称，将其作为唯一的参数：

```bash
pwsh ef_migrate.ps1 [NAME_OF_MIGRATION]
```

这将生成迁移，然后应将其包含在您的 PR 中。

### \[未实现] 手动 MSSQL 迁移 <a href="#not-yet-implemented-manual-mssql-migrations" id="not-yet-implemented-manual-mssql-migrations"></a>

可能需要在正常更新过程之外运行迁移。这些类型的迁移应该出于非常特殊的目的而保留。其中一个原因可能是索引重建。

1. 编写一个带有当前日期前缀的新迁移并将其放置在 `src/Migrator/DbScripts_manual` 中
2. 在我们的云环境中运行它并且我们对结果感到满意后，创建一个 PR 将其移动到 `DbScripts` 。这将使其能够由我们的迁移器进程在全新安装的云和自托管环境，以及自托管中运行
