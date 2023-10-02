# Vision

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/clients/services/vision)
{% endhint %}

## 术语定义 <a href="#definitions-of-terms" id="definitions-of-terms"></a>

* **`[Domain]Service`** ：通用术语，指我们用来处理域问题的任何服务。例如 CipherService、OrganizationService 等。一般来说，以名词开头的服务是 `[Domain]Service` 的一个示例。
* **`StorageService`** ：负责将信息存储到三个位置（磁盘、安全磁盘或内存）之一的服务。
* **全知类**：任何知道太多或做太多事情的类。
* **魔术字符串**：用于指定行为的字符串。
* **账户切换**：Bitwarden 功能允许在多个帐户之间切换，而无需注销/重新登录。于 2021 年底开发。
* **状态**：状态是事物的样子；其配置、属性、条件或信息内容。

## 我们在哪里以及我们是如何到达这里的 <a href="#where-we-are-and-how-we-got-here" id="where-we-are-and-how-we-got-here"></a>

### `StorageService` <a href="#storageservice" id="storageservice"></a>

在账户切换之前，客户端状态完全通过直接调用 `StorageService` 对象和魔术字符串键来处理。设置以及获取的值是分布在整个应用程序中的，而不是本地化的。如果一个类需要一些已存储的数据，它会使用带有特定键的 `StorageService` 上的 `get()` 方法。当键相对较少且存储模型不太复杂时，这种方法效果很好。对于账户切换，状态模型对于这种分布式方法来说过于复杂，我们需要一个集中位置来跟踪要获取的几个相似键中的哪一个。那就是 `StateService` 。

### `StateService` <a href="#stateservice" id="stateservice"></a>

`StateService` 的工作原理是维护一个已验证状态的数组，并跟踪该数组中在任何给定时间要使用的实体。始终假定当前活动用户是 `get()` 或 `set()` 所指的用户，因此使用该用户存储/检索实体。此外， `StateService` 通常用于保存解密项目的内存缓存，因为它能够跨上下文同步数据。

#### **`StateService` 痛点** <a href="#stateservice-pain-points" id="stateservice-pain-points"></a>

**内存中和磁盘上的项目保存在同一个对象中**

`Account` 用于存储有关账户的所有数据，无论存储位置如何。之所以这样做，是因为我们假设服务会在逐次调用的基础上定期更改存储位置。但这种情况从未发生过，我们现在需要复杂的逻辑来管理可能包含解密机密的对象的过于危险的序列化。

**`StateService` 是一个全知类**

在创建 `StateService` 时，我们的主要痛点是魔法字符串的使用。我们认为，通过将这些字符串限制为对象键，可以创建一个更安全的存储系统。事实的确如此，但它将所有可以存储的数据都集中到了一个类中。

## 我们要去哪里以及为什么 <a href="#where-were-going-and-why" id="where-were-going-and-why"></a>

我们对 Bitwarden 客户端的状态管理有一个终极设计：

* 客户端可以及时响应状态数据更新
  * 通过更有效地利用 Angular 简化可视化层
  * 通过减少完全刷新事件的需求来改进用户体验
* 状态更新通过 RxJS Observables 广播
  * 允许通过 `BehaviorSubject` 免费缓存数据
  * 允许任意订阅数据，而无需服务的任何实现细节
* 特定状态项完全由控制特定数据片段的服务管理
  * 关注点分离提高了代码隔离性和可维护性
  * 解构 `StateService` 全知类
  * 由于删除了上帝类别，减少了总体依赖计数
* `StateService` 是各种存储位置的容器
  * 单一责任 → 将数据正确路由到适当的存储位置
* 服务最终将简化为提供程序，通过 Observable 提供填充状态的类实例
  * 单一责任 → 宣布其控制的状态对象的更改
  * 可观察类将负责现在由 `<Domain>Services` 控制的各种辅助方法

一般来说，上述架构的目标是：

* 简化并减少所有对象的职责
* 允许反应式应用程序设计
