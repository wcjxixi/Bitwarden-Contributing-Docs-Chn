# 0005 - Refactor Api Service

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/refactor-api-service)
{% endhint %}

| ID：  | ADR-0005   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-07-08 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

`ApiService` 目前负责处理所有 API 请求。这导致该类演变成了一个 Bloater，目前有 **2021** 行代码，**268** 个方法。此外，由于它知道与服务器相关的所有内容，因此还需要导入每个请求和响应，这需要将 `ApiService` 和请求/响应放在同一个 npm 包中。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **提取类** - 我们应该使用提取类重构来分解类，其中每个域上下文应该有自己的 API 服务。 `ApiService` 应被转换为通用服务，该服务不关心请求或响应是什么，只能在其他 API 服务中使用。
* **什么也不做** - 保持原样。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**提取类**

这些新类的命名应该表示为 `{Domain}ApiService` ，例如文件夹域应该命名为 `FolderApiService` 。

重构示例：

* [`folder-api.service.ts`](https://github.com/bitwarden/clients/pull/3011/files#diff-11b3488b9977f06625349680f81554505613715cfcc9890ebb356a74579c236a) ：创建一个新的服务，将方法从 `ApiService` 移动到新服务。在此重构期间，我们还将服务器知识从 `FolderService` 中移出，因为它应该只负责维护其状态。
* 从 [`ApiService`](https://github.com/bitwarden/clients/pull/3011/files#diff-6c8f3163b688c01f589d1e9ee5b7998aea4a0aedde8333c3939fb6181c301bed) 中删除旧方法。
