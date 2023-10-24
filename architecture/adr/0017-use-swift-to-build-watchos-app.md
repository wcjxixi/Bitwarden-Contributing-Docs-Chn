# =0017 - Use Swift to build watchOS app

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/watchOS-use-swift)
{% endhint %}

| ID：  | ADR-0017   |
| ---- | ---------- |
| 状态：  | 完成         |
| 发表于： | 2022-12-30 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

我们希望将 watchOS 应用程序与常规 iOS 应用程序捆绑在一起。watchOS 应用程序将充当配套应用程序，并最初提供一种从手表查看之前从 iPhone 同步的 TOTP 代码的方法。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* [使用 Xamarin 的 .Net](https://learn.microsoft.com/en-us/xamarin/ios/watchos/)
* [使用 ](https://developer.apple.com/documentation/watchkit/)[Swift 的 ](https://developer.apple.com/documentation/watchkit/)[WatchKit](https://developer.apple.com/documentation/watchkit/)
* [使用 ](https://developer.apple.com/xcode/swiftui/)[Swift 的 SwiftUI](https://developer.apple.com/xcode/swiftui/)

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**使用 SwiftUI Swift**。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 完全支持watchOS
* 我们可以正确构建和调试应用程序
* 通过声明式 UI 和预览进行快速开发，以增强开发体验
* 使用 UI 上的组件组织代码
* Apple 发布后即可提供框架和 SDK 的更新
* 还有更多文档、示例和公共存储库可供检查

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 供团队学习的新技术堆栈
* 即使我们可以正确调试应用程序，我们也不能同时调试 iOS 和 watchOS 应用程序（调试 watchOS 应用程序时，iPhone 上安装了一个存根 iOS 应用程序，因此原始应用程序会被存根应用程序覆盖）
* 由于我们需要将 XCode 构建的 watchOS 应用程序捆绑到 Xamarin iOS 应用程序中并相应地更新 CI，因此设置更加困难

## 方案的优点和缺点​ <a href="#pros-and-cons-of-the-options" id="pros-and-cons-of-the-options"></a>

### 使用 Xamarin 的 .Net  <a href="#net-using-xamarin" id="net-using-xamarin"></a>

### 使用 WatchKit​ 的 Swift <a href="#swift-using-watchkit" id="swift-using-watchkit"></a>

### 使用 SwiftUI 的 Swift ​ <a href="#swift-using-swiftui" id="swift-using-swiftui"></a>
