# 进化数据库设计

{% hint style="info" %}
开始对应的[官方页面地址](https://contributing.bitwarden.com/contributing/database-migrations/edd)
{% endhint %}

在 Bitwarden，我们遵循[进化数据库设计 (EDD)](https://en.wikipedia.org/wiki/Evolutionary\_database\_design)。EDD 描述了一个过程，在这个过程中，数据库架构被持续更新，同时通过使用数据库过渡阶段仍然确保与旧版本的兼容性。

Bitwarden 还需要支持：

* **零停机部署**：这意味着应用程序的多个版本将在部署窗口期间同时运行。
* **代码回滚**：代码中的严重缺陷应该能够回滚到以前的版本。

为了满足这些附加要求，数据库架构必须支持以前版本的服务器。

## 设计 <a href="#design" id="design"></a>

数据库更改可以分为两类：破坏性更改和非破坏性更改\[1]。如果没有相应的代码更改，破坏性更改会阻止现有功能按预期工作。非破坏性更改则相反：数据库更改不需要更改代码即可允许非应用程序继续按预期工作。

### 非破坏性改更改 <a href="#non-destructive-changes" id="non-destructive-changes"></a>

通过在数据库表、视图和存储过程中混合使用可空字段和默认值，可以以向后兼容的方式设计许多数据库更改。这确保了可以在没有新列的情况下调用存储过程，并允许它们同时使用旧代码和新代码运行。

### 破坏性更改 <a href="#destructive-changes" id="destructive-changes"></a>

任何不能以非破坏性方式完成的更改都是破坏性更改。这可以像添加一个不可为空的列一样简单，其中需要从现有字段计算值，或者重命名现有列。为了处理破坏性更改，有必要将它们分为三个阶段：开始、过渡和结束，如下图所示。

{% embed url="https://contributing.bitwarden.com/assets/images/transitions-b5d691da2f06e34d8a4e13a3ab25a4b8.png" %}

值得注意的是，_重构阶段_通常是滚动的，一个重构的_结束阶段_是另一个重构的_过渡阶段_。下表详细说明了在哪个数据库阶段需要支持哪些应用程序版本。

| Database Phase                   | Release X | Release X+1 | Release X+2 |
| -------------------------------- | --------- | ----------- | ----------- |
| b7d28821bbaf47a6a67a74b15772b0bf | ✅         | ❌           | ❌           |
| Transition                       | ✅         | ✅           | ❌           |
| End                              | ❌         | ✅           | ✅           |

### 迁移 <a href="#migrations" id="migrations"></a>

#### 初始化迁移 <a href="#initial-migration" id="initial-migration"></a>

#### 过渡迁移 <a href="#transition-migration" id="transition-migration"></a>

#### 结束迁移 <a href="#finalization-migration" id="finalization-migration"></a>

### 示例 <a href="#example" id="example"></a>

让我们看一个例子，重命名列的重构如下图所示。

{% embed url="https://contributing.bitwarden.com/assets/images/rename-column-1f4999a32d438f4a75649089e85b4c18.gif" %}

在这个重构中，我们将 `Customer` 表中的 `Fname` 列重命名为 `FirstName`。这可以很容易地使用常规的 `Alter Table` 语句来实现，但这将破坏与现有运行代码的兼容性。相反，让我们来看看如何逐步重构这个表。

我们将首先创建一个迁移，将 `FirstName` 列添加到客户表中。同时，我们还将更新存储过程，以同步 `FName` 和 `FirstName` 之间的内容，确保新旧服务器版本可以同时运行。同步代码在下面的代码片断中强调。

之后，新的服务器版本将被部署，一切检查完毕，现有数据将使用_数据迁移_脚本进行迁移。这主要是将 `FName` 复制到 `FirstName` 列。

最后，_第二次迁移_将被运行，它将删除旧的列并更新存储过程以删除同步逻辑。

#### 迁移 <a href="#migrations" id="migrations"></a>

{% hint style="info" %}
所有的数据库迁移都应该支持多次运行；即使随后的运行不执行任何操作。
{% endhint %}

{% tabs %}
{% tab title="初始迁移" %}
```sql
-- Add Column
IF COL_LENGTH('[dbo].[Customer]', 'FirstName') IS NULL
BEGIN
    ALTER TABLE
        [dbo].[Customer]
    ADD
        [FirstName] NVARCHAR(MAX) NULL
END
GO

-- Drop existing SPROC
IF OBJECT_ID('[dbo].[Customer_Create]') IS NOT NULL
BEGIN
    DROP PROCEDURE [dbo].[Customer_Create]
END
GO

-- Create the new SPROC
CREATE PROCEDURE [dbo].[Customer_Create]
    @CustomerId UNIQUEIDENTIFIER OUTPUT,
    @FName NVARCHAR(MAX) = NULL, -- Deprecated as of YYYY-MM-DD
    @FirstName NVARCHAR(MAX) = NULL
AS
BEGIN
    SET NOCOUNT ON

    SET @FirstName = COALESCE(@FirstName, @FName);

    INSERT INTO [dbo].[Customer]
    (
        [CustomerId],
        [FName],
        [FirstName]
    )
    VALUES
    (
        @CustomerId,
        @FirstName,
        @FirstName
    )
END
```
{% endtab %}

{% tab title="过渡迁移" %}
```sql
UPDATE [dbo].Customer SET
    FirstName=FName
WHERE FirstName IS NULL
```
{% endtab %}

{% tab title="结束迁移" %}
```sql
-- Remove Column
IF COL_LENGTH('[dbo].[Customer]', 'FName') IS NOT NULL
BEGIN
    ALTER TABLE
        [dbo].[Customer]
    DROP COLUMN
        [FName]
END
GO

-- Drop existing SPROC
IF OBJECT_ID('[dbo].[Customer_Create]') IS NOT NULL
BEGIN
    DROP PROCEDURE [dbo].[Customer_Create]
END
GO

-- Create the new SPROC
CREATE PROCEDURE [dbo].[Customer_Create]
    @CustomerId UNIQUEIDENTIFIER OUTPUT,
    @FirstName NVARCHAR(MAX) = NULL
AS
BEGIN
    SET NOCOUNT ON

    INSERT INTO [dbo].[Customer]
    (
        [CustomerId],
        [FirstName]
    )
    VALUES
    (
        @CustomerId,
        @FirstName
    )
END
```
{% endtab %}
{% endtabs %}

## 部署编排 <a href="#deployment-orchestration" id="deployment-orchestration"></a>

***

***

### 在线环境 <a href="#online-environments" id="online-environments"></a>

### 离线环境 <a href="#offline-environments" id="offline-environments"></a>

## 回滚 <a href="#rollbacks" id="rollbacks"></a>

如果服务器发布失败，需要回滚，这应该很简单，只要重新部署以前的版本就可以了。数据库将**处于**过渡阶段，直到可以发布修补程序，并且服务器可以被更新。

我们的目标是快速解决这个问题，并重新部署固定的代码，以尽量减少数据库停留在过渡阶段的时间。如果需要完全提取某个功能，则需要编写新的迁移以撤消数据库更改，并且还需要更新未来的迁移以适应数据库更改。通常不建议这样做，因为需要重新访问挂起的迁移（对于其他版本）。

## 测试 <a href="#testing" id="testing"></a>

在合并 PR 之前，请确保数据库更改在当前发布的版本上运行良好。我们目前没有为此提供自动化测试套件，开发人员可以确保他们的数据库更改针对当前发布的版本正确运行。

## 延伸阅读 <a href="#further-reading" id="further-reading"></a>

* [进化数据库设计](https://martinfowler.com/articles/evodb.html)（特别是[所有数据库更改都是数据库重构](https://martinfowler.com/articles/evodb.html#AllDatabaseChangesAreMigrations)）
* [敏捷数据 (AD) 方法](http://agiledata.org/)（特别是[数据库目录重构](http://agiledata.org/essays/databaseRefactoringCatalog.html)）
* [重构数据库：进化数据库](https://databaserefactoring.com/)
* 重构数据库：进化数据库设计（Addison-Wesley 签名系列 (Fowler)）ISBN-10: 0321774515
