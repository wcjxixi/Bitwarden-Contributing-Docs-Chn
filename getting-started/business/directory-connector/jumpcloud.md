# JumpCloud

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/enterprise/directory-connector/jumpcloud)
{% endhint %}

[JumpCloud](https://jumpcloud.com/) 提供了带有免费层级的 LDAP 即服务，可用于测试。

{% hint style="info" %}
JumpCloud 免费层仅限于 10 个用户，而且您不会得到 [OpenLDAP](open-ldap.md) 设置中的那种漂亮的预生成数据。
{% endhint %}

## 设置 JumpCloud <a href="#setup-jumpcloud" id="setup-jumpcloud"></a>

1. 创建一个 JumpCloud 账户并登录。
2. 创建一个用户并将该用户绑定到一个目录。应该有一个可以用于此的默认目录，称为 JumpCloud LDAP。有关说明，请参阅 [JumpCloud 帮助文档](https://support.jumpcloud.com/support/s/article/using-jumpclouds-ldap-as-a-service1#createuser)。
3. 创建一个管理员用户并将该用户绑定到同一目录。您将使用此用户验证 JumpCloud 目录连接器。

## 配置目录连接器 <a href="#configure-directory-connector" id="configure-directory-connector"></a>

1. 运行 Directory Connector Electron 应用程序（请参阅[构建说明](./#build-instructions)）。
2. 使[用组织 API 密钥](https://help.ppgg.in/organizations/bitwarden-public-api#authentication)登录。
3. 使用下面的配置设置。

### 目录设置 <a href="#directory-settings" id="directory-settings"></a>

对于这些设置，需要您的 JumpCloud 组织 ID。您可以在 JumpCloud Admin Console → User Authentication → LDAP → \[your LDAP server] 中找到它。

* **Type**: Active Directory / LDAP
* **Server Hostname**: ldap.jumpcloud.com
* **Server Port**: 636
* **Root Path**: o=\[Your JumpCloud Organization ID],dc=jumpcloud,dc=com
* **This server uses Active Directory:** \[unchecked]
* **This server pages search results:** \[unchecked]
* **This server uses an encrypted connection:** \[checked]
  * **Use SSL** \[checked]
  * **Do not verify server certificates** \[checked]
* **Username**: uid=\[Admin User],ou=Users,o=\[Your JumpCloud organization ID],dc=JumpCloud,dc=com
* **Password**: \[Admin User's password]

### 同步设置 <a href="#sync-settings" id="sync-settings"></a>

* **Sync Users**: \[checked]
* **User Path**: ou=Users,o=\[Your JumpCloud Organization ID]
* **User Object Class**: inetOrgPerson
* **User Email Attribute**: mail
* **Sync Groups**: \[checked]
* **Group Path**: o=\[Your JumpCloud Organization ID]
* **Group Object Class**: groupOfNames
* **Group Name Attribute**: memberOf

## 同步 <a href="#sync" id="sync"></a>

{% hint style="warning" %}
当您进行真正的同步时，邀请电子邮件将发送给所有已同步的用户。确保您使用的是 [Mailcatcher](../../server/guide.md#mailcatcher)，这样您就不会发送实时电子邮件。
{% endhint %}

1. 点击目录连接器中的「Test Now」按钮。你应该会得到一个用户列表。
2. 准备好后，点击「Sync Now」以执行真正的同步。您应该会在 Directory Connector 中收到确认消息，并在 Web 密码库中看到新的被邀请的用户。
