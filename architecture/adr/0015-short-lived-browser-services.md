# 0015 - Short Lived Browser Services

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/short-lived-browser-services)
{% endhint %}

| ID：  | ADR-0015   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-12-09 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

[ADR-003](0003-adopt-observable-data-services-for-angular.md) 将 Observables 的使用引入到了我们的 TypeScript 代码库中。该 ADR 描述了使用 `ngDestroy` 触发取消订阅所有已订阅的 `Observables` 。但是，只有当使用 Angular 路由器离开页面时才会调用 `ngDestroy` 。在 SPA 中，这不是问题。如果没有使用路由器，则意味着您关闭了页面，SPA 已失效。在浏览器扩展中，我们有一个持久的背景，可以在此类关闭事件中存活下来。这意味着观察者队列中存在的订阅不再存在。

特别是在 Firefox 中，这是一个灾难性的失败。Firefox 将用 `DeadObject` 替换为属于不再存在的 DOM 对象的所有对象。当在 `Subject` 上调用 `next()` 时， `DeadObject` 会被视为观察者并抛出错误，从而阻止向后续观察者发出任何进一步的通知，并导致扩展中出现灾难性的中断。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **还原 ADR 003 并移除 Observables** -- 我们从代码库中删除 Observables 并还原到之前的状态。
* **浏览器事件** -- 我们使用 `beforeunload`、`unload`、`visibilityChange` 和/或 `pageHide` 事件来触发 Angular 服务的销毁。
  * 这些触发器并不保证会被调用，并且可能不会在所有浏览器中被调用。
* **短期主题** -- 我们确保在组件中创建的订阅不会引用长期订阅。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**短暂主题**。

这种方案最为灵活，既能保留 Observables 的优点，又能避免与页面可见性事件相关的不稳定性。

这些短期主题将通过创建可视化级服务来实现，这些服务的生命周期与它们所服务的组件相同。数据将在这些短期服务和后台的长期扩展之间同步。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 对编写组件级代码没有影响
* 无论如何都会将我们引向 Manifest V3 所需的方向
* 绕过由于悬空订阅而导致的潜在内存泄漏

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 由于需要根据可视化和后台上下文创建服务的可观察量，因此增加了浏览器扩展的内存占用。
* 需要在前台和后台上下文之间同步服务的可观察量。
* 需要对服务的可观察量进行更复杂的实现。

### 在上下文之间同步主题​ <a href="#synching-a-subject-between-contexts" id="synching-a-subject-between-contexts"></a>

上下文之间的主题同步是使用 `Observable` 订阅、浏览器消息传递 API 和浏览器存储 API 的组合来开发的。

存储或直接对象共享被用作公共数据存储。订阅用于写入公共存储和发出消息，这会触发另一端的后续读取。

### 实施浏览器服务​ <a href="#implementing-a-browser-service" id="implementing-a-browser-service"></a>

在本节中，我们将通过一个示例介绍如何实现浏览器服务。我们将使用 `FolderService` 作为示例。

`FolderService` 提供了两个 `Observables` ，由 `BehaviorSubject`、`_folders` 和 `_folderViews` 支持。

```typescript
export class FolderService {
  private _folders = new BehaviorSubject<Folder[]>([]);
  private _folderViews = new BehaviorSubject<FolderView[]>([]);

  readonly folders = this._folders.asObservable();
  readonly folderViews = this._folderViews.asObservable();
}
```

以下各章节讨论将服务的可观察量分为前台和后台上下文所需的更改。

#### **libs** 服务​ <a href="#the-libs-service" id="the-libs-service"></a>

libs 服务需要使支持 `Observable` 的 `Subject` 可供扩展它的浏览器服务使用。

```diff
export class FolderService {
-  private _folders = new BehaviorSubject<Folder[]>([]);
-  private _folderViews = new BehaviorSubject<FolderView[]>([]);
+  protected _folders = new BehaviorSubject<Folder[]>([]);
+  protected _folderViews = new BehaviorSubject<FolderView[]>([]);

  readonly folders = this._folders.asObservable();
  readonly folderViews = this._folderViews.asObservable();
}
```

#### 浏览器服务​ <a href="#the-browser-service" id="the-browser-service"></a>

浏览器服务必须简单地扩展库服务并将其自身和 `Subject` 包装在一些装饰器中。

```typescript
@browserSession
export class BrowserFolderService extends FolderService {
  @sessionSync({ initializer: Folder.fromJSON, initializeAs: "array" })
  protected _folders: BehaviorSubject<Folder[]>;
  @sessionSync({ initializer: FolderView.fromJSON, initializeAs: "array" })
  protected _folderViews: BehaviorSubject<FolderView[]>;
}
```

`@sessionSync` 装饰器负责注册要同步的属性，并提供有关如何在需要序列化数据时初始化数据的信息。 `@browserSession` 装饰器读取注册的属性并在给定类的所有实例之间设置同步。

#### 依赖注入​ <a href="#dependency-injection" id="dependency-injection"></a>

一旦服务实现，就可以向 Angular 的依赖注入系统注册。请务必更新或添加任何必要的提供程序。

#### **main.background** <a href="#main.background" id="main.background"></a>

`@browserSession` 仅在相同的类之间同步，因此后台服务需要使用与前台服务相同的类。目前，后台服务在 `main.background.ts` 中初始化。

```diff
-import { FolderSerivce } from '@bitwarden/common/src/services/folder.service';
+import { BrowserFolderService } from '../services/browser-folder.service';

-this.folderService = new FolderService(
+this.folderService = new BrowserFolderService(
  this.cryptoService,
  this.i18nService,
  this.cipherService,
  this.stateService
);
```
