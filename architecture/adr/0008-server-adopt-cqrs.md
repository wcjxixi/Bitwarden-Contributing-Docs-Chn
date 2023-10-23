# 0008 - Server: Adopt CQRS

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/server-CQRS-pattern)
{% endhint %}

| ID：  | ADR-0008   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-07-15 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

在 Bitwarden Server 中，我们目前使用 `<<Entity>>Service` 模式来作用于我们的实体。这些类最终成为了涉及实体的所有操作的垃圾场：导致[臃肿](https://refactoring.guru/refactoring/smells/bloaters)和[耦合](https://refactoring.guru/refactoring/smells/couplers)。有两个事实帮助我们确定了当前的设计：

* 我们使用实体模式来表示存储在数据库中的数据，并使用 Dapper 或实体框架自动绑定这些实体类。
* 我们使用基于构造函数的依赖注入来将依赖项传递给对象。

上述两个事实意味着，如果不接收所有必要的状态作为方法参数，我们的实体就无法运行，这与我们典型的 DI 模式背道而驰。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **`<<Entity>>Services`** -- 上面讨论过了
* **查询和命令** -- 从根本上来说，我们的问题是 `<<Entity>>Service` 名称完全封装了您可以对该实体执行的任何操作，并且排除了不同实体之间的任何代码重用。 CQRS 模式根据对实体采取的操作创建类。这就自然而然地限制了类的范围，并在两个实体需要实现相同的命令行为时允许重复使用。[https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs](https://docs.microsoft.com/en-us/azure/architecture/patterns/cqrs)
* **基于功能的小型服务** -- 这种设计会将 `<<Entity>>Service` 分解为 `<<Feature>>Service` ，但最终会遇到同样的问题。随着功能的增加，该服务将变得臃肿，并与其他服务紧密耦合。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**查询和命令**

对于现任者来说，命令似乎是更好的决定。我们获得了代码重用并限制了类的范围。此外，我们还拥有一条迭代路径，可以通过队列工作实现完整的 CQRS 管道。

查询基本上已经通过存储库和/或服务完成，但需要进行一些明显的重组。

## 过渡计划​ <a href="#transition-plan" id="transition-plan"></a>

随着时间的推移，我们将逐渐过渡到 CQRS 模式。如果开发人员正在进行使用或影响服务方法的更改，他们应该考虑是否可以将其提取到查询/命令中，并将其作为技术债务包含在他们的工作中。

当前的领域服务规模庞大且相互依赖，一次性将它们全部分解可能不太现实。重构「更深一层」并保留其他方法是可以接受的。这可能会导致新的查询/命令仍然在某种程度上与其他服务方法耦合。在过渡阶段，这种情况是可以接受的，但随着时间的推移，这些相互依赖关系应该被移除。
