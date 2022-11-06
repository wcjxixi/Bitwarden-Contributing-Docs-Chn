# MSSQL 数据库

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/server/mssql/)
{% endhint %}

Bitwarden 主要将数据存储在 MSSQL (Microsoft SQL Server) 中。服务器中的数据访问层使用 [Dapper](https://github.com/DapperLib/Dapper) 编写，Dapper 是 .NET 的轻量级对象映射器。

## 创建数据库 <a href="#creating-the-database" id="creating-the-database"></a>

请参阅[服务器设置指南](../guide.md)。

## 更新数据库 <a href="#updating-the-database" id="updating-the-database"></a>

每当您与 `master` 分支同步或创建新的迁移脚本时，您都应该运行 `migrate.ps1` 帮助程序脚本。`migrate.ps1` 在 `migrations_$DATABASENAME`（通常是 `migrations_vault_dev`）中跟踪执行迁移。

## 修改数据库 <a href="#modifying-the-database" id="modifying-the-database"></a>

修改数据库的过程描述在[迁移](migrations.md)中。

## 故障排除 <a href="#troubleshooting" id="troubleshooting"></a>

### 新数据库，但跳过了迁移 <a href="#new-database-but-skipping-migrations" id="new-database-but-skipping-migrations"></a>

迁移记录按密码库数据库名称存储，名称通常为 `vault_dev` 或 `vault_dev_self_host`。如果您为了要重新开始而删除了这些名称，则应删除相应的 `migrations_$DATABASENAME` 数据库。
