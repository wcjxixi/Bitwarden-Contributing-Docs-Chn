# 0017 - Use Swift to build watchOS app

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/watchOS-use-swift)
{% endhint %}

| ID：  | ADR-0017   |
| ---- | ---------- |
| 状态：  | 完成         |
| 发表于： | 2022-12-30 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

我们希望将 watchOS 应用程序与常规 iOS 应用程序捆绑在一起。watchOS 应用程序将作为一个辅助应用程序，最初提供一种从手表查看之前从 iPhone 同步的 TOTP 代码的方法。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* [使用 Xamarin 的 .Net](https://learn.microsoft.com/en-us/xamarin/ios/watchos/)
* [使用 ](https://developer.apple.com/documentation/watchkit/)[Swift 的 ](https://developer.apple.com/documentation/watchkit/)[WatchKit](https://developer.apple.com/documentation/watchkit/)
* [使用 ](https://developer.apple.com/xcode/swiftui/)[Swift 的 SwiftUI](https://developer.apple.com/xcode/swiftui/)

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**使用 SwiftUI 的 Swift**。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 完全支持 watchOS
* 我们可以正确构建和调试应用程序
* 通过声明式 UI 和预览功能进行快速开发，增强开发体验
* 使用 UI 上的组件组织代码
* 框架和 SDK 的更新可在 Apple 发布后立即获得
* 有更多的文档、示例和公共存储库可供查阅

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 团队需要学习新的技术堆栈
* 即使我们可以正确调试应用程序，我们也不能同时调试 iOS 和 watchOS 应用程序（调试 watchOS 应用程序时，iPhone 上安装了一个存根 iOS 应用程序，因此原始应用程序会被存根应用程序覆盖）
* 由于我们需要将 XCode 构建的 watchOS 应用程序捆绑到 Xamarin iOS 应用程序中并相应地更新 CI，因此设置更加困难

## 方案的优点和缺点​ <a href="#pros-and-cons-of-the-options" id="pros-and-cons-of-the-options"></a>

### 使用 Xamarin 的 .Net  <a href="#net-using-xamarin" id="net-using-xamarin"></a>

* ✅ 保持相同的技术堆栈
* ✅ 共享大量代码
* ✅ 更简单的学习曲线和审查
* ✅ 易于集成到常规 iOS 应用程序中
* ⛔ 影响 watchOS 开发体验的几个重要问题，尤其是无法正确调试
* ⛔ watchOS 平台不是 .Net 团队的优先考虑事项
* ⛔ 没有计划在 MAUI、.Net 7 和 .Net 8 上加入 watchOS 支持

### 使用 WatchKit​ 的 Swift <a href="#swift-using-watchkit" id="swift-using-watchkit"></a>

* ✅ 这是原生方法，意味着它始终是最新的
* ✅ 有大量的文档和示例/项目可供查看
* ✅ 调试工作符合预期
* ⛔ 陡峭的学习曲线（语言 + Watch 相关的内容）
* ⛔ 难以集成到常规 iOS 应用程序中

### 使用 SwiftUI 的 Swift ​ <a href="#swift-using-swiftui" id="swift-using-swiftui"></a>

* ✅ 这是原生方法，意味着它始终是最新的
* ✅ 有大量的文档和示例/项目可供查看
* ✅ 调试工作符合预期
* ✅ 使用 SwiftUI 框架快速开发
* ✅ 预览功能让开发体验更上一层楼，大大减少了工作量
* ⛔ 陡峭的学习曲线（语言 + Watch 相关的内容 + SwiftUI 框架）
* ⛔ 难以集成到常规 iOS 应用程序中
* ⛔ SwiftUI 还不够完善，因此需要注意一些特殊的导航和渲染问题
