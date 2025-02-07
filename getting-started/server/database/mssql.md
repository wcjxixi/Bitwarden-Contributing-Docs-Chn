# MSSQL

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/mssql/)
{% endhint %}

Bitwarden 主要将数据存储在 MSSQL (Microsoft SQL Server) 中。服务器中的数据访问层使用 [Dapper](https://github.com/DapperLib/Dapper) 编写，Dapper 是 .NET 的轻量级对象映射器。

## 创建数据库 <a href="#creating-the-database" id="creating-the-database"></a>

请参阅[服务器设置指南](../guide.md)。

## 更新数据库 <a href="#updating-the-database" id="updating-the-database"></a>

`dev/migrate.ps1` 辅助脚本使用我们的 [MsSql Migrator Utility](https://github.com/bitwarden/server/tree/main/util/MsSqlMigratorUtility) 来运行迁移。每次与 `main` 分支同步或创建新的迁移脚本时，都应运行此辅助脚本。已经运行的迁移会在数据库的 `Migration` 表中进行跟踪。

## 修改数据库 <a href="#modifying-the-database" id="modifying-the-database"></a>

修改数据库的过程描述在[迁移](../../../contributing/database-migrations/)中。
