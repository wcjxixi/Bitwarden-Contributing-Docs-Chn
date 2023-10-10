# 拉取请求

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/pull-requests/)
{% endhint %}

Pull Requests（拉取请求）是我们用来编写软件的主要机制。GitHub 有一些关于使用 Pull Request 功能的精彩[文档](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)。

## 分支 <a href="#branch" id="branch"></a>

每个新功能或错误修复都应该在单独的分支上开发。分支允许您同时处理多个功能。在大多数情况下，您应该从 `master` 分支。但是，如果您与其他贡献者合作，我们通常会分支出一个长期存在的功能分支。长期存在的功能分支允许我们将单个功能分解为多个 PR，这些 PR 可以单独审查，但可以一起测试和发布。

作为 Bitwarden 贡献者，您应该分支 `origin/master` ，这确保分支始终基于最新的上游 `master` 即使本地 `master` 已过时。

```bash
git checkout -b <team>/<issue-number>/<brief-description> -t origin/master
```

[这里](branching.md)详细描述了我们的分支策略。

## 提交 <a href="#commit" id="commit"></a>

我们建议将相关更改分组到单个提交中。这可以使审阅者更容易理解和评估所提议的更改，同时还可以为贡献者提供检查点，以便在出现问题时可以恢复。

我们没有关于如何构建提交消息（例如语义提交消息）的标准。我们鼓励提交消息应在 50 个字符的限制内，以便可以轻松使用 `git log` 。如果提交消息需要超过 50 个字符，最好将其分解为更小的原子更改，以提高 git 历史记录的可读性和可延展性（还原、挑选等）。

更高级的贡献者可能会发现[重写历史](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)很有用。这允许贡献者在推送到远程存储库之前修改其本地历史记录。一个常见的用例是压缩多个半工作提交。请务必遵循强制推送建议。

{% hint style="danger" %}
PR 被审查后，就应**避免强制推送**。

影响现有 git 提交的 Git 操作会阻止 GitHub 正确识别 PR 的「新的更改」，迫使审阅者重新开始。
{% endhint %}

## 创建拉取请求 <a href="#creating-a-pull-request" id="creating-a-pull-request"></a>

Bitwarden 存储库有一个应遵循的 _Pull Request_ 模板。这将确保 PR 审核顺利进行，因为它将为审核者提供背景信息。创建社区 PR 后，它们将自动链接到内部 Jira 票证。内部票证用于确定优先级和跟踪目的。将 `@dept-design` 标记为任何 UI 更改的审阅者。

## 审查流程 <a href="#review-process" id="review-process"></a>

虽然我们主要使用异步审查流程，但请随时安排与审查者/贡献者的会议来讨论更改。虽然异步通信很有用，但它会带来时间损失，从而拖延审查过程。有时，召开简短的电话会议来讨论更改可能会节省大量时间。

我们编写了一些[代码审查指南](code-review.md)，建议您在执行第一次代码审查之前阅读这些指南。
