# iOS

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/mobile/ios/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* dotnet Core 2.0（最新版）
* dotnet Core 3.1（最新版）
* Xamarin（iOS 版）
* 安装了 Xcode 的 Mac

## Apple 开发者账户设置 <a href="#apple-developer-account-setup" id="apple-developer-account-setup"></a>

1. 接受邀请加入 Bitwarden Apple Developer 团队。您的电子邮件中应该会收到一个请求，主题是「您被邀请加入开发团队」。点击链接「接受邀请」，系统会提示您为自己的 Bitwarden 电子邮件地址创建一个 Apple ID。如果您没有收到这封邮件，请联系 IT 部门 (@IT in slack)。接受条款和条件并完成注册流程。
2. 访问 [Apple ID Online](https://appleid.apple.com/)，使用新的 Apple ID 登录。设置因素身份验证（使用手机和/或受信任的设备）-- 这一点至关重要，因为苹果不再允许没有 MFA 的「开发者」账户，但在本地构建失败时它不会告诉你这一点。
3. 访问 [App Store Connect](https://appstoreconnect.apple.com/) 并接受条款和条件。
4. 确保您有权访问 Bitwarden 团队和团队应用程序配置文件。
5. 访问 [Apple Developer Account](https://developer.apple.com/account/)，然后转到「证书、ID 和配置文件」菜单项。检查是否能在「证书」部分看到 8bit Solutions LLC 证书，以及在「配置文件」部分看到 Bitwarden 配置文件。如果缺少其中任何一项，请向 IT 部门 (@IT #tech-support in slack) 询问附加角色/权限。

## macOS 设置 <a href="#macos-setup" id="macos-setup"></a>

接下来，您需要为构建和运行 Bitwarden iOS 移动项目设置 Mac 环境。这需要创建必要的开发人员配置文件，以便在 Mac 上通过 Xcode 进行代码签名和执行。Visual Studio 有一个获取所有供应配置文件的简单过程，但在没有太多反馈的情况下很容易失败。请先尝试 Visual Studio 的说明（「简单的方式」），如果需要，再使用 Xcode 的说明（「复杂的方式」）。

### Visual Studio：简单的方式 <a href="#visual-studio-the-easy-way" id="visual-studio-the-easy-way"></a>

1、打开 Visual Studio for Mac

2、转到「首选项」->「发布」->「Apple 开发者账户」

3、点击「添加」，选择「企业账户」，然后使用之前配置的 Apple 开发者账户登录

{% hint style="info" %}
如果您收到「Failed to synchronize with Apple Developer Portal」（无法与 Apple Developer Portal 同步）错误，则表示您缺少附加角色/权限。
{% endhint %}

成功登录后，您应该在列表中看到您的账户，并在账户团队列表中看到「Bitwarden Inc」

4、点击「查看详细信息...」

5、如果您没有有效的 Apple 开发证书，请点击「创建证书」->「Apple 开发」

6、点击「下载所有配置文件」

7、您现在应该可以通过设置左上角的 `iOS > Debug | iPhone Simulator > [pick any iOS Simulator]` 然后按「播放」来运行应用程序了

<figure><img src="https://contributing.bitwarden.com/assets/images/run-debug-81ba8d56bdba21767e27f03a980585ee.png" alt=""><figcaption></figcaption></figure>

如果可以运行，您可以跳到下一部分。

如果只有「Generic Simulator」（通用模拟器）选项，并提示降低「Deployment Target」（部署目标），则您的 Xamarin 版本可能尚不支持您正在使用的 Xcode 版本（如[此处](https://github.com/xamarin/xamarin-macios/issues/15954#issuecomment-1246025735)所讨论）。

要解决这个问题，请尝试从 Apple [下载](https://developer.apple.com/download/all/)并安装旧版本的 Xcode（您可以从 Xamarin.iOS [发行说明](https://github.com/xamarin/xamarin-macios/releases)中查找有关使用哪个 Xcode 版本的指导）。安装新版本的 Xcode 后，重启 Visual Studio 并加载项目以验证可用的模拟器选项。

{% hint style="info" %}
如果您需要在您的开发机器上安装多个版本的 Xcode，您可以将从下载中提取的 `Xcode.app` 文件重命名为其他名称（例如「Xcode\_14\_2.app」），然后将其放入您的应用程序文件夹中。然后，您可以在命令行中使用 `xcode-select` 以在 Xcode 版本之间进行切换：

```bash
sudo xcode-select -s /Applications/Xcode_14_2.app
```

您可以使用 Xcodes.app 等工具获得类似的结果
{% endhint %}

### Xcode：复杂的方式 <a href="#xcode-the-hard-way" id="xcode-the-hard-way"></a>

{% hint style="info" %}
如果您是下一个按照这些说明操作的人，请提交并上传您创建的 Xcode 项目文件，以便我们简化这一流程。
{% endhint %}

仅当上述 Visual Studio 说明不适合您时才尝试这些说明。

1、打开 Xcode

2、接受所有默认设置，确保已安装所有扩展/附加组件等

3、「创建新的项目...」->「iOS」->「App」

4、对您的新项目使用以下选项：

* 产品名称：bitwarden
* 团队：Bitwarden Inc（如果缺少，请仔细检查上面您的 Apple 开发者账户设置）
* 组织标识符：com.8bit
* 绑定标识符（自动生成）：com.8bit.bitwarden
* 语言：Objective-C
* 用户界面：Storyboard
* 保留所有其他复选框未选中（或取消选中它们）

<div align="left">

<figure><img src="https://contributing.bitwarden.com/assets/images/new-project-options-03e83d1de2942e190f3d992d846409df.png" alt=""><figcaption></figcaption></figure>

</div>

5、点击「下一步」，保存到默认位置，然后点击「创建」

6、在项目配置页面上，点击「签名和功能」选项卡

7、确保您具有以下默认值：

* 自动管理签名：（已选中）
* 团队：Bitwarden Inc
* 配置配置文件：Xcode 托管配置文件
* 签名证书：您的 Apple ID/Name

{% embed url="https://contributing.bitwarden.com/assets/images/signing-and-capabilities-241bc2966623419ab15ef49a36c2a153.png" %}

8、从菜单栏中，点击「产品」->「构建」

9、重复步骤 3-8，并在步骤 4 中进行以下更改：

* 产品名称：find-login-action-extension
* 组织标识符：com.8bit.bitwarden
* 捆绑定标识符（自动生成）：com.8bit.bitwarden.find-login-action-extension

10、重复步骤 3-8，并在步骤 4 中进行以下更改：

* 产品名称：autofill
* 组织标识符：com.8bit.bitwarden
* 捆绑定标识符（自动生成）：com.8bit.bitwarden.autofill

11、重复步骤 3-8，并在步骤 4 中进行以下更改：

* 产品名称：share-extension
* 组织标识符：com.8bit.bitwarden
* 绑定标识符（自动生成）：com.8bit.bitwarden.share-extension

12、如果您有想要用于测试的物理设备（例如 iPhone 或 iPad），您还需要对刚刚创建的每个 Xcode 项目执行以下操作：

* 用电缆连接设备
* 在 Xcode 中选择您的设备作为构建目标
* 从菜单栏中，点击「产品」>「构建」
* 如果询问，请同意注册您的设备

{% hint style="info" %}
有时这些配置文件可能会比较混乱。如果您在物理设备（或模拟器）上运行时遇到问题，请尝试运行 `rm -r ~/Library/MobileDevice/Provisioning\ Profiles` 来清除它们。再次构建每一个 Xcode 项目以重新生成它们。
{% endhint %}

## Visual Studio <a href="#visual-studio" id="visual-studio"></a>

接下来，我们需要配置您的 Visual Studio 开发环境。

{% tabs %}
{% tab title="Windows" %}
1. 连接到您刚刚完成上述步骤的 Mac
2. 打开 Visual Studio 然后点击「工具」->「iOS」->「与 Mac 配对」
3. 扫描并选择您的机器。如果看不到，请点击「添加 Mac... 」按钮并输入 Mac 名称或 IP 地址。如果不知道 Mac 名称（或 Mac 上有 Windows 虚拟机），请进入 Mac，打开「系统偏好设置」>「共享」，查找机器的「.local」地址。
4. 出现提示时提供您的 MacOS 用户名和密码
5. 配对后，关闭「配对 Mac」窗口
6. 将活动的构建配置文件更改为「调试」>「iPhoneSimulator」>「iOS」
7. 从解决方案资源管理器重新构建 iOS 项目
8. 您现在可以使用 iOS 模拟器进行调试了
{% endtab %}

{% tab title="macOS" %}
1. 检查是否安装了命令行工具：
   * 打开 Xcode
   * 从菜单栏中，点击「Xcode」->「首选项」->「位置」
   * 确保在「命令行工具」下选择了 Xcode 版本
2. 打开 Visual Studio for Mac
3. 打开本地移动存储库根目录中的移动解决方案文件 (`bitwarden-mobile.sln`)
4. 在顶部栏中，您应该能够选择「iOS」->「iPhoneSimulator」->选择您的型号然后点击「运行」（如果您设置了物理设备，则选择您的物理设备）
{% endtab %}
{% endtabs %}

## 调试 <a href="#debugging" id="debugging"></a>

### iPhone 模拟器 <a href="#iphone-simulator" id="iphone-simulator"></a>

iPhone 模拟器可以访问 localhost，您可以像往常一样将客户端指向本地开发服务器。不过，应用程序默认需要使用 https。要允许使用 http 进行测试，请按以下步骤操作。

1、在 Visual Studio Code 或其他编辑器中打开 `src/iOS/Info.plist` ，以便可以编辑原始 XML。（不要使用 Visual Studio 中的「属性列表编辑器」）

2、在顶层 `<dict>` 元素中添加以下代码：

```xml
<key>NSAppTransportSecurity</key>
<dict>
    <key>NSAllowsArbitraryLoads</key>
    <false/>
    <key>NSExceptionDomains</key>
    <dict>
        <key>localhost</key>
        <dict>
            <key>NSExceptionAllowsInsecureHTTPLoads</key>
            <true/>
            <key>NSIncludesSubdomains</key>
            <true/>
        </dict>
    </dict>
</dict>
```

3、保存并退出 `Info.plist`

4、在启动之前按 `Command` + `B` 强制运行新的构建

5、不要推送这些更改 :)

### iPhone 设备 <a href="#iphone-device" id="iphone-device"></a>

该设备无法直接访问 Mac 的本地主机，因此您可以遵循[本指南](https://ymoondhra.medium.com/how-to-run-localhost-on-your-iphone-4110a54d1896)来连接它们。

完成此操作后，您还必须修改 `Info.plist` 以允许 http 用于测试，如之前在模拟器测试中所解释的那样。

您也很可能需要更改服务器上每个项目的 `Properties` 上的 `launchSettings.json`。在那里，您需要更改 `iisSettings -> iisExpress` 和 `profiles -> Identify` 的 `applicationUrl` ，以便它显示 `name.local` 而不是 `localhost`，其中 `name` 是您在 Mac 共享配置中设置的计算机名称。

在实际测试应用程序之前，请打开浏览器并尝试通过访问 `http://name.local:4000/alive` 以连接到 `Api` 。如果这不起作用，请查看指南或服务器配置中的步骤。确保您的 `User secrets` 也是最新的。

最后，您必须在手机上配置 `Api` 和 `Identity` URL 才能使用 `http://name.local:4000` 和 `http://name.local:33656` ，其中 `name` 是您在 Mac 共享配置中设置的计算机名称。

### iOS 扩展 <a href="#ios-extensions" id="ios-extensions"></a>

1. 将 iOS 扩展项目设置为启动项目
2. 您将收到一个弹出窗口，显示「Waiting for the debugger to connect...」（正在等待调试器连接...）
3. 不要打开 Bitwarden 应用程序（否则调试器将连接到它而不是连接到扩展）。相反，触发扩展
4. 您的扩展断点现在应该被命中

例如：如果您想调试 **iOS.Autofill** 扩展，您需要完成步骤 1-3，然后转到您的 iOS 设备，打开浏览器，登录，点击钥匙图标并从底部弹出窗口打开 Bitwarden。

### 使用服务器隧道​ <a href="#using-server-tunneling" id="using-server-tunneling"></a>

您应该使用[连接到本地服务器的代理隧道](../../server/tunnel.md)，让您的应用程序直接与之连接，而不是将设备或模拟器配置为忽略 SSL 证书。

### 推送通知（实时同步和无密码）​ <a href="#push-notifications-live-sync--passwordless" id="push-notifications-live-sync--passwordless"></a>

推送通知当前不可用于调试部署。它们仅在 TestFlight 和生产版本上受支持。
