# 0009 - Composition over inheritance

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/angular-composition-over-inheritance)
{% endhint %}

| ID：  | ADR-0009   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-07-25 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

目前，我们的 Angular 应用程序严重依赖继承。虽然这在当时似乎是一个很自然的决定，因为它允许我们在不同领域之间快速共享代码。但这也导致了紧密耦合，因此难以理解变更会产生的影响。它还助长了大型页面级组件的出现。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **什么也不做** -- 维持现状，这并不是一个真正的选择。
* **优先选择组合而不是继承** -- 将组件拆分成只做一件事的小组件。保持组件精简，并主要使用_服务_来共享功能。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**优先选择组合而不是继承**

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 更轻巧的组件
* 更好地理解变更所产生的影响，因为它现在仅被隔离到单个组件

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 继承性更容易理解
