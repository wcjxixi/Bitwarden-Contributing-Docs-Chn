# Angular

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/clients/presentation/angular)
{% endhint %}

对于 Bitwarden，我们使用 Angular 作为我们的客户端框架。在继续阅读之前建议对 Angular 有一个基本的了解，如果不确定请参考官方 [Angular 文档](https://angular.io/docs)或 [Angular 入门](https://angular.io/start)。我们也尽力遵循 [Angular 风格指南](https://angular.io/guide/styleguide)。

本文档旨在涵盖我们所遵循的最佳实践。它们中的大部分最初是基于不同的 ADR，虽然尽管 ADR 擅长描述原因，但它们提供了次优的阅读体验。

## 命名 ([ADR-0012](../adr/0012-angular-filename-convention.md)) <a href="#naming-adr-0012" id="naming-adr-0012"></a>

我们遵循 Angular 风格指南中的[命名](https://angular.io/guide/styleguide#naming)部分。更具体地说，使用破折号来分隔描述性名称中的单词，并使用点来分隔名称和类型。

我们使用以下由 [Style-02-01](https://angular.io/guide/styleguide#general-naming-guidelines) 建议的常规后缀：

* `service` - 服务（在 Bitwarden，此类型表示抽象服务）
* `component` - Angular 组件
* `pipe` - Angular 管道
* `module` - Angular 模块
* `directive` - Angular 指令

在 Bitwarden，我们还使用了更多的类型：

* `.api` - API 模型
* `.data` - 数据模型（用于序列化域模型）
* `.view` - 视图模型（解密域模型）
* `.export` - 导出模型
* `.request` - API 请求
* `.response` - API 响应
* `.type` - 枚举
* `.service.implementation` - 抽象服务的实现

类名也应使用后缀作为其类名的一部分。例如，服务实现被命名为 `FolderServiceImplementation`，请求模型被命名为 `FolderRequest`。

由于摘要比实现更频繁地被引用，它们使用简化类型，而实现必须指定它们是实现，例如，`.service` 用于摘要，`.service.implementation` 用于植入。

## 可观察对象 ([ADR-0003](../adr/0003-adopt-observable-data-services-for-angular.md)) <a href="#observables-adr-0003" id="observables-adr-0003"></a>

## 响应式表单 ([ADR-0001](../adr/reactive-forms.md)) <a href="#reactive-forms-adr-0001" id="reactive-forms-adr-0001"></a>

## 轻量化组件 <a href="#thin-components" id="thin-components"></a>

### 组合优于继承 ([ADR-0009](../adr/0009-composition-over-inheritance.md)) <a href="#composition-over-inheritance-adr-0009" id="composition-over-inheritance-adr-0009"></a>
