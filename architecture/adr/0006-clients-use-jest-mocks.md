# 0006 - Clients: Use Jest Mocks

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/clients-use-jest-mocks)
{% endhint %}

| ID：  | ADR-0006   |
| ---- | ---------- |
| 状态：  | 完成         |
| 发表于： | 2022-07-18 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

我们目前使用 [Substitute](https://www.npmjs.com/package/@fluffy-spoon/substitute) 在测试中创建模拟。这包括匹配参数、模拟返回值和监视调用。不过，我们也使用 Jest，它带有自己的模拟、监视和匹配功能。这意味着我们有两个提供相同功能的库。

这是不受欢迎的，因为：

* 我们在两个竞争库之间存在重复的功能
* 目前尚不清楚团队应该使用哪个库
* 熟悉 Jest 的人自然会使用 Jest 进行模拟，这不是我们目前遵循的模式
* 与 Jest 相比，Substitute 的知名度和使用率较低，是另一个需要学习的库
* 它要求我们混合语法，特别是对于断言：我们使用 Jest 的 `expect` 来断言测试结果，但我们使用 Substitute 的 `Arg` 来匹配模拟调用中的参数

我们应该只选其一。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **使用 Substitute 并禁止开发人员使用 Jest 模拟** -- 我们保留这两个库并继续同时使用它们。在这种情况下，我们应该为开发人员提供明确的指导和培训，让他们在所有模拟和参数匹配中使用 Substitute（而不是 Jest）。
* **弃用 Substitute，改用带有 jest-mock-extend 的 Jest** -- 我们弃用 Substitute，并更新所有现有测试以改用 Jest 模拟。我们使用 [jest-mock-extended](https://github.com/marchaos/jest-mock-extended/)，以方便的模拟语法并更好地与 Typescript 集成。此更改不应导致任何功能损失。
* **使用不同的模拟库和/或测试运行器** -- 我们完全更改为其他东西。这实际上并没有摆在桌面上，但也是一种选择。Jest 到目前为止运行良好，所以我不建议我们更换它。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**弃用 Substitute，改用带有 jest-mock-extend 的 Jest**

我们还应该举办培训/学习会议，以鼓励并授权开发人员对其代码进行单元测试。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 相比 Substitute，前端开发人员可能更熟悉 Jest
* Jest 有更多的文档和资源（Medium 文章、StackOverflow 答案等）
* 它是 Jest 的一个组成部分
* 不需要混合不同的库或语法

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 与 Substitute（NSubstitute 的 .NET 移植版本）相比，后端开发人员的学习曲线会更长。然而，这仍然不是一个特别陡峭的学习曲线，并且在前端环境中使用前端工具是有意义的。
* 需要对现有测试进行一些改动，但改动不大。
