# UI 审查 - Chromatic

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/pull-requests/chromatic)
{% endhint %}

我们使用一种名为 [Chromatic](https://www.chromatic.com/) 的工具，对所有 Storybook Story 进行自动快照测试。这使我们能够以自动化的方式快速捕捉设计回归。作为其中的一部分，我们还使用 Chromatic 来审查和批准可视化更改。Bitwarden GitHub 组织的成员可以使用他们的 GitHub 账户登录 Chromatic。

## 检查​ <a href="#checks" id="checks"></a>

Chromatic 将审核流程分为两个部分：用户界面测试 (UI Tests) 和用户界面审核 (UI Review)。这些在 PR 上表现为两种不同的检查，需要以不同的方式处理。下面详细介绍了处理每一项的期望。

### UI 测试​ <a href="#ui-tests" id="ui-tests"></a>

> UI 测试捕获云浏览器环境中每个 Story 的视觉快照。每当您推送代码时，Chromatic 都会生成一组新的快照并将它们与基线进行比较。如果有视觉变化，您需要验证它们是否是故意的。
>
> _https://www.chromatic.com/docs/test_

### UI 审查​ <a href="#ui-review" id="ui-review"></a>

> UI 测试可以保护您免受意外回归的影响。但是，在发布之前，您需要邀请开发人员、设计师和产品经理检查 UI 以确保其正确。
>
> UI 审查创建由 PR 引入的精确视觉更改的变更集。您指定审阅者，他们可以对不太正确的更改发表评论并请求调整。可以将其视为代码审查，但针对的是您的 UI。
>
> _https://www.chromatic.com/docs/review_

## 审查​ <a href="#reviewing" id="reviewing"></a>

如果存在视觉变化，Chromatic 会将拉取请求标记为待处理。每个拉取请求作者都有责任审查 Chromatic 中的 UI 测试结果，并批准更改是否是有意的。

通过单击拉取请求中的 **UI 测试**检查，可以轻松访问测试。

<figure><img src="https://raw.githubusercontent.com/bitwarden/contributing-docs/master/docs/contributing/pull-requests/ui-tests.png" alt=""><figcaption></figcaption></figure>

UI 审核所需的操作取决于失败的案例：

* 组件库应由设计部门审查，这是通过在 GitHub 中请求审查 `bitwarden/dept-design` 来完成的。
* 其他更改应由审查开发人员审查。

可以通过单击 **UI 审查**检查来访问审核。

<figure><img src="https://raw.githubusercontent.com/bitwarden/contributing-docs/master/docs/contributing/pull-requests/publish-review.png" alt=""><figcaption></figcaption></figure>

还可以通过单击 **Storybook Publish** 检查来浏览 Storybook 中的拉取请求。
