# 架构决策记录

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/)
{% endhint %}

> 架构决策记录：Architectural Decision Records，简称 ADR。

[架构决策 (AD)](https://en.wikipedia.org/wiki/Architectural_decision) 是一种软件设计选择，用于解决在架构上具有重要意义的功能性或非功能性需求。例如，对于实例，这可能是技术选择（例如 Java 与 JavaScript）、IDE 的选择（例如 IntelliJ 与 Eclipse IDE）、库之间的选择（例如 [SLF4J](https://www.slf4j.org/) 与 [java.util.logging](https://docs.oracle.com/javase/8/docs/api/java/util/logging/package-summary.html)），或功能上的决策（例如，无限撤消与有限撤消）。

## @Bitwarden

在 Bitwarden 的工程团队中引入架构决策 (AD) 的目的是引导开发朝着可维护和可扩展的代码库方向发展。同时努力确保所有团队的一致性。

AD 还将作为提议和规划技术债务的基础。

### 状态定义 <a href="#status-definition" id="status-definition"></a>

* **进行中** - ADR 已获批准，我们正在整个项目中采用它。
* **标准** - ADR 已实施并假定为标准。
* **已放弃** - ADR 已被放弃，和/或被另一个 ADR 取代。

### 标签 <a href="#tags" id="tags"></a>

请确保每个 ADR 都包含一个标签，标记它们适用于哪些项目（_客户端_、_移动&#x7AEF;_&#x548C;/&#x6216;_&#x670D;务器_）。如果需要，请随意创建更多标签。

## 流程 <a href="#process" id="process"></a>

虽然流程最初主要是为发起者讨论架构决策而创建的，但对我们来说保持流程对任何人的建议开放是很重要的。为此，任何人都可以自由地打开 PR 来建议 AD。然后将讨论这些建议，以便在采纳之前在发起者之间达成普遍共识。

## ADR <a href="#adrs" id="adrs"></a>

<table data-card-size="large" data-view="cards"><thead><tr><th></th><th data-hidden data-card-target data-type="content-ref"></th></tr></thead><tbody><tr><td>0001 - Angular Reactive Forms</td><td><a href="0001-angular-reactive-forms.md">0001-angular-reactive-forms.md</a></td></tr><tr><td>0002 - Public API for modules</td><td><a href="0002-public-api-for-modules.md">0002-public-api-for-modules.md</a></td></tr><tr><td>0003 - Adopt Observable Data Services for Angular</td><td><a href="0003-adopt-observable-data-services-for-angular.md">0003-adopt-observable-data-services-for-angular.md</a></td></tr><tr><td>0004 - Refactor State Service</td><td><a href="0004-refactor-state-service.md">0004-refactor-state-service.md</a></td></tr><tr><td>0005 - Refactor Api Service</td><td><a href="0005-refactor-api-service.md">0005-refactor-api-service.md</a></td></tr><tr><td>0006 - Clients: Use Jest Mocks</td><td><a href="0006-clients-use-jest-mocks.md">0006-clients-use-jest-mocks.md</a></td></tr><tr><td>0007 - Manifest V3 sync Observables</td><td><a href="0007-manifest-v3-sync-observables.md">0007-manifest-v3-sync-observables.md</a></td></tr><tr><td>0008 - Server: Adopt CQRS</td><td><a href="0008-server-adopt-cqrs.md">0008-server-adopt-cqrs.md</a></td></tr><tr><td>0009 - Composition over inheritance</td><td><a href="0009-composition-over-inheritance.md">0009-composition-over-inheritance.md</a></td></tr><tr><td>0010 - Angular Modules</td><td><a href="0010-angular-modules.md">0010-angular-modules.md</a></td></tr><tr><td>0011 - Angular Clients folder structure</td><td><a href="0011-scalable-angular-clients-folder-structure.md">0011-scalable-angular-clients-folder-structure.md</a></td></tr><tr><td>0012 - Angular Filename convention</td><td><a href="0012-angular-filename-convention.md">0012-angular-filename-convention.md</a></td></tr><tr><td>0013 - Avoid layered folder structure</td><td><a href="0013-avoid-layered-folder-structure-for-request-response-models.md">0013-avoid-layered-folder-structure-for-request-response-models.md</a></td></tr><tr><td>0014 - Adopt Typescript Strict flag</td><td><a href="0014-adopt-typescript-strict-flag.md">0014-adopt-typescript-strict-flag.md</a></td></tr><tr><td>0015 - Short Lived Browser Services</td><td><a href="0015-short-lived-browser-services.md">0015-short-lived-browser-services.md</a></td></tr><tr><td>0016 - Move Decryption and Encryption to Views</td><td><a href="0016-move-decryption-and-encryption-to-views.md">0016-move-decryption-and-encryption-to-views.md</a></td></tr><tr><td>0017 - Use Swift to build watchOS app</td><td><a href="0017-use-swift-to-build-watchos-app.md">0017-use-swift-to-build-watchos-app.md</a></td></tr><tr><td>0018 - Feature management</td><td><a href="0018-feature-management.md">0018-feature-management.md</a></td></tr><tr><td>0019 - Adoption of Web Push</td><td><a href="0019-adoption-of-web-push.md">0019-adoption-of-web-push.md</a></td></tr><tr><td>0020 - Observability with OpenTelemetry</td><td><a href="0020-observability-with-opentelemetry.md">0020-observability-with-opentelemetry.md</a></td></tr><tr><td>0021 - Logging to Standard Output</td><td><a href="0021-logging-to-standard-output.md">0021-logging-to-standard-output.md</a></td></tr></tbody></table>
