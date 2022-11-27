# 进化的数据库设计

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/server/mssql/edd)
{% endhint %}

在 Bitwarden，我们遵循[进化数据库设计 (EDD)](https://en.wikipedia.org/wiki/Evolutionary\_database\_design)。EDD 描述了一个过程，在这个过程中，数据库架构被持续更新，同时通过使用数据库过渡阶段仍然确保与旧版本的兼容性。

简而言之，Bitwarden服务器的数据库架构必须**支持**以前的服务器版本。数据库迁移将在代码部署前进行，在版本回滚的情况下，数据库架构将**不会**被更新。

## 设计 <a href="#design" id="design"></a>

数据库表、视图和存储过程几乎都应该使用可归零的字段或有一个默认值。因为这将允许存储过程省略列，这在运行新旧代码时都是一个要求。

### EDD 过程 <a href="#edd-process" id="edd-process"></a>

EDD 将每个数据库迁移分为三个阶段。_开始_、_过渡_和_结束_。

{% embed url="https://contributing.bitwarden.com/assets/images/stages_refactoring-4e6864b672648dcd79589749db600cd5.jpg" %}

{% embed url="https://www.martinfowler.com/articles/evodb.html#TransitionPhase" %}

这进行两次不同的数据库迁移。第一次迁移增加了新的内容，并与现有的代码向后兼容。第二次迁移删除了内容，并且与第一次迁移之前的相同代码不向后兼容。

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
{% tab title="第一次迁移" %}
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

{% tab title="数据迁移" %}
```sql
UPDATE [dbo].Customer SET
    FirstName=FName
WHERE FirstName IS NULL
```
{% endtab %}

{% tab title="第二次迁移" %}
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

## 工作流程 <a href="#workflow" id="workflow"></a>

下面将介绍 Bitwarden 编写迁移的具体工作流程。

### 开发者 <a href="#developer" id="developer"></a>

在[迁移](migrations.md)中描述了开发流程。

### 开发运维 <a href="#devops" id="devops"></a>

#### **On `rc` cut**

创建一个 PR 来移动未来的脚本。

* `DbScripts_future` 到 `DbScripts`，在脚本前面加上当前日期，但保留现有日期。
* `dbo_future` 到 `dbo`。

#### 服务器发布后 <a href="#after-server-release" id="after-server-release"></a>

1. 运行任何可能需要的数据迁移脚本。(这可能需要分批执行，直到所有的数据都被迁移完毕）
2. 在服务器运行一段时间后，执行未来的迁移脚本来清理数据库。

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
