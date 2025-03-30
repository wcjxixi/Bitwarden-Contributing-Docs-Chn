# watchOS

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/watchos/)
{% endhint %}

## 要求​ <a href="#requirements" id="requirements"></a>

按照 [iOS 设置](ios.md)进行操作。

为了使一切正常工作，需要使用设备。在模拟器上，同步将无法正常工作（某些其他部分可能也无法正常工作）。此外，尽可能启用蓝牙功能，以简化设备间的同步和调试通信。

建议同时阅读 [watchOS 架构](../../../architecture/mobile-clients/watchos.md)。

## macOS 设置​ <a href="#macos-setup" id="macos-setup"></a>

按照 iOS 的 macOS 设置，在 XCode 中打开项目时，它将自动设置配置文件，因此无需额外配置。

## 调试​ <a href="#debugging" id="debugging"></a>

可以从两个位置进行调试：

* 从 Visual Studio for Mac（iOS 应用程序）
* 从 XCode（watchOS 应用程序）

目前，无法同时调试两个应用程序（iOS 和 watchOS），因为从 Xamarin 中无法访问 watchOS 应用程序的调试信息，并且从 XCode 中，iPhone 上安装了一个 iOS 存根应用程序来对其进行调试。因此，在调试时需要选择要了解哪一部分的信息，从而确定是从 VS4M 还是从 XCode 进行调试。

{% hint style="warning" %}
从 XCode 进行调试时，Xamarin iOS 应用程序将被替换为来自 XCode 的存根应用程序。因此 iOS 应用程序上的任何配置都将丢失（例如服务器 URL）

从 VS4M 进行调试时，请在两次构建之间从 Apple Watch 上卸载之前的 watchOS 应用程序（如果有的话），以使其始终保持最新（如果不卸载以前的 watchOS 应用程序，有时它就不会更新）
{% endhint %}

### 构建 <a href="#building" id="building"></a>

鉴于 Xamarin iOS 应用程序需要 Xcode 构建的输出，因此需要先从 XCode 构建 watchOS 应用程序，然后再从 VS4M 构建 iOS 应用程序，以便在设备上运行。

XCode 构建的输出存储在与 `iOS.csproj` 中配置的附近位置非常相似的位置：

```xml
<PropertyGroup>
    <WatchAppBuildPath Condition=" '$(Configuration)' == 'Debug' ">$(Home)/Library/Developer/Xcode/DerivedData/bitwarden-cbtqsueryycvflfzbsoteofskiyr/Build/Products</WatchAppBuildPath>
```

每一台 Mac 上的文件夹 `bitwarden-cbtqsueryycvflfzbsoteofskiyr` 很可能都不相同。因此，我们需要将 `iOS.csproj` 中的这一部分更改为 XCode 在本地自动创建的部分。

要知道路径确切是什么：在 XCode 中打开项目 -> 转到产品 ->「在 Finder 中显示构建文件夹」。

_这一点需要改进，以便有一个固定的位置，或者有一个更简单的方法来自动获取它。_

{% hint style="warning" %}
需要特别注意两个 IDE 上的目标平台相同。因此，在设备上运行时，在 XCode 和 VS4M 上构建时都要以设备为目标，这样才能正确绑定 watchOS 应用程序。此外，还需要确保选择了「bitwarden WatchKit app」方案。
{% endhint %}

### 同步​ <a href="#synchronization" id="synchronization"></a>

由于上述原因，无法同时完全调试同步。

因此，我们只能调试一端 (iOS) 或另一端 (watchOS)。

如果需要「同时」检查两端的某些内容（如检查消息未发送/到达的原因），则需要使用控制台日志或将 Xamarin 代码的一部分调整到 Xcode 上的 iOS 存根应用程序，并从 XCode 调试同步。
