# =数据模型

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/sdk/data-models)
{% endhint %}

SDK 预期将在各种项目中使用，为此它定义了一个稳定的公共接口。必须注意避免暴露 SDK 的内部工作原理，包括其数据模型。

## 公共接口[​](https://contributing.bitwarden.com/architecture/sdk/data-models#public-interface) <a href="#public-interface" id="public-interface"></a>

SDK 的公共接口是任何对外公开的接口，无论是函数名、类型还是公开的模型。SDK 通常会公开请求和响应模型，其中包含每次 API 调用的预期数据。

我们通常可以将公共模型分为以下几类：

* **视图模型**：通常表示解密状态的模型，例如 `CipherView`、`CipherListView`、`FolderView` 等。
* **请求模型**：用于向 SDK 发送数据的模型。一些示例包括 `ProjectGetRequest`、`ProjectCreateRequest` 等。
* **响应模型**：SDK 返回的数据，例如 `ProjectResponse`。

## 内部模型[​](https://contributing.bitwarden.com/architecture/sdk/data-models#internal-models) <a href="#internal-models" id="internal-models"></a>

SDK 还维护内部模型：

* **API 模型**：[自动生成的模型](server-bindings.md) ，用于与服务器交互。
* **领域模型**：用于在 SDK 中表示特定关注点的通用数据模型。例如 `Cipher`、`Folder` 等。
* **DTO**：数据传输对象 (DTO) 用于在 SDK 的各个层级之间传输数据。它们通常用于解耦领域模型和 API 模型。

