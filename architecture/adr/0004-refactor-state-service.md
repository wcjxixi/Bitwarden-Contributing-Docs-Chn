# 0004 - Refactor State Service

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/refactor-state-service)
{% endhint %}

| ID：  | ADR-0004   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-06-30 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

此 ADR 建立在[采用适用于 Angular 的可观察数据服务](0003-adopt-observable-data-services-for-angular.md)的基础上。

Bitwarden 客户端目前拥有相当复杂的状态架构，其中所有状态均由单个服务处理。这导致一切都与 `StateService` 紧密耦合，本质上使其成为上帝对象。

此外，任何服务或组件都可以使用状态服务直接访问任何状态。这使得跟踪每种数据类型的状态生命周期变得困难，并给数据访问方式带来了不确定性。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

我们应该将状态服务重构为通用存储容器。

* 优点：消除了状态服务的「good」功能
* 优点：状态由拥有它的服务维护
* 优点：不能任意访问数据
* 缺点：返回必须是唯一的任意键

### 示例​ <a href="#example" id="example"></a>

```typescript
interface StateService {
  getAccountData<T>: (account: Account, key: string, options?: StorageOptions) => Promise<T>;
  saveAccountData: (account: Account, key: string, options?: StorageOptions) => Promise<void>;
  deleteAccountData: (account: Account, key: string, options?: StorageOptions) => Promise<void>;

  deleteAllAccountData: (account: Account);

  getGlobalData<T>: (key: string, options?: StorageOptions) => Promise<T>;
  saveGlobalData: (key: string, options?: StorageOptions) => Promise<void>;
  deleteGlobalData: (key: string, options?: StorageOptions) => Promise<void>;
}
```

```typescript
// StorageKey is an internal constant, and should be prefixed with the domain.
//  DO NOT EXPORT IT.
const StorageKey = "organizations";

class OrganizationService {
  async save(organizations: { [adr: string]: OrganizationData }) {
    await this._stateService.saveAccountData(this._activeAccount, StorageKey, organizations);
    await this._organizations$.next(await this.decryptOrgs(this._activeAccount, organizations));
  }
}
```
