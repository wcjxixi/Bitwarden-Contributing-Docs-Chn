# \*模板库

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/template-repository)
{% endhint %}

## 位置和用途​ <a href="#location-and-usage" id="location-and-usage"></a>

私有模板库是新项目的基础文件集和整体设置，可在 GitHub 仓库创建界面中选择。它包含了拉取请求模板、inting、持续集成入门等所需的内容。一般适用于所有版本库的核心概念都应在这里创建和审查，然后再发布。该模板代表了整个公司的最佳实践，但也应被视为根据版本库需求进一步设置的起点；在许多情况下，都需要进行定制，详见下文。

## 内容和许可​ <a href="#content-and-licensing" id="content-and-licensing"></a>

文本许可证文件声明了 GPL 和 Bitwarden 专有许可证的使用途。带有 `README` 的 `bitwarden_license` 目录将用于放置 Bitwarden 许可的代码和内容，否则将指定使用 GPL。这些文件及其位置/结构不得修改。

安全和贡献文件阐明了公司的政策和方法。如果有特殊情况，可以对这些文件进行修改，但极有可能保持原样。

根目录下的 `README` 预期可以定制，但没有明确的内容规定。由于文档可以保存在其他地方，如本网站！因此建议简短地写上标题和几句话的简单描述。

## 编辑器配置​ <a href="#editor-configuration" id="editor-configuration"></a>

有一组文件为 Git 定义了预期属性和忽略属性。后者有望根据版本库的需求进行扩展，但在可能的情况下，模板本身也应针对其他用例进行扩展。流行的操作系统和 IDE 特定的忽略项已经存在。

编辑器配置为文件格式设置了规则。与上述忽略类似，模板也应根据新语言和公司标准进行更新。Linters 在执行规则时将遵从编辑器配置。

## 本地 linting <a href="#local-linting" id="local-linting"></a>

[Husky](https://github.com/typicode/husky) 和通过 NPM 进行的 [lint-staged](https://github.com/okonet/lint-staged) 用于本地 lint 和格式化已更改的文件。运行：

```bash
npm install
```

克隆您的新存储库以安装必要的 Git hook 后。

在[包配置](https://github.com/bitwarden/template/blob/main/package.json)的 `lint-staged` 部分中，存在针对特定文件类型的 linter 配置，并使用 [Prettier](https://github.com/prettier/prettier) 作为所有文件的默认格式化程序。扩展这些文件类型，以使其适用于具有可用格式化程序的相关文件类型，例如使用 .NET 应用程序：

```json
"*.cs": "dotnet format --include"
```

或 TypeScript：

```json
"*.ts": "eslint --cache --cache-strategy content --fix"
```

上面使用的编辑器配置可由许多 linter 访问，以驱动结果。

## 依赖管理​ <a href="#dependency-management" id="dependency-management"></a>

为可管理依赖更新配置。它：

* 每个包管理器将次要更改和补丁更改合并到一个汇总拉取请求中。
* 使用依赖性仪表板，我们可以看到哪些拉取请求尚未创建，但仍然可以管理工作负载。
* 通过重建、语义版本控制和锁定文件更新来管理更新。
* 以较小的拉取请求限制作为起点。
* 包括了作为单独拉取请求的主要更新（最新）。
* 将计划安排在周末，此时组织可能有更多的 Actions 工作人员。

如果存储库随着时间的推移而扩展以包含新的包管理器，建议将所有包管理器保持启用状态。更新计划以及单个存储库有多少拉取请求。可能需要例外、其他包管理器和特定于依赖项的配置。

考虑固定依赖项的[最佳实践](https://docs.renovatebot.com/dependency-pinning/#so-whats-best)（尤其是在 root），就像上面用于本地 linting 的最佳实践一样。开发依赖项（例如格式化程序和 linter）需要在所有团队之间进行沟通和协调部署，以便代码风格根据我们的标准和模板存储库本身中看到的编辑器配置保持一致。

## 议题模板​ <a href="#issue-templates" id="issue-templates"></a>

针对集中和相关链接议题的配置，以及用于创建拉取请求的模板，该模板使用几乎所有更改所需的公共部分。说明存在于拉取请求模板中，但一般来说：

* 预期会有跟踪链接，例如 GitHub 或 Jira 议题。
* 如果存储库没有用户界面，则可以移除「Screenshots」部分。
* 根据存储库上下文的需要调整提醒。

您不太可能需要在目标存储库中对模板进行大量修改，并且此处的原始模板始终可以通过组织内的其他改进进行扩展。

## 代码所有权​ <a href="#code-ownership" id="code-ownership"></a>

要定义的 CODEOWNERS 条目指示「拥有」相关路径上的代码的团队。
