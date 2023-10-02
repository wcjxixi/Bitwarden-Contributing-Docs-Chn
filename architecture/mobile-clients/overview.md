# =概览

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/mobile-clients/overview)
{% endhint %}

移动应用程序的整体架构与遵循分层架构的网络客户端非常相似：

* 状态层
* 服务层
* 表示层

尽管状态层和服务层与 Web 层非常相似，但表示层还是有所不同：

## 表示层 <a href="#presentation" id="presentation"></a>

对于移动应用程序，表示层是使用 `Xamarin.Forms` 实现的，但watchOS 除外，它使用 `SwiftUI`，请参阅 [ADR](../adr/0017-use-swift-to-build-watchos-app.md)。
