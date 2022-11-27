# OpenLDAP Docker 服务器

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/enterprise/directory-connector/open-ldap)
{% endhint %}

此方法使用 [OpenLDAP Docker 镜像](https://github.com/osixia/docker-openldap)来运行可用于开发的本地目录服务。

## 要求 <a href="#requirements" id="requirements"></a>

* [Web Vault](../../clients/web-vault/)
* [服务器](../../server/)
* [目录连接器](./)
* 一个企业组织

## LDIF 文件 <a href="#ldif-file" id="ldif-file"></a>

LDIF 文件包含目录的配置（例如用户、群组等）。

### 下载示例 LDIF 文件 <a href="#download-example-ldif-file" id="download-example-ldif-file"></a>

对于大多数用例，您可以下载以下示例 LDIF 文件之一以帮助您快速开始和运行：

* [20 users](https://contributing.bitwarden.com/enterprise/directory-connector/directory-20.ldif)
* [50 users](https://contributing.bitwarden.com/enterprise/directory-connector/directory-50.ldif)
* [100 users](https://contributing.bitwarden.com/enterprise/directory-connector/directory-100.ldif)
* [250 users](https://contributing.bitwarden.com/enterprise/directory-connector/directory-250.ldif)
* [500 users](https://contributing.bitwarden.com/enterprise/directory-connector/directory-500.ldif)

### 生成您自己的 LDIF 文件 <a href="#generate-your-own-ldif-file" id="generate-your-own-ldif-file"></a>

或者，您可以使用以下说明生成您自己的 LDIF 文件。除非您有特殊要求，否则您不需要这样做。

1. 下载 [LDIF 生成器](https://ldapwiki.com/wiki/LDIF%20Generator)
2. 将 `Data/mail-hosts.txt` 文件替换为我们自己的 [mail-hosts.txt](https://contributing.bitwarden.com/enterprise/directory-connector/mail-hosts.txt) 文件。这包含大量唯一主机名，以避免生成重复的电子邮件地址。
3. 运行 `java -jar LDIFGen.jar`
4. 使用以下设置：
   * Base Added: dc=bitwarden, dc=com
   * Generate OUs: Generic
   * Generate People: add
5. 点击 Run
6. LDIF 输出可能在电子邮件地址中包含非法字符（例如空格和撇号） - 您应该在使用前手动检查。

## 启动 Open LDAP <a href="#start-open-ldap" id="start-open-ldap"></a>

1、在本地服务器存储库中打开终端

2、转到 `dev` 文件夹：

```bash
cd dev
```

3、将您的 LDIF 文件复制到此文件夹中，并将其命名为 `directory.ldif`：

```bash
cp path/to/file.ldif ./directory.ldif
```

4、启动 OpenLDAP Docker 容器

```bash
docker-compose --profile ldap up -d
```

如果您更改了 LDIF 文件，您可以通过使用 `--force-recreate` 标志再次运行此命令来强制 Docker 使用新的文件。

## 配置目录连接器 <a href="#configure-directory-connector" id="configure-directory-connector"></a>

1. 运行 Directory Connector Electron 应用程序（请参阅[构建说明](./#build-instructions)）
2. 使用[组织 API 密钥](https://help.ppgg.in/organizations/bitwarden-public-api#authentication)登录
3. 使用下面的配置设置

### 目录设置 <a href="#directory-settings" id="directory-settings"></a>

* **Type**: Active Directory / LDAP
* **Server Hostname**: localhost
* **Server Port**: 389
* **Root Path**: dc=bitwarden,dc=com
* **This server uses Active Directory:** \[unchecked]
* **This server pages search results:** \[unchecked]
* **This server uses an encrypted connection:** \[unchecked]
* **Username**: cn=admin,dc=bitwarden,dc=com
* **Password**: admin

### 同步设置 <a href="#sync-settings" id="sync-settings"></a>

* **User Path**: \[blank]
* **User Object Class**: person
* **User Email Attribute**: mail
* **Group Path**: \[blank]
* **Group Object Class**: organizationalUnit
* **Group Name Attribute**: ou

## 同步 <a href="#sync" id="sync"></a>

{% hint style="warning" %}
当您进行真正的同步时，邀请电子邮件将发送给所有已同步的用户。确保您使用的是 [Mailcatcher](../../server/guide.md#mailcatcher)，这样您就不会发送实时电子邮件。
{% endhint %}

1. 点击目录连接器中的「Test Now」按钮。你应该会得到一个用户列表
2. 准备好后，点击「Sync Now」以执行真正的同步。您应该会在 Directory Connector 中收到确认消息，并在 Web 密码库中看到新的被邀请的用户
