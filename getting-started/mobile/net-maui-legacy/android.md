# Android

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/android/)
{% endhint %}

{% hint style="warning" %}
**Legacy**

在 .NET MAUI 中完成的**旧版** Android App 入门。
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您应该已经安装了推荐的[工具和库](../../tools.md)。您还需要安装：

1. Visual Studio 2022 / VS Code
2. [.NET 8（最新版本）](https://dotnet.microsoft.com/zh-cn/download/dotnet/8.0)
   * 注意：即使您使用的是基于 ARM64 的 Mac（M1、M2、M3 等），也可以安装所有 x64 SDK 来运行 Android
   * 在 Mac 版 Visual Studio 中，您可能需要通过 Visual Studio > Preferences > Preview Features > Use the .NET 8 SDK 来打开 .NET 8 功能
3. .NET MAUI Workload
   * 您可以通过运行 `dotnet workload install maui` 来安装它
4. Android SDK 34
   * 您可以使用 [Visual Studio](https://learn.microsoft.com/zh-cn/previous-versions/xamarin/android/get-started/installation/android-sdk) 或 [Android Studio](https://developer.android.com/tools/releases/platforms?hl=zh-cn) 中的 SDK 管理器来安装它

要确保您是否已安装 Android SDK 和模拟器：

1. 打开 Visual Studio
2. 点击 Tools > SDK Manager（在 Android 子标题下）
3. 点击 Tools 选项卡
4. 确保已安装以下项目：
   * Android SDK 工具（至少一个版本的命令行工具）
   * Android SDK Platform-Tools
   * Android SDK 构建工具（至少一个版本）
   * Android 模拟器
5. 如果您已标记所有要安装的内容，请单击 Apply Changes

如果您遗漏了任何内容，Visual Studio 应该都会提示您。

## 安卓开发设置 <a href="#android-development-setup" id="android-development-setup"></a>

要设置新的虚拟 Android 设备用于调试：

1. 点击 Tools > Device Manager（在 Android 子标题下）
2. 点击 New Device
3. 设置您要模拟的设备 - 如果您不确定，您可以选择 Base Device 并保留默认设置
4. 然后，Visual Studio 将下载该设备的镜像。下载进度显示在 Android 设备管理器对话框的进度中。
5. 完成后，模拟的 Android 设备将在 App > Debug > {设备名称} 下作为构建目标使用

### ARM64 Mac <a href="#arm64-macs" id="arm64-macs"></a>

1. 安装并打开 Android Studio
2. 在顶部导航栏中，点击 Android Studio > Settings > Appearance & Behavior (tab) > System Settings > Android SDK
3. 在 SDK 平台选项卡中，确保选中 Show Package Details 复选框（位于右下角）
4. 在每一个 Android API 的下方，您会看到一系列系统镜像，选其中的 `ARM 64 v8a` 并等待它下载
5. 转到 View > Tool Windows > Device Manager
6. 在 Device Manager 中，使用之前下载的系统镜像创建一个设备

{% embed url="https://contributing.bitwarden.com/assets/images/android-sdk-529574bf6a67ae6500b23196a6843ec5.png" %}

## F-Droid[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/android/#f-droid) <a href="#f-droid" id="f-droid"></a>

在 `App.csproj` 和 `Core.csproj` 中，我们现在可以在构建/发布时传递 `/p:CustomConstants=FDROID`，以便将 `FDROID` 常量添加到项目级别的已定义常量中，我们可以使用它与预编译指令一起，例如：

```c
#if FDROID
    // perform operation only for FDROID.
#endif
```

## 构建[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/android/#building) <a href="#building" id="building"></a>

目前在 Mac 版 Visual Studio 中构建项目时存在一些问题，因此如果您遇到一些错误，请使用 CLI 构建（之前移除的 `bin/obj` 文件夹）：

```vbnet
dotnet build -f net8.0-android -c Debug
```

## 测试与调试 <a href="#testing-and-debugging" id="testing-and-debugging"></a>

### 使用 Android 模拟器 <a href="#using-the-android-emulator" id="using-the-android-emulator"></a>

在 Mac 上使用 Visual Studio 进行原生调试时，要访问 Android 模拟器中的 `localhost:<port>` 资源，您需要使用 `<http://10.0.2.2:<port>` 配置端点地址，以便访问 `localhost`，`localhost` 被设计为映射 Android 代理。

### 使用服务器隧道 <a href="#using-server-tunneling" id="using-server-tunneling"></a>

您可以使用一个[到本地服务器的代理隧道](../../server/tunnel.md)并让您的 App 直接连接到它，而不是配置设备或模拟器。

### 推送通知 <a href="#push-notifications" id="push-notifications"></a>

Android App 的默认配置是将自身注册到与 Bitwarden 的 QA Cloud 相同的环境中。这意味着，如果您尝试使用生产端点调试 App，您将无法接收实时同步更新或无密码登录请求。

因此，为了在调试时接收通知，您有两个选项：

* 为 Api 和身份使用 QA Cloud 端点，或
* 使用本地服务器设置，其中 Api 连接到 QA Azure Notification Hub

### 本地测试无密码 <a href="#testing-passwordless-locally" id="testing-passwordless-locally"></a>

在开始测试和调试无密码登录之前，请确保本地服务器设置运行正常（[服务器设置](../../server/guide.md)）。您还应该能够将 Android App 部署到您的设备或模拟器上。

{% hint style="info" %}
调试和测试无密码身份验证受到[推送通知](android.md#push-notifications)的限制。
{% endhint %}

测试无密码通知：

1. 启动本地服务器（`Api`、`Identity`、`Notifications`）
2. 确保您的移动设备可以[连接到您的本地服务器](android.md#using-server-tunneling)
3. 启动[网页客户端](../../clients/web-vault/)，因为您需要它来发出登录请求
4. 将 Android App 部署到您的设备或模拟器
5. 部署后，打开 App，登录您的 QA 账户然后在设置中激活无密码登录请求
6. 使用您喜欢的浏览器打开网页密码库（例如：http://localhost:8080）
7. 输入之前已在该设备（即「已知设备」）上进行了身份验证的账户的电子邮箱地址，然后点击「继续」。当出现登录选项时，点击「设备登录」。
8. 检查移动设备是否有通知

## AndroidX 凭据[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/android/#androidx-credentials) <a href="#androidx-credentials" id="androidx-credentials"></a>

目前， [androidx.credentials](https://developer.android.com/jetpack/androidx/releases/credentials) 官方绑定存在一些错误，我们还不能使用它。因此，我们自己实现了一个绑定，位于此处：[AndroidX.Credentials](https://github.com/bitwarden/xamarin.androidx.credentials)

目前，我们使用的是 1.2.0 版本。

在项目中，该包被添加为本地 NuGet 包，位于 `lib/android/Xamarin.AndroidX.Credentials`，该源已在 `nuget.config` 文件中配置。

如果需要更改绑定，请创建一个新的本地 NuGet 包并将其替换到上述源中。

{% hint style="danger" %}
请勿将项目添加到解决方案中，也不要将其作为项目引用添加到 `App.csproj`  /  `Core.csproj` 中，这将导致 iOS App 在启动时因解决方案配置而崩溃。尽管我们无法找到根本原因，但这就是该操作造成的影响。
{% endhint %}
