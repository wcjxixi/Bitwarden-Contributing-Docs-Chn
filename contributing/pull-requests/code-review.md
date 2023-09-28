# 代码审查指南

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/pull-requests/code-review)
{% endhint %}

在 Bitwarden，我们鼓励每个人都参与代码审查。团队将主要关注他们自己的代码审查，但如果您看到一些有趣的内容，请随时加入并讨论。

为了进行高效的代码审查，需要记住以下几点（来自 [Best Practices for Code Review | SmartBear](https://smartbear.com/learn/code-review/best-practices-for-peer-code-review/)）：

* 拉取请求应该是可管理的。如果 PR 太大（明显超过几百行），请询问贡献者是否可以在审查之前将其拆分为多个 PR。
  * 这在我们的代码库中可能很棘手，因为许多事情都是紧密耦合的。
* 审查时请花些时间 - 预计每小时代码量少于 500 行。
* 休息一下——审查时间不要超过 60 分钟。

不要因为花时间进行代码审查而感到难过！它们通常花费的时间比您想象的要长，我们应该根据需要花费尽可能多的时间。

{% hint style="success" %}
在开发周期早期发现的错误或缺陷，其修复成本要小得多。
{% endhint %}

## 审查 <a href="#reviewing" id="reviewing"></a>

如果您认为其他人对您正在审查的代码非常了解，请随时与他们联系或将他们添加为审查者。

请不要批准您不理解其含义的代码。随时欢迎提出意见和关注！例如，可以留下一些一般性评论或反馈，同时也表示您没有足够的知识来批准更改。作者可以要求其他人再次审查，并且 PR 上有两个审查者并没有什么问题。

{% hint style="info" %}
在审查代码时，请记住所有软件都是为了符合一组假设而构建的。功能、错误修复和其他需求更改代表了这些假设的变化。合并后的代码应该代表满足新需求集的最佳解决方案，这可能不一定与以前的解决方案一致。
{% endhint %}

### 审查状态 <a href="#review-statuses" id="review-statuses"></a>

请正确使用审查状态。

#### 评论 <a href="#comment" id="comment"></a>

评论是讨论事物而无需明确批准或请求更改的好方法。

#### 请求更改 <a href="#request-changes" id="request-changes"></a>

当您认为在合并 PR 之前需要更改某些内容时，应使用请求更改，因为这会阻止其他人在解决您的问题之前批准 PR。

我们应该毫不犹豫地使用此状态，但是我们应该就 PR 获得批准需要进行哪些更改提供明确的反馈。同样，PR 作者不应因更改请求而气馁，这只是表明应在合并 PR 之前进行更改。这很常见。

{% hint style="warning" %}
虽然可以放弃审查，但应谨慎使用。只要有可能，请先联系审查者，以确保他们的疑虑得到解决。以下是一些通常认为放弃审查的情况。

* 审查者离开办公室的时间较长，他们最初的反馈已得到解决。新审查者有责任确保原始反馈得到解决。
* PR 是一个需要快速部署的修补程序，并且审查者处于不同的时区。如果反馈尚未得到解决，则可以在后续 PR 中解决。
{% endhint %}

#### 批准 <a href="#approve" id="approve"></a>

批准 PR 意味着您对代码的工作原理以及它执行 PR 所声称的功能有信心。这可以基于测试更改或以前的领域知识。

* PR 针对正确的分支。
* 您已验证链接的 Jira 问题描述是否与 PR 中所做的更改匹配。
* 您已阅读并理解 PR 建议的更改的全部影响。
* 您证明这些变化
  * 解决预期的问题，
  * 以最好的方式解决需求，
  * 代码结构良好，
  * 遵循我们最新的、公认的模式，
  * 并且没有意外的副作用。

## 审查技巧 <a href="#reviewing-techniques" id="reviewing-techniques"></a>

### 多个重点领域 <a href="#multiple-focus-areas" id="multiple-focus-areas"></a>

### GitHub 功能 <a href="#github-features" id="github-features"></a>

### 本地运行 <a href="#running-locally" id="running-locally"></a>
