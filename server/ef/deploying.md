# 部署实体框架

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/server/ef/deploying/)
{% endhint %}

{% hint style="danger" %}
实体框架是为 Bitwarden 提供数据库服务的一种基本未经测试的方式。如果您决定使用它，请要预料到不一致的地方和错误。如果您遇到任何问题，请在 [https://github.com/bitwarden/server/issues](https://github.com/bitwarden/server/issues) 创建一个话题，并提供尽可能多的复制信息。

我们还没有一个自动运行数据库迁移的方法。您需要在_每次更新您的 Bitwarden 实例时_自己做。最好不要多次运行给定的迁移。同样，这不是自动的，因此需要您自己跟踪。

我们不会尝试将数据从现有的 MSSQL 数据库迁移到一个新的数据库提供程序。您可以在任何时候通过将 `globalSettings__databaseProvider="postgres"` 更改为 `globalSettings__databaseProvider="sqlServer"` 以切换回 MSSQL。

**不要在有您在乎的数据的生产实例中这样做!**
{% endhint %}

{% hint style="info" %}
这些步骤仅在 Linux 上测试过。
{% endhint %}

## 教学 <a href="#instructions" id="instructions"></a>

1、像平常一样安装 Bitwarden 自托管实例：[https://bitwarden.com/help/install-on-premise-linux/](https://bitwarden.com/help/install-on-premise-linux/)

2、更新 `bwdata/docker/docker-compose.override.yml`

> 注意：您应该选择一个更合适的 PgAdmin4 密码

```json
#
# Useful references:
# https://docs.docker.com/compose/compose-file/
# https://docs.docker.com/compose/reference/overview/#use--f-to-specify-name-and-path-of-one-or-more-compose-files
# https://docs.docker.com/compose/reference/envvars/
#
#########################################################################
# WARNING: This file is generated. Do not make changes to this file.    #
# They will be overwritten on update. If you want to make additions to  #
# this file, you can create a `docker-compose.override.yml` file in the #
# same directory and it will be merged into this file at runtime. You   #
# can also manage various settings used in this file from the           #
# ./bwdata/config.yml file for your installation.                       #
#########################################################################

version: '3'

services:
  mssql:

  web:

  attachments:

  api:

  identity:

  sso:

  admin:

  icons:

  notifications:

  events:

  nginx:

  postgresql:
    image: postgres:13.2
    container_name: bitwarden-postgres
    restart: always
    stop_grace_period: 60s
    networks:
      - default
      - public
    ports:
      - '5432:5432'
    volumes:
      - ../postgres/data:/var/lib/postgresql/data
      - ../postgres/config:/etc/postgresql
      - ../postgres/log:/var/log/postgresql
    env_file:
      - postgresql.env
      - ../env/uid.env
      - ../env/postgresql.override.env

  pgadmin:
    image: dpage/pgadmin4
    environment:
      PGADMIN_DEFAULT_EMAIL: postgres@bitwarden.com
      PGADMIN_DEFAULT_PASSWORD: root
    ports:
      - "8889:80"
    networks:
      - default
      - public
```

3、创建 `bwdata/docker/postgresql.env`

```systemd
POSTGRES_DB=bw_vault
POSTGRES_USER=postgres
```

4、创建 `bwdata/env/postgresql.override.env`

> 注意：您应该选择一个更合适的密码

```systemd
POSTGRES_PASSWORD=example
```

5、通过添加以下内容以更新 `bwdata/env/global.override.env`（确保更新密码以与您在 `postgresql.override.env` 中设置的密码相匹配）：

```systemd
globalSettings__databaseProvider="postgres"
globalSettings__postgreSql__connectionString="Host=bitwarden-postgres;Port=5432;Username=postgres;Password=example;Database=bw_vault"
```

6、重新启动您的 Bitwarden 环境以应用这些配置更改：`bitwarden.sh restart`

7、运行数据库迁移。由于我们仅仅是做了设置，我们还需要运行所有迁移。

1. 克隆服务器存储库 `git clone https://github.com/bitwarden/server`
2. `cd` 进入相应的脚本目录。对于 Postgres，这是 `server/util/PostgresMigrations/Scripts`
3. 将每一个迁移复制到 postgres docker 容器并运行它，下面的脚本将为当前目录中的每一个迁移执行此操作。注意：这个脚本需要从当前目录运行，否则 `docker exec` 命令中的 `$f` 不会指向容器上的脚本文件。

```sql
for f in `ls -v ./*.psql`; do docker cp $f bitwarden-postgres:/var/lib/postgresql/; docker exec -u 1001 bitwarden-postgres psql bw_vault postgres -f /var/lib/postgresql/$f; done;
```

8、运行 `bitwarden.sh restart` 以确保一切都是最新的

您的自托管实例现在应该可以使用 Postgres 了！
