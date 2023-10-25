# 0012 - Angular Filename convention

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/angular-filename-convention)
{% endhint %}

| ID：  | ADR-0012   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-08-23 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

目前，我们使用混合文件名约定，其中一些文件遵循 Angular 样式指南，而其他文件则使用 camelCase（驼峰命名法）。这导致了一些混乱，不知道该遵循哪种约定，我们应该统一一种约定，以避免混乱。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **Angular 编码风格指南** -- Angular 编码风格指南规定「使用点和破折号分隔文件名」。
* **camelCase** -- 长期以来，我们一直习惯使用 camelCase。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

使用 [**Angular 编码风样式指南**](https://angular.io/guide/styleguide#naming)。更具体地说是[样式 02-02](https://angular.io/guide/styleguide#style-02-02) 和[样式 02-03](https://angular.io/guide/styleguide#style-02-03)。

这两个样式规则侧重于使用破折号来分隔描述性名称中的单词，以及使用点来分隔类型中的名称。 Angular 通常有以下类型：

* `service` - 抽象服务
* `component` - Angular 组件
* `pipe` - Angular 管道
* `module` - Angular 模块
* `directive` - Angular 指令

在 Bitwarden，我们还使用了更多其他类型：

* `.api` - API 模型
* `.data` - 数据模型（用于序列化域名模型）
* `.view` - 视图模型（已解密的域名模型）
* `.export` - 导出模型
* `.request` - API 请求
* `.response` - API 响应
* `.type` - 枚举
* `.service.abstraction` - 服务的抽象类，用于 DI，并非所有服务都需要抽象类

类名也应使用后缀作为其类名的一部分。例如，服务实现将被命名为 `FolderService`，请求模型将被命名为 `FolderRequest`。

如果服务无法完全实现，则会创建带有 `Abstraction` 后缀的抽象类。如果 Angular 和 Node 实现由于某种原因必须有所不同，通常会发生这种情况。传统上会使用接口，但在 JavaScript 中，TypeScript 接口不能用于连接依赖注入。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 由于我们的大部分代码都是用 Angular 编写的，因此我们应该使用 Angular 编码风格指南。
* 不使用 camelCase 将避免与大小写敏感的文件系统发生问题。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 我们需要更新大量文件才能保持一致。
