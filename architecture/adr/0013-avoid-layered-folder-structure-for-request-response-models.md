# 0013 - Avoid layered folder structure for request/response models

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/avoid-layered-folder-structure)
{% endhint %}

| ID：  | ADR-0013   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-09-16 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

我们的 Angular 应用程序目前部分使用分层文件夹结构。这会导致域上下文被多个文件夹分割成多个层次。由于修改服务往往需要修改属于该服务的模型，这就造成了摩擦。

可以理解的是，我们的应用程序使用了大量模型，但是模型中最大且最孤立的部分是请求和响应。这使它们成为一个很好的起点。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **保持原样** -- 我们可以保持原样，继续将模型放在 `libs/common/models` 中。
* **将模型放在其所有者旁边** -- 请求和响应由单个 API 服务拥有。因此，它们可以放置在靠近其服务的位置，从而使连接的变更彼此接近。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

使用 **将模型放在其所有者旁边** 作为请求和响应模型。

一个经验法则是将抽象中使用的任何模型放在抽象目录中。而服务中使用的任何模型都放在服务目录中。抽象是服务公共接口的一部分，而服务是内部接口的一部分。

### 示例​ <a href="#example" id="example"></a>

```
libs/common/
  abstractions/folder/
    folder.service.abstraction.ts
    folder-api.service.abstraction.ts
    responses/
      folder.response.ts  (Exposed as public API)
  services/folder/
    folder.service.ts
    folder-api.service.ts
    requests/
      folder.request.ts  (Internal, only used within the implementation)
```
