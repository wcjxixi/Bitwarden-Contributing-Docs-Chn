# 0011 - Scalable Angular Clients folder structure

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/angular-folder-structure)
{% endhint %}

| ID：  | ADR-0011   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-07-25 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

目前，我们的 Angular 客户端中的文件夹结构非常分散，具有多个相互竞争的文件夹结构。我们需要对单一文件夹结构进行标准化，这将使我们能够继续发展，而不会产生摩擦。本 ADR 以  [0010 Use Angular Modules](https://contributing.bitwarden.com/architecture/adr/angular-ngmodules) 为基础，提出了一种文件夹结构。

### 资源​ <a href="#resources" id="resources"></a>

这在很大程度上基于以下资源：

* [Angular - NgModules](https://angular.io/guide/ngmodules)
* [Angular - Application structure and NgModules](https://angular.io/guide/styleguide#application-structure-and-ngmodules)
* [Angular - Lazy-loading feature modules](https://angular.io/guide/lazy-loading-ngmodules)
* [Using Nx at Enterprises](https://nx.dev/guides/monorepo-nx-enterprise)

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **Nx 文件夹结构** -- 看起来是一个经过深思熟虑的结构，但它本质上要求我们使用 nx，因为它严重依赖于 npm 包。我们还没有达到可以轻松采用这种工具的阶段。
* **受 Angular Docs 启发的轻量级结构** -- 从 Angular docs + Nx 中汲取灵感，提出了一种更轻量级的结构，仍然保留 `app` 目录中的功能。

### `SharedModule` & `CoreModule` <a href="#sharedmodule--coremodule" id="sharedmodule--coremodule"></a>

[Angular 文档](https://angular.io/guide/module-types#shared-ngmodules)规定应该有一个 `SharedModule`。它进一步指定共享模块不应提供提供程序。常见的做法是创建一个 `CoreModule` 来负责设置核心提供程序。

### 建议的文件夹结构​ <a href="#proposed-folder-structure" id="proposed-folder-structure"></a>

该文件夹结构基于我们现有的路由结构，因为常见的模式是使用模块进行嵌套路由以支持延迟加载。根文件夹主要基于我们拥有的根路由概念，各种公共路由被分类在 `accounts` 下。

```
web/src/app
  core
    services
    core.module.ts
    index.ts
  shared
    shared.module.ts
    index.ts

  accounts
  providers
  reports
  sends
  settings
  tools
  vault
    shared
      vault-shared.module.ts (this gets imported by Organization Vaults)
    vault.module.ts
    index.ts (exposes the following files:)
      vault-shared.module.ts
      vault.module.ts
  --
  app.component.html
  app.component.ts
  app.module
  oss-routing.module.ts
  oss.module.ts
  wildcard-routing.module.ts
```

该结构将分多个步骤实施：

1. 从 `src/app` 中提取不相关的文件。[https://github.com/bitwarden/clients/pull/3127](https://github.com/bitwarden/clients/pull/3127)
2. 创建 `CoreModule`。[https://github.com/bitwarden/clients/pull/3149](https://github.com/bitwarden/clients/pull/3149)
3. 创建 `SharedModule`。[https://github.com/bitwarden/clients/pull/3222](https://github.com/bitwarden/clients/pull/3222)
4. 将所有现有的松散组件迁移到 `SharedModule`
   * 根中的任何剩余功能都应该提供一个模块

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

以上述示例为蓝本实现文件结构。
