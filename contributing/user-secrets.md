# 修改用户机密

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/user-secrets)
{% endhint %}

## 手动编辑用户机密 <a href="#manually-editing-user-secrets" id="manually-editing-user-secrets"></a>

我们建议使用[服务器设置指南](../getting-started/server/guide.md)中描述的自动帮助程序脚本。然而，您也可以使用以下的教学手动编辑您的用户机密或对其进行故障排除。如果您手动编辑正在使用的机密，如果需要，请务必记住将您的更改复制到所有项目。

### 编辑用户机密 - Windows Visual Studio <a href="#editing-user-secrets-visual-studio-on-windows" id="editing-user-secrets-visual-studio-on-windows"></a>

右键点击解决方案资源管理器中的项目，然后点击 **Manage User Secrets**。

### 编辑用户机密 - macOS Visual Studio <a href="#editing-user-secrets-visual-studio-on-macos" id="editing-user-secrets-visual-studio-on-macos"></a>

打开终端并导航到项目的目录。

添加用户机密：

```bash
dotnet user-secrets set "<key>" "<value>"
```

查看当前设置的机密：

```bash
dotnet user-secrets list
```

默认情况下，用户机密文件位于：

```
~/.microsoft/usersecrets/<project name>/secrets.json
```

您可以直接编辑此文件，这比使用命令行工具要简单得多。

### 编辑用户机密 - Rider <a href="#editing-user-secrets-rider" id="editing-user-secrets-rider"></a>

* 导航到 **Preferences** -> **Plugins** 然后安装 .NET Core User Secrets
* 右键点击项目，然后点击 **Tools** -> **Open project user secrets**
