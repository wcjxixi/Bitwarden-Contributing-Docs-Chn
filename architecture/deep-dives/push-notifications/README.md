# 推送通知

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/push-notifications/)
{% endhint %}

Bitwarden 使用推送通知从 Bitwarden 服务器与其客户端进行实时通信。

## Bitwarden 将其用于 <a href="#uses-at-bitwarden" id="uses-at-bitwarden"></a>

目前，此功能用于：

* 在客户端之间同步密码库
* 无密码登录请求

## 使用的技术 <a href="#technology-in-use" id="technology-in-use"></a>

Bitwarden 服务器在执行相关操作时启动推送通知，通常是在用户数据发生更改且需要同步时，或者执行需要用户输入的操作时。

目前有两种不同的方法用于推送通知：

* 移动应用程序使用 Azure 通知中心，它是 Apple 和 Android 生态系统的推送通知服务 (PNS) 的抽象。自托管实例通过 Bitwarden 云托管服务中继其移动消息。
* 其他客户端使用 SignalR，它是一种双向 RPC，通常通过 WebSocket（但也有其他协议）来与我们的非移动客户端建立实时客户端通信。
