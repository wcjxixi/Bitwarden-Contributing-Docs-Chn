# 0010 - Angular Modules

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/angular-ngmodules)
{% endhint %}

| ID：  | ADR-0010   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-07-25 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

> NgModule 是一个内聚代码块的容器，专用于某个应用程序域、工作流程或密切相关的功能集。它们可以包含组件、服务提供商和其他代码文件，其范围由包含的 NgModule 定义。它们可以导入从其他 NgModule 导出的功能，并导出选定的功能以供其他 NgModule 使用。
>
> \-- [_https://angular.io/guide/architecture-modules_](https://angular.io/guide/architecture-modules)

在 ADR [0002 Define public module in NPM packages](https://contributing.bitwarden.com/architecture/adr/public-module-npm-packages)，我们决定开始使用桶形文件，并限制从其他“模块”导入。这解决了我们遇到的一些痛点，但由于所有组件都需要在 Angular 模块中定义，因此它们仍在桶形文件中导出。这就限制了桶形文件的实用性。

Angular 鼓励创建许多小型 NgModule，人们提倡每个功能一个模块，或者甚至每个组件定义一个模块。在 Angular v14 中，由于引入了[独立组件](https://angular.io/guide/standalone-components)，这变得更加容易。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **什么都不做** -- 维持现状，在单一模块中定义大多数组件。这意味着每个组件本质上仍然是全局的。
* [**Angular 模块**](https://angular.io/guide/architecture-modules) -- 在我们的桶形文件旁边添加 NgModules。这样可以对内部组件进行适当的封装。
* [**独立组件**](https://angular.io/guide/standalone-components) -- 提供 NgModule 的优点，而无需大部分额外的样板。目前仍处于预览阶段，因此依赖它有一定风险。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**Angular 模块**。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 内部组件不能在功能之外重复使用。
* 需要模块来支持延迟加载。
* 模块结构与独立组件类似，如果认为有必要，将来可以轻松迁移。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* Angular 对 NgModules 的错误处理非常糟糕，提供了难以调试的隐含错误。
* 额外的样板。

## 指南​ <a href="#guidelines" id="guidelines"></a>

* 旨在导出尽可能少的组件。在许多情况下，您根本不需要导出任何组件，而是可以将路由封装在该模块中，这样就可以在认为有用的情况下延迟加载它。
* 需要在所有模块之间共享的功能应放置在 `Shared` 功能中。
* 如果需要在个人密码库和组织密码库等模块之间共享附加功能，请考虑创建 `feature/shared` 模块。

### 实施 <a href="#implementation" id="implementation"></a>

功能模块的一个例子是**报告**。我们知道报告既可用于个人用户，也可用于组织。

```
reports
  shared
    report-card.component
    report-list.component

    reports-shared.module.ts
    index.ts
  reports
    breach-report.component
    ...
  reports.module.ts -> depends on reports-shared.module.ts
  reports.component.ts
  index.ts

organizations
  reports
    organization-reports.module.ts -> depends on reports-shared.module.ts
```
