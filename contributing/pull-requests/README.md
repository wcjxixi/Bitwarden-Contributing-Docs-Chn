# 拉取请求

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/pull-requests/)
{% endhint %}

Pull Requests（拉取请求）是我们用来编写软件的主要机制。GitHub 有一些关于使用 Pull Request 功能的精彩[文档](https://docs.github.com/en/pull-requests/collaborating-with-pull-requests/proposing-changes-to-your-work-with-pull-requests/about-pull-requests)。

## 创建分支 <a href="#fork" id="fork"></a>

为了向 Bitwarden 做出贡献，您需要创建存储库分支。有关如何执行此操作的详细信息，请参阅 [GitHub 上的这篇帮助文章](https://docs.github.com/en/get-started/quickstart/fork-a-repo)。创建存储库分支后，您需要将其克隆到本地。

```bash
# Example for the clients repository
git clone git@github.com:username/clients.git
```

添加指向官方 Bitwarden 存储库的 `upstream` 远程也很有用。

```bash
# Example for the clients repository, from the repository directory
git remote add upstream https://github.com/bitwarden/clients.git
```

这将使您可以通过运行轻松引入上游更改。

```bash
# Example for the clients repository, from the repository directory
git fetch upstream
```

## 分支 <a href="#branch" id="branch"></a>

每个新功能或错误修复都应该在单独的分支上开发。分支允许您同时处理多个功能。在大多数情况下，您应该从 `master` 分支。但是，如果您与其他贡献者合作，我们通常会分支出一个长期存在的功能分支。长期存在的功能分支允许我们将单个功能分解为多个 PR，这些 PR 可以单独审查，但可以一起测试和发布。

作为社区贡献者，您可以使用以下命令直接从上游主分支进行分支。

```bash
git checkout -b feature/example
```

## 提交 <a href="#commit" id="commit"></a>

我们建议将相关更改分组到单个提交中。这可以使审阅者更容易理解和评估所提议的更改，同时还可以为贡献者提供检查点，以便在出现问题时可以恢复。

我们没有关于如何构建提交消息（例如语义提交消息）的标准。我们鼓励提交消息应在 50 个字符的限制内，以便可以轻松使用 `git log` 。如果提交消息需要超过 50 个字符，最好将其分解为更小的原子更改，以提高 git 历史记录的可读性和可延展性（还原、挑选等）。

更高级的贡献者可能会发现[重写历史](https://git-scm.com/book/en/v2/Git-Tools-Rewriting-History)很有用。这允许贡献者在推送到远程存储库之前修改其本地历史记录。一个常见的用例是压缩多个半工作提交。请务必遵循强制推送建议。

## 创建拉取请求 <a href="#creating-a-pull-request" id="creating-a-pull-request"></a>

Bitwarden 存储库有一个应遵循的 _Pull Request_ 模板。这将确保 PR 审核顺利进行，因为它将为审核者提供背景信息。创建社区 PR 后，它们将自动链接到内部 Jira 票证。内部票证用于确定优先级和跟踪目的。将 `@dept-design` 标记为任何 UI 更改的审阅者。

## 审查流程 <a href="#review-process" id="review-process"></a>

创建社区 PR 后，Bitwarden 开发人员将执行代码审查，虽然我们会在合理的时间范围内尝试执行此操作，但请理解，我们的内部路线图和优先级可能会延迟此过程。

我们编写了一些[代码审查指南](code-review.md)，建议您在执行第一次代码审查之前阅读这些指南。
