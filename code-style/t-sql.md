# T-SQL

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/code-style/sql/)
{% endhint %}

## 部署脚本 <a href="#deployment-scripts" id="deployment-scripts"></a>

有一些特定的方式来构建部署脚本。这些标准的目标是确保脚本可以重新运行。我们从不打算在一个环境中多次运行脚本，但脚本应该支持它。

### 表 <a href="#tables" id="tables"></a>

#### 创建表 <a href="#creating-a-table" id="creating-a-table"></a>

建表时，首先要检查此表是否存在：

```plsql
IF OBJECT_ID('[dbo].[{table_name}]') IS NULL
BEGIN
    CREATE TABLE [dbo].[{table_name}] (
        [Id]                UNIQUEIDENTIFIER NOT NULL,
        '...
        CONSTRAINT [PK_{table_name}] PRIMARY KEY CLUSTERED ([Id] ASC)
    );
END
GO
```

#### 添加列 <a href="#adding-a-column-to-a-table" id="adding-a-column-to-a-table"></a>

您必须先检查该列是否存在，然后才能将其添加到表中：

```sql
IF COL_LENGTH('[dbo].[{table_name}]', '{column_name}') IS NULL
BEGIN
    ALTER TABLE [dbo].[{table_name}]
        ADD [{column_name}] {DATATYPE} {NULL|NOT NULL};
END
GO
```

向现有表添加新的 `NOT NULL` 列时，请重新评估是否需要它。不要害怕在 C# 和应用程序层中使用 Nullable 原语，这总比对 DB 的每一行使用默认值导致占用不必要的空间要好，特别是对于新功能或新特性，需要占用很长时间，才能对大多数行级数据（如果有的话）有用。

如果您决定添加 `NOT NULL` 列，请**使用 DEFAULT 约束**，而不是创建列、更新行以及更改列。这对于像 `dbo.User` 和 `dbo.Cipher` 这样的大型表尤其重要。我们在 Azure 中的 SQL Server 版本使用元数据作为默认约束。这意味着我们可以更新默认的列值，而**无需**更新表中的每一行（这将使用大量的 DB I/O）。

这个很慢：

```sql
IF COL_LENGTH('[dbo].[Table]', 'Column') IS NULL
BEGIN
    ALTER TABLE
        [dbo].[Table]
    ADD
        [Column] INT NULL
END
GO

UPDATE
    [dbo].[Table]
SET
    [Column] = 0
WHERE
    [Column] IS NULL
GO

ALTER TABLE
    [dbo].[Column]
ALTER COLUMN
    [Column] INT NOT NULL
GO
```

这个更好：

```sql
IF COL_LENGTH('[dbo].[Table]', 'Column' IS NULL
BEGIN
    ALTER TABLE
        [dbo].[Column]
    ADD
        [Column] INT NOT NULL CONSTRAINT D_Table_Column DEFAULT 0
END
GO
```

#### 更改列数据类型 <a href="#changing-a-column-data-type" id="changing-a-column-data-type"></a>

您必须将 `ALTER TABLE` 语句包装在条件块中，以便脚本的后续运行不会再次修改数据类型。

```sql
IF EXISTS (
    SELECT *
    FROM INFORMATION_SCHEMA.COLUMNS
    WHERE COLUMN_NAME = '{column_name}' AND
        DATA_TYPE = '{datatype}' AND
        TABLE_NAME = '{table_name}')
BEGIN
    ALTER TABLE [dbo].[{table_name}]
    ALTER COLUMN [{column_name}] {NEW_TYPE} {NULL|NOT NULL}
END
GO
```

#### 调整元数据 <a href="#adjusting-metadata" id="adjusting-metadata"></a>

调整表时，您还应该检查该表是否被引用在任何视图中。如果视图中的基础表已被修改，则应运行 `sp_refreshview` 以重新生成视图元数据。

```sql
EXECUTE sp_refreshview N'[dbo].[{view_name}]
```

### 创建或修改视图 <a href="#create-or-modify-a-view" id="create-or-modify-a-view"></a>

我们建议使用 `CREATE OR ALTER` 语法来添加或修改视图。

```sql
CREATE OR ALTER VIEW [dbo].[{view_name}]
AS
SELECT
    *
FROM
    [dbo].[{table_name}]
GO
```

#### 调整元数据 <a href="#adjusting-metadata" id="adjusting-metadata"></a>

更改视图时，您可能还需要刷新引用该视图或函数的模块（存储了过程或函数），以便 SQL Server 更新其统计信息并编译对它的引用。

```sql
IF OBJECT_ID('[dbo].[{procedure_or_function}]') IS NOT NULL
BEGIN
    EXECUTE sp_refreshsqlmodule N'[dbo].[{procedure_or_function}]';
END
GO
```

### 创建或修改函数或存储过程 <a href="#create-or-modify-a-function-or-stored-procedure" id="create-or-modify-a-function-or-stored-procedure"></a>

我们建议使用 `CREATE OR ALTER` 语法来添加或修改函数或存储过程。

```sql
CREATE OR ALTER {PROCEDURE|FUNCTION} [dbo].[{sproc_or_func_name}]
'...
GO
```

### 创建或修改索引 <a href="#create-or-modify-an-index" id="create-or-modify-an-index"></a>

在创建索引时，尤其是在频繁使用的表上，我们的生产数据库很容易出现脱机、无法使用、CPU 达到 100% 以及许多其他不良行为。通常最好使用在线索引构建来执行此操作，以免锁定底层表。这可能会导致索引操作花费更长的时间，但您不会创建一个底层架构表锁来阻止所有读取和连接到该表，而只会在操作过程中锁定表的更新。

一个很好的例子是在 `dbo.Cipher` 或 `dbo.OrganizationUser` 上创建索引时，由于这些表是重读表，锁定会导致 Azure SQL 中异常高的 CPU、等待时间和 Worker 耗尽。

```sql
CREATE NONCLUSTERED INDEX [IX_OrganizationUser_UserIdOrganizationIdStatus]
   ON [dbo].[OrganizationUser]([UserId] ASC, [OrganizationId] ASC, [Status] ASC)
   INCLUDE ([AccessAll])
   WITH (ONLINE = ON); -- ** THIS ENSURES ONLINE **
```
