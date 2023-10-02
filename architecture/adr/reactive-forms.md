# 0001 - Angular Reactive Forms

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/reactive-forms)
{% endhint %}

| ID:  | ADR-0001   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-05-28 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

我们的 Angular 应用程序中的大多数表单都使用模板驱动的表单。最近我们注意到扩展和维护这些表单的问题。并开始混合使用模板驱动的表单和反应式表单。

维护两种处理表单的方法很复杂，完全转变为单一方法将确保开发人员和用户获得更一致的体验。

## 考虑的选项​ <a href="#considered-options" id="considered-options"></a>

* **反应式表单** - 提供对底层表单对象模型的直接、显式访问。与模板驱动的表单相比，它们更加健壮：它们更具可扩展性、可重用性和可测试性。如果表单是应用程序的关键部分，或者您已经使用反应式模式来构建应用程序，请使用反应式表单。
* **模板驱动的表单** - 依靠模板中的指令来创建和操作底层对象模型。它们对于向应用程序添加简单的表单非常有用，例如电子邮件列表注册表单。它们可以直接添加到应用程序中，但它们的扩展性不如反应式表单。如果您有非常基本的表单需求，而且逻辑只需在模板中进行管理，那么模板驱动的表单可能非常适合您。

来源：[https://angular.io/guide/forms-overview#choosing-an-approach](https://angular.io/guide/forms-overview#choosing-an-approach)

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的选项：**反应式表单**，因为我们的需求超出了模板驱动表单的推荐范围。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 您永远不需要考虑使用哪种表单方法。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 仅使用反应式表单式意味着我们可能需要一些额外的模板。
