# \*Mac App Store Dev

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/desktop/mac/)
{% endhint %}

{% hint style="danger" %}
只有在测试 Mac App Store (MAS) 独有的某些功能时，才需要使用 Mac App Store (MAS) Dev 构建。一般来说，除非有特殊原因需要使用 MAS 构建，否则应使用主构建说明（使用 `npm run electron`）。
{% endhint %}

## 设置 <a href="#setup" id="setup"></a>

这些步骤可能比较复杂。如果遇到任何困难，请在 `#team-eng` Slack 频道中发帖寻求帮助。

### Xcode

1. 安装[来自 App Store 的 Xcode](https://apps.apple.com/us/app/xcode/id497799835?mt=12)
2. 使用您的 AppleID（8bit solutions LLC 组织的成员）登录。这可以从 `Xcode > Preferences ... > Accounts` 完成
3. 点击 `Bitwarden Inc` 团队并点击 `Manage Certificates...` ，以确保您拥有分配给 Bitwarden Inc 的个人代码签名证书。
4. 如果未列出证书，请点击加号 ( `+` ) 创建一个。

### 钥匙串 <a href="#keychain" id="keychain"></a>

验证您的 Apple 钥匙串是否包含 `AC_PASSWORD` 的值，如果没有，我们需要生成一个。

1、使用您的 Apple 账户在 [AppleID 网站](https://appleid.apple.com/)登录

2、点击「应用程序专用密码」

<div align="left">

<figure><img src="https://github.com/bitwarden/contributing-docs/blob/master/docs/getting-started/clients/desktop/mac/app-specific-passwords.png?raw=true" alt=""><figcaption></figcaption></figure>

</div>

3、然后点击 `Passwords` 旁边的 `+` 图标以添加新的应用程序专用密码

{% embed url="https://contributing.bitwarden.com/assets/images/app-specific-passwords2-bad7e0cf20855adc23e15b91dbc27031.png" %}

4、使用已保存的新应用程序专用密码

```bash
security add-generic-password -a "<apple_id>" -w "<app_specific_password>" -s "AC_PASSWORD"
```

### 配置配置文件 <a href="#provisioning-profile" id="provisioning-profile"></a>

1. 请求 DevOps (@DevOps in slack) 将您的 `Apple Development` 签名证书添加到配置文件中，以及将您的 Mac `Provisioning UDID` 添加到白名单中。通过转到 `About This Mac > System Report...` 可以找到 `Provisioning UDID`，然后复制 `Provisioning UDID:` 行&#x20;
2. 添加完所有内容后，从 [https://developer.apple.com/account/resources/profiles/list](https://developer.apple.com/account/resources/profiles/list) 下载 `Bitwarden Desktop Development (2021)` 配置文件
3. 将配置文件安装到您的设备，并将其放置在 `clients/apps/desktop` 存储库根目录中。

## 测试 <a href="#testing" id="testing"></a>

1、运行以下命令来识别您的个人开发证书的名称：

```bash
security find-identity -v | grep 'Apple Development'
```

2、运行 `export CSC_NAME=""`，确保设置了 `CSC_NAME` 环境变量，其值应该是 `find-identity` 的输出，不带 `Apple Development:` 部分。

3、运行 `npm run dist:mac:masdev`。

{% hint style="info" %}
如果这是您第一次在本地运行桌面，请确保在运行 `npm run dist:mac:masdev` 之前先运行 `npm ci`。
{% endhint %}

## 故障排除 <a href="#troubleshoot" id="troubleshoot"></a>

如果收到错误消息 `You do not have permission to open the application "Bitwarden".`（您没有打开应用程序 "Bitwarden" 的权限），请确保将正确的配置文件放置在桌面存储库根目录中。
