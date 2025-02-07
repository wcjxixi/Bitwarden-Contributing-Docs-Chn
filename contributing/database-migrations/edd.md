# 进化数据库设计

{% hint style="info" %}
开始对应的[官方页面地址](https://contributing.bitwarden.com/contributing/database-migrations/edd)
{% endhint %}

在 Bitwarden，我们遵循[进化数据库设计 (EDD)](https://en.wikipedia.org/wiki/Evolutionary_database_design)。EDD 描述了一个过程，在这个过程中，数据库架构被持续更新，同时通过使用数据库过渡阶段仍然确保与旧版本的兼容性。

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

值得注意的是，_重构阶&#x6BB5;_&#x901A;常是滚动的，一个重构&#x7684;_&#x7ED3;束阶&#x6BB5;_&#x662F;另一个重构&#x7684;_&#x8FC7;渡阶段_。下表详细说明了在哪个数据库阶段需要支持哪些应用程序版本。

| 数据库阶段 | Release X | Release X+1 | Release X+2 |
| ----- | --------- | ----------- | ----------- |
| 开始    | ✅         | ❌           | ❌           |
| 过渡    | ✅         | ✅           | ❌           |
| 结束    | ❌         | ✅           | ✅           |

### 迁移 <a href="#migrations" id="migrations"></a>

上图中描述的三种不同的迁移是初始迁移、过渡迁移和最终迁移。

#### 初始 (**Initial**) 迁移 <a href="#initial-migration" id="initial-migration"></a>

初始迁移在代码部署之前运行，其目的是在不中断对版本 X 的支持的情况下添加对版本 X+1 的支持。迁移应快速执行且不包含任何昂贵的操作，以确保零停机时间。

#### 过渡 (**Transition**) 迁移 <a href="#transition-migration" id="transition-migration"></a>

过渡迁移在过渡阶段的某个时候运行，并提供可选的数据迁移，以防数据迁移太慢或对数据库造成太大负载，或者使其不适合初始迁移。

* 与版本 X 和版本 X+1 应用程序兼容。
* 如果需要，此时只能运行数据群体迁移
  * 必须在过渡阶段作为后台任务运行。
  * 操作被批处理或以其他方式优化，以确保数据库保持响应。
* 在此阶段不会运行架构更改。

#### 最终 (**Finalization**) 迁移 <a href="#finalization-migration" id="finalization-migration"></a>

最终迁移删除了保留与版本 X 的向后兼容性所需的临时测量，并且数据库架构从此仅支持版本 X+1。这些迁移作为版本 X+2 部署的一部分运行。

### 示例 <a href="#example" id="example"></a>

让我们看一个例子，重命名列的重构如下图所示。

{% embed url="https://contributing.bitwarden.com/assets/images/rename-column-1f4999a32d438f4a75649089e85b4c18.gif" %}

在这个重构中，我们将 `Customer` 表中的 `Fname` 列重命名为 `FirstName`。这可以很容易地使用常规的 `Alter Table` 语句来实现，但这将破坏与现有运行代码的兼容性。相反，让我们来看看如何逐步重构这个表。

我们将首先创建一个迁移，将 `FirstName` 列添加到 `Customer` 表中。同时，我们还将更新存储过程，以同步 `FName` 和 `FirstName` 之间的内容，以确保新旧服务器版本可以同时运行。同步代码在下面的代码片断中突出显示。

之后将部署新的服务器版本，一切检查完毕后，现有数据将使&#x7528;_&#x6570;据迁&#x79FB;_&#x811A;本进行迁移。这实际上是将 `FName` 复制到 `FirstName` 列。

最后，将运&#x884C;_&#x7B2C;二次迁移_，删除旧列并更新存储过程以删除同步逻辑。

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

{% tab title="最终迁移" %}
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

该流程的实施有一些重要的约束条件：

* Bitwarden 生产环境需要始终处于在线状态
* 自托管实例必须支持相同的数据库更改流程；但是，它们没有相同的始终在线应用程序限制
* 最大限度地减少流程中的手动步骤

支持所有这些约束条件的过程是一个复杂的流程。下面是状态机的图像，希望有助于可视化该过程及其支持的内容。它假设所有数据库更改都遵循[迁移](./)中规定的标准。

***

{% embed url="https://contributing.bitwarden.com/assets/images/edd_state_machine-76cf9f0a9e72188bf7f8fb71f5d53c19.jpg" %}

***

### 在线环境 <a href="#online-environments" id="online-environments"></a>

架构迁移和数据迁移只是迁移。底层的实现问题是协调迁移的运行时约束。最终，所有迁移都将以 `DbScripts` 结束。但是，为了协调 _Transition_ 和相关 _Finalization_ 迁移的运行，它们将保留在 `DbScripts` 之外，直到正确的时间。

在具有始终在线应用程序的环境中，必须在推出新代码后运行 _Transition_ 脚本。要执行完整部署，将运行 `DbScripts` 中的所有新迁移，推出新代码，然后在所有新代码服务上线后立即运行 `DbScripts_transition` 目录中的所有转换迁移。如果新代码推出后出现严重故障，将进行回滚（请参阅下面的回滚）。_Finalization_ 迁移将在下一次部署开始时才会运行，并将其移至 `DbScripts` 中。

在此部署之后，为了准备下一个版本，`DbScripts_transition` 中的所有迁移都会移至 `DbScripts` ，然后 `DbScripts_finalization` 中的所有迁移都会移至 `DbScripts`，保留其执行顺序以进行全新安装。对于当前的分支策略，当 `rc` 被削减以准备此版本时，PR 将针对 `master` 开放。此 PR 自动化还将处理重命名迁移文件并将 `[dbo_finalization]` 的任何引用更新为 `[dbo]`。

下一次部署将选取 `DbScripts` 中新添加的迁移，并将之前可重复的 _Transition_ 迁移设置为不再可重复，执行 _Finalization_ 迁移，然后执行与以下代码更改相关的任何新迁移出去。

任何时候不同目录中的迁移状态都会保存在迁移器实用程序中并进行版本控制，该实用程序支持两种类型环境中的分阶段迁移过程。

### 离线环境 <a href="#offline-environments" id="offline-environments"></a>

离线环境的过程与始终在线的过程类似。但是，由于它们没有始终在线的约束，因此 _Initial_ 迁移和 _Transition_ 迁移将相继运行：

* 像今天一样停止 Bitwarden 堆栈
* 启动数据库
* 运行 `DbScripts` 中的所有新迁移（包括上次部署的 _Finalization_ 迁移和当前部署的任何 _Initial_ 迁移）
* 运行所有 _Transition_ 迁移
* 重新启动 Bitwarden 堆栈

## 回滚 <a href="#rollbacks" id="rollbacks"></a>

如果服务器发布失败，需要回滚，这应该很简单，只要重新部署以前的版本就可以了。数据库将**停留**在过渡阶段，直到发布修补程序并且可以更新服务器。修补程序准备好发布后，就会对其进行部署，并重新运行 _Transition_ 迁移以验证数据库是否处于所需的状态。

如果需要完全拉取某个功能，则需要编写新的迁移来撤消数据库更改，并且还需要更新未来的迁移以适应数据库更改。通常不建议这样做，因为需要重新访问挂起的迁移（对于其他版本）。

## 测试 <a href="#testing" id="testing"></a>

在合并 PR 之前，请确保数据库更改在当前发布的版本上运行良好。我们目前没有为此提供自动化测试套件，开发人员需要确保他们的数据库更改针对当前发布的版本正确运行。

## 延伸阅读 <a href="#further-reading" id="further-reading"></a>

1. [进化数据库设计](https://martinfowler.com/articles/evodb.html)（特别是[所有数据库更改都是数据库重构](https://martinfowler.com/articles/evodb.html#AllDatabaseChangesAreMigrations)）
2. [敏捷数据 (AD) 方法](http://agiledata.org/)（特别是[数据库目录重构](http://agiledata.org/essays/databaseRefactoringCatalog.html)）
3. [重构数据库：进化数据库](https://databaserefactoring.com/)
4. 重构数据库：进化数据库设计（Addison-Wesley 签名系列 (Fowler)）ISBN-10: 0321774515
