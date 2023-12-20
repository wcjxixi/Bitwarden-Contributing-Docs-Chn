# 依赖管理

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/dependencies/)
{% endhint %}

Bitwarden 使用 [Renovate](https://www.mend.io/renovate/) 来自动更新依赖项。Renovate 会每周自动为依赖项创建拉取请求。安全更新会立即生成拉取请求。

## 所有权​ <a href="#ownership" id="ownership"></a>

Bitwarden 的存储库分为两类：团队拥有的和共享的。

### 团队拥有的存储库​ <a href="#team-owned-repositories" id="team-owned-repositories"></a>

从依赖性的角度来看，团队拥有的存储库由单个团队「拥有」。指定的团队负责审查、批准和合并依赖项更新。一个存储库可能由团队拥有的一些原因是该存储库主要由该团队开发，或者是为了平衡团队需要管理的依赖项数量。

团队拥有的存储库的一些示例是 [`directory-connector`](https://github.com/bitwarden/directory-connector)（由管理控制台团队拥有）和 [`key-connector`](https://github.com/bitwarden/key-connector/)（由身份验证团队拥有）。

### 共享的存储库​ <a href="#shared-repositories" id="shared-repositories"></a>

共享存储库没有任何直接拥有者。相反，每个依赖项都分配给一个团队。分配给某个依赖项的团队负责审查、批准和合并该依赖项。对于重大升级，该团队负责与其他团队协调升级。

共享的存储库的示例是 [`server`](https://github.com/bitwarden/server/) 和 [`clients`](https://github.com/bitwarden/clients/)。

## PR 示例​ <a href="#example-pr" id="example-pr"></a>

{% embed url="https://contributing.bitwarden.com/assets/images/renovate-pr-50085e99a2b40ed978266a67c808e53a.png" %}
Renovate PR 示例
{% endembed %}

Renovate PR 包含多个相关领域。上面的 PR 示例包含了两个分组的依赖项。PR 提议将依赖项从 `6.0.21` 升级到 `7.0.12`。该版本的存在时间为 **13 天**，**13%** 的存储库已采用该版本。Renovate 在 Renovate 管理的存储库中的测试成功率为 **74%**，以及对这一更改的置信度较低。有关更多详细信息，请参阅[合并置信度的 Renovate 文档](https://docs.renovatebot.com/merge-confidence/)。

## 工作流程 <a href="#workflow" id="workflow"></a>

Renovate 会在周末自动创建拉取请求，这自然与每个团队在下周一分配一些时间来处理各自团队中的拉取请求相吻合。团队应共同努力在一周内解决未完成的拉取请求，以避免工作停滞。

{% hint style="info" %}
主要的例外是重大升级，有时可能需要更长的时间来协调。理想情况下，团队会提前协调并解决弃用问题。
{% endhint %}

Renovate PR 可能包含单个依赖项或一组相关依赖项。在 Bitwarden，我们通常会将已知相关且应同时升级的依赖项进行分组。我们尽量使分组尽可能小，以最大程度地减少影响并增加批准和合并的信心。

### 审查 <a href="#review" id="review"></a>

典型的依赖关系工作流包括以下步骤：

1. 阅读提议的变更。
2. 审查当前升级和建议升级之间每个已发布版本的每个依赖项的发行说明。确定是否有影响我们代码的任何弃用或破坏性变更。
   * 对于破坏性变更，要么自己解决，要么与其他团队协调解决重大变更。
   * 对于弃用，在受影响团队的积压工作中创建高优先级的 Jira 票证，到期日期至少要比下一个计划的主要依赖项版本早一个冲刺。
3. 验证 CI 状态。
4. 如果测试覆盖率不足，请在本地检查并手动确认几个关键区域。
5. 审查提议的代码变更并批准 PR。
6. 编写包含 QA 测试说明的 Jira 票证。
7. 合并 PR。将 Jira 票证分配给 QA。

### Jira 票证 <a href="#jira-ticket" id="jira-ticket"></a>

开发人员和 QA 之间的交接将通过 Jira 票证进行。该票证应包含受影响的依赖项、要测试部分的任何相关发行说明，以及受影响区域的一些测试说明。

### QA 测试​ <a href="#qa-testing" id="qa-testing"></a>

虽然开发人员负责编写带有测试说明的 Jira 票证，但 QA 工程师应该进行尽职尽责，同时考虑依赖项变更的影响，并在需必要时与工程师讨论可能增加或减少测试范围的问题。

### 回归 <a href="#reverting" id="reverting"></a>

如果 QA 发现了回归，开发人员应负责评估影响并立即还原更新或在新 PR 中解决回归问题。

### 关闭无关的 PR <a href="#closing-irrelevant-prs" id="closing-irrelevant-prs"></a>

有时，由于各种原因，Renovate 会为我们目前无法升级的依赖项创建 PR。例如， `contributing-docs` 依赖于 `docusaurus` ，而后者支持特定版本的 `react`。在 `docusaurus` 支持它之前，我们无法升级 `react`。

在这些情况下，团队可以对 PR 注释不升级的原因，然后关闭 PR 或推迟到以后再升级。如果团队关闭了 PR，则希望其成员监控其依赖性，并在未来重新考虑升级问题。

## 更新配置 <a href="#renovate-configuration" id="renovate-configuration"></a>

Renovate 通过每个存储库中的 `.github/renovate.json` 文件进行配置。为了保持一致性，我们遵循一个内部模板。该模板可在[模板库](https://github.com/bitwarden/template/blob/main/.github/renovate.json)中获取。

Renovate 使用一个名为 [`PackageRules`](https://docs.renovatebot.com/configuration-options/#packagerules) 的概念，它允许我们指定依赖项的所有权，并确保将适当的团队添加为审查者。下面是将 `@angular/core` 指派给 Platform 团队的示例。

```json
{
  "matchPackageNames": ["@angular/core"],
  "description": "Platform owned dependencies",
  "commitMessagePrefix": "[deps] Platform:",
  "reviewers": ["team:team-platform-dev"]
}
```

对于由单个团队维护的存储库，无需使用 `packageRules` 来分配所有权。相反，请确保设置了适当的代码所有者。
