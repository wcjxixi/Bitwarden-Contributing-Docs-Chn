# 0003 - Adopt Observable Data Services for Angular

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/observable-data-services)
{% endhint %}

| ID：  | ADR-0003   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-06-30 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

我们的许多组件和服务与不同域的状态紧密耦合。这导致了紧密耦合和难以修改不同区域。这导致使用 `MessagingService` 服务通过发送事件来同步状态更新。虽然事件溯源是一种完全有效的软件开发方式，但我们当前的事件是空的，这导致组件需要手动重新获取其状态。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* [可观察/反应数据服务](https://blog.angular-university.io/how-to-build-angular2-apps-using-rxjs-observable-data-services-pitfalls-to-avoid/)
* [NGRX](https://ngrx.io/) - Angular 的反应状态（Redux 实现）

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**可观察数据服务**，因为

* 使我们能够快速迭代到更具反应性的数据模型。
  * 反应式数据模型让我们摆脱事件消息。
  * 组件将始终显示最新状态。
* 不需要大量的前期投资。
* 响应式数据模型的工作将使我们能够在未来需要时采用 NGRX 等模式。

### 示例​ <a href="#example" id="example"></a>

#### 组织​ <a href="#organizations" id="organizations"></a>

`OrganizationService` 应拥有所有组织相关的数据的所有权。

```typescript
class OrganizationService {
  private _organizations: new BehaviorSubject<Organization[]>([]);
  organizations$: Observable<Organization[]> = this._organizations$.asObservable();

  async save(organizations: { [adr: string]: OrganizationData }) {
    await this._organizations$.next(await this.decryptOrgs(this._activeAccount, organizations));
  }
}

class Component implements OnDestroy {
  private destroy$: Subject<void> = new Subject<void>();

  ngInit() {
    this._organizationService.organizations$
      .pipe(takeUntil(this.destroy$))
      .subscribe((orgs) => {
        this.orgs = orgs;
      });
  }

  ngOnDestroy() {
    this.destroy$.next();
    this.destroy$.unsubscribe();
  }
}
```

在此示例中，我们使用 `takeUntil` 模式，该模式可以与 eslint 规则结合使用，以确保每个组件自行清理。

## 方案的优点和缺点​ <a href="#pros-and-cons-of-the-options" id="pros-and-cons-of-the-options"></a>

### 可观察数据服务​ <a href="#observable-data-services" id="observable-data-services"></a>

* 优点：轻量级
* 优点：可以增量重构
* 优点：反应灵敏，更改时将通知
* 缺点：没有严格的标准，更多的是一套指导方针
* 缺点：状态被分成多个微小的「stores」

### NGRX​ <a href="#ngrx" id="ngrx"></a>

NGRX 是 Angular 最受欢迎的 Redux 实现。有关更多详细信息，请阅读 [redux 背后的动机](https://redux.js.org/understanding/thinking-in-redux/motivation)以及查看此 [NGRX 架构图](https://ngrx.io/guide/store)。

* 优点：Angular 是最受欢迎的 redux 库
* 优点：解耦组件
* 优点：单一状态简化了操作
* 缺点：需要对整个状态层进行重大重写
* 缺点：增加了复杂性，并且可能使数据流难以理解
