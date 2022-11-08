# 生物识别解锁

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/clients/browser/biometric/)
{% endhint %}

目前，移动端、桌面端和浏览器扩展支持生物识别解锁。Bitwarden 的生物识别解锁功能与本机消息传递 API 集成以利用此功能。

## 哪些设备支持生物识别解锁？ <a href="#which-devices-support-biometric-unlock" id="which-devices-support-biometric-unlock"></a>

有关最新信息，请参阅[帮助文章](https://help.ppgg.in/your-vault/unlocking-with-biometrics)，但在撰写本文时，以下是概要：

_支持：_

* 基于 Chromium 的浏览器
* Firefox 版本 87 及更高版本
* Safari 14 及更高版本
* 从 [download](https://bitwarden.com/download) 获取的侧面加载的 Windows 桌面应用程序

_不支持：_

* Firefox 版本 86 及更早版本
* 侧面加载的 MacOS 桌面应用程序
* Linux OS

## 一般设置步骤 <a href="#general-setup-steps" id="general-setup-steps"></a>

{% hint style="danger" %}
如果您之前为 Safari 安装了本地构建的浏览器扩展，请按照[此处](./)所述重置扩展引用路径。
{% endhint %}

本机消息传递的工作方式是让浏览器启动一个轻量级的代理，并将其植入我们的桌面应用程序。

开箱即用，桌面应用程序只能与生产浏览器扩展通信。当您在桌面应用程序中启用浏览器集成时，应用程序会生成包含浏览器扩展的生产 ID 的清单。要启用桌面应用程序与开发版本浏览器扩展之间的通信，我们需要将您的浏览器扩展开发 ID 添加到此清单中。

### 构建并运行浏览器扩展 <a href="#build-and-run-the-browser-extension" id="build-and-run-the-browser-extension"></a>

* 在本地浏览器项目中，运行 `npm install`。
* 要在 Safari 上使用本地浏览器扩展，请使用以下命令：`npm run dist:safari:dmg`。构建完成后，您应该会在 Safari 的「偏好设置」中的「扩展」菜单下看到 Bitwarden 扩展。如果没有，打开并构建相关的 Xcode 项目（通常位于 `$HOME/browser/dist/Safari/dmg/desktop.xcodeproj`）。然后它应该出现在「偏好设置」菜单中，您就可以启用它了。
* 对于其他浏览器，使用 `npm run build:watch` 然后使用[此处](./#testing-and-debugging)描述的方法加载本地构建的扩展。

### 为本机消息添加扩展 ID <a href="#add-the-extension-id-for-native-messaging" id="add-the-extension-id-for-native-messaging"></a>

运行扩展后，确认扩展 ID 已添加到您选择的浏览器的 `NativeMessagingHost` JSON 文件中。

* 在 `chrome://extensions` 或 `about:debugging` 中找到扩展 ID。

{% embed url="https://contributing.bitwarden.com/clients/browser/extension-id.png" %}

* 使用您的 IDE 将 ID 添加到 `NativeMessageHost` JSON。此文件嵌套在应用程序支持目录中。例如，对于 Chrome 浏览器，该文件位于：

{% tabs %}
{% tab title="Windows" %}
```
%APPDATA%\Bitwarden\browsers\chrome.json
```
{% endtab %}

{% tab title="MacOS" %}
```
~/Library/Application Support/Google/Chrome/NativeMessagingHosts/com.8bit.bitwarden.jso
```
{% endtab %}

{% tab title="Linux" %}
```
~/.config/google-chrome/NativeMessagingHosts/com.8bit.bitwarden.json
```
{% endtab %}
{% endtabs %}

### 构建并运行桌面应用程序 <a href="#build-and-run-the-desktop-app" id="build-and-run-the-desktop-app"></a>

按照[桌面端](../desktop/)设置文档进行操作。

{% hint style="danger" %}
在 macOS 上，您需要构建一个 Mac App Store Development Build，或者关闭 Gatekeeper。
{% endhint %}

我的项目已经在运行了，现在呢？

* 在桌面应用程序的首选项菜单中，开启 `Enable browser integration`。
* 在浏览器扩展中，访问设置菜单，然后启用 `Unlock with biometrics` 选项。
* 浏览器会要求您允许该操作，但随后会锁定扩展。当您解锁密码库并再次启用生物识别解锁时，系统会要求您在桌面应用程序中确认此选择，并开启本地生物识别解锁功能。
