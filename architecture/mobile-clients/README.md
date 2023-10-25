# 移动客户端架构

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/mobile-clients/)
{% endhint %}

移动客户端包括 Android、iOS 及其扩展和 watchOS。它们都位于此 [Git 存储库](https://github.com/bitwarden/mobile)中。

主要结构如下：

* `Core`：侧重于应用程序逻辑部分的共享代码。有几个类是从 Web 客户端移植到 C# 的。
* `App`：共享代码，侧重于应用程序的表示层和一些业务逻辑。
* `Android`：特定于 Android 平台的所有代码
* `iOS`：特定于 iOS 平台的所有代码
* `iOS.Core`：iOS 应用程序及其扩展使用的共享代码
* `iOS.Autofill`：处理自动填充的 iOS 扩展
* `iOS.Extensions`：从底层表扩展处理自动填充的 iOS 扩展
* `iOS.ShareExtension`：通过 Send 处理共享文件的 iOS 扩展
* `watchOS`：特定于 watchOS 平台的所有代码
  * `bitwarden`：存根 iOS 应用程序，以便 watchOS 应用程序在 XCode 上有一个配套应用程序
  * `bitwarden WatchKit App`：主 Watch 应用程序，我们在其中设置资产。
  * `bitwarden WatchKit Extension`：Watch 应用程序的所有逻辑和表示逻辑都在这里

## 依赖关系图 <a href="#dependencies-diagram" id="dependencies-diagram"></a>

下面是移动存储库的简化依赖关系图。

<div align="left">

<figure><img src="../../.gitbook/assets/simplified-dependencies-diagram.svg" alt=""><figcaption></figcaption></figure>

</div>
