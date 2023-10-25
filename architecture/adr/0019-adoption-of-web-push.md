# 0019 - Adoption of Web Push

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/adopt-web-push)
{% endhint %}

| ID：  | ADR-0019   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2023-02-06 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

现在，数百万用户利用推送通知进行后台密码库同步和无密码登录请求。随着平台的发展，维护当前基于 [SignalR](https://dotnet.microsoft.com/en-us/apps/aspnet/signalr) 的解决方案（该解决方案使用 WebSockets）的需求也在不断增长，该系统在部署或其他应用程序更新时会给云组件带来巨大压力。加上还需要支持使用专有推送通知协议的移动设备，因此需要一种下一代混合服务来简化和扩大通知交付。

### 现代基础设施管理​ <a href="#modern-infrastructure-management" id="modern-infrastructure-management"></a>

通知服务是一个独立部署的组件，对于 Bitwarden 托管版本而言，它由数十台虚拟机提供支持，每台虚拟机支持数以万计的并发连接。随着云中手动组件维护的减少（特别是 Kubernetes），维持与 [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/) 的大量连接的负担变得复杂，预计性能会出现峰值。

### WebSockets​ <a href="#websockets" id="websockets"></a>

从表面上看，SignalR 的使用，即使是在工作负荷大得多的情况下，也运行得很好，但所执行工作的性质以及某些客户端对持久 WebSockets 连接的使用已变得十分脆弱。总的来说，通知服务的输入和输出量非常大，并且几乎全部用于面向后台的操作。各种更适合同步工作的新技术已经出现。所使用的技术或协议必须可在云中大规模使用，也能用于自托管安装。

浏览器扩展的变更和强制要求（例如 [Manifest V3](https://developer.chrome.com/docs/extensions/mv3/intro/)）也带来了长期后台连接的支持问题。

### 成本和可靠性​ <a href="#cost-and-reliability" id="cost-and-reliability"></a>

新的解决方案不仅要能扩展到基本无限的连接，还要能平衡成本与用户增长，同时提供高可用性。现有的移动通知方案要么有设备限制（例如 [Azure 通知中心](https://azure.microsoft.com/en-us/pricing/details/notification-hubs/)），要么缺乏服务级别保证，同时也不提供现代技术。此外，某些客户端（例如 [F-Droid](https://mobileapp.bitwarden.com/fdroid/)）无法利用成熟的（尽管是专有的）推送后端。单一服务提供商需要提供尽可能多的功能，同时还能为自托管提供灵活性。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

关于移动通知：

* **解决 Azure 通知中心限制** -- 由于证书安全性问题，所有移动设备（即使是自托管安装）都必须利用 Bitwarden 云进行推送，因此应设计一种解决方案，在新订阅中将设备分散到多个通知中心。
* **采用新的移动推送通知产品** -- 使用 Azure 通知中心以外的其他没有限制的产品，可能需要牺牲一些技术。
* **采用新的组合推送服务提供商** -- 不仅要从 Azure 通知中心迁移，还要选择支持本地移动通知以及 Web Push 等其他所需协议的服务提供商。

关于协议现代化：

* **保留 SignalR 解决方案并继续扩大其规模** -- 保留通知服务虚拟机集群，并不断扩大集群以应对规模。同时将所有移动通知转移到自定义解决方案，放弃 Azure 通知中心。
* **采用新的非移动推送通知产品** -- 在通知服务中使用 SignalR 以外的其他产品，如自研 Web Push 后端。为有需要的客户提供兼容的 Web Push 后端。
* **采用新的组合推送服务提供商** -- 不仅要尽可能从 SignalR 迁移，还要选择支持本地移动通知协议的服务提供商。另外，如上所述，还要为自托管实现 Web Push 后端。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**采用新的组合推送服务提供商。**

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* Web Push 在我们需要它的大多数地方都得到了很好的支持，而且是一种有价值的技术升级，易于将来维护。
* 一些服务提供商为我们的云产品提供了 Web Push 后端，而且该协议也可以在自托管的通知服务中实施。
* Web Push 非常适合 Manifest V3 和 Service Worker。
* 通知服务的基础设施维护负担和成本将大幅降低。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 需要关注 Safari 对 Web Push 的支持，该支持在撰写本文时刚刚发布。
* 选择不同的服务提供商可能会增加成本。

### 计划​ <a href="#plan" id="plan"></a>

通知服务将继续存在，并支持 SignalR 连接的 API 以及 Web Push 的新 API。基于云的客户端将在可行的情况下连接到具有 Web Push 的统一服务提供商，并在无法利用 Web Push 时利用现有的 SignalR 实现。自托管客户端将利用通知服务提供的 Web Push 和 SignalR 类似组合。Web Push 所需的密钥交换和安全性 (VAPID) 可以使用现有的内部技术用于自托管，以及服务提供商用于云托管。随着时间的推移，客户端将大部分迁移到 Web Push 连接，SignalR 的负载也将大大减少，不过在可预见的未来，SignalR 仍计划为某些客户端提供支持。

所有移动设备都将迁移到同一个统一服务提供商，该服务提供商除支持 Web Push 外，还支持本地 iOS (APNS) 和 Android (FCM) 推送通知协议。尽管 SignalR 实现仍然可用，但对于不兼容的客户端，未来将考虑在支持 Web Push 的同时支持 [Unified Push](https://unifiedpush.org/)（统一推送）。

在将兼容客户端迁移到新提供商时，将考虑对推送通知有效载荷进行端到端加密（使用用户加密密钥），以便为潜在敏感的有效载荷内容提供更强的安全性。
