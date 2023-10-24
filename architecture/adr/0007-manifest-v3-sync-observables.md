# 0007 - Manifest V3 sync Observables

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/manifest-v3-browser-memory-caching)
{% endhint %}

| ID：  | ADR-0007   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-07-12 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

Manifest v3 通过禁止长期活动的后台进程，为网络扩展带来了短暂的上下文。这意味着我们需要通过不同的弹出窗口/服务 worker/网络 worker 实例来存储和同步状态。这是通过 `LocalBackedMemoryStorageService` 完成的。

从 StateService 转向可观察量使这变得更加困难，因为可观察量从根本上与扩展实例相关联。

我们需要存储这些可观察量来维护状态。状态应该在初始化时加载，在下一步更新，并通过重新加载消息更新。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **构造函数中的初始化和 UpdateObservables 中的更新** -- 这种方法简单明了，但无法保证我们始终更新存储并发出信息。此外，如果打开侧边栏并通过内容脚本保存密码，也无法实时同步数据。我们可以构建一个计时器来定期从内存中同步这些项目。
* **装饰器模式** -- 使用装饰器来包装服务构造函数。这个装饰器将：
  * 从 `LocalBackedMemoryStorage` 初始化
  * 订阅观察者以更新 `LocalBackedMemoryStorage`，并将事件推送给 worker 以从内存中更新可观察值。
  * 在更新事件上推送 `observable.next`
  * 在这里避免循环事件循环？（循环发生在消息和可观察对象上）
    * 消息可以通过 guid 来修复
    * TODO：消息问题 -> `observable.next` -> 订阅 -> 存储服务未知

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**装饰器模式**。这将是更可重用、更简洁的解决方案。它需要更多的思考来确定如何避免循环事件，但一旦解决了，它也就解决了。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 可重用、简洁的解决方案。
* 遵循可观察到的最佳实践。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 一些未知的实施细节可能会导致开发过程中出现障碍。
