# 安卓移动应用程序

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/clients/mobile/android/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您应该已经安装了推荐的[工具和库](../../tools.md)。您还需要安装：

1. Visual Studio 2019（VS 2022 中尚未包含所需的依赖项；可以在 VS 2022 中完成开发）
2. [.NET Core 3.1（最新版）](https://dotnet.microsoft.com/zh-cn/download/dotnet/3.1)\
   注意：即使您使用的是 ARM 64 架构的 M1 Mac，您也可以安装所有 x64 SDK 来运行 android
3. [Xamarin (Android)](https://learn.microsoft.com/zh-cn/xamarin/get-started/installation/?pivots=macos-vs2022)
4. Android SDK 29\
   您可以使用 [Xamarin](https://learn.microsoft.com/zh-cn/xamarin/get-started/installation/?pivots=macos-vs2022)、[Visual Studio](https://learn.microsoft.com/zh-cn/xamarin/android/get-started/installation/android-sdk) 或 [Android Studio](https://developer.android.com/studio/releases/platforms) 中的 SDK 管理器来安装它

确保您已安装 Android SDK 和模拟器：

1. 打开 Visual Studio
2. 点击 Tools > SDK Manager（在 Android 子标题下）
3. 点击 Tools 选项卡
4. 确保已安装以下项目：
   * Android SDK 工具（至少一个版本的命令行工具）
   * Android SDK平台-工具
   * Android SDK 构建工具（至少一个版本）
   * 安卓模拟器
5. 如果您已标记所有要安装的内容，请单击 Apply Changes

如果您遗漏了任何内容，Visual Studio 应该都会提示您。

## 安卓开发设置 <a href="#android-development-setup" id="android-development-setup"></a>

要设置新的虚拟 Android 设备用于调试：

1. 点击 Tools > Device Manager（在 Android 子标题下）
2. 点击 New Device
3. 设置您要模拟的设备 - 如果您不确定，您可以选择 Base Device 并保留默认设置
4. 然后，Visual Studio 将下载该设备的镜像。下载进度显示在 Android 设备管理器对话框的进度中。
5. 完成后，模拟的 Android 设备将在 Android > Debug >（设备名称）下作为构建目标使用

### M1 Mac <a href="#m1-macs" id="m1-macs"></a>

1. 安装并打开 Android Studio
2. 在顶部导航栏中，点击 Android Studio > Preferences > Appearance & Behavior (tab) > System Settings > Android SDK
3. 在 SDK 平台选项卡中，确保选中「Show Package Details」复选框（位于右下角）
4. 在每一个 Android API 的下方，您会看到一系列系统镜像，选其中的 `ARM 64 v8a` 并等待它下载
5. 转到 View > Tool Windows > Device Manager
6. 在 Device Manager 中，使用之前下载的系统镜像创建一个设备

{% embed url="https://contributing.bitwarden.com/assets/images/android-sdk-529574bf6a67ae6500b23196a6843ec5.png" %}

## 使用安卓模拟器测试和调试 <a href="#testing-and-debugging-using-the-android-emulator" id="testing-and-debugging-using-the-android-emulator"></a>

为了使用本地 Mac 上的 Xamarin Studio 进行调试时访问 Android 模拟器中的 `localhost:<port>` 资源，您需要使用 `<http://10.0.2.2:<port>` 配置端点地址，以访问 `localhost`，它被设计为映射 Android 代理。
