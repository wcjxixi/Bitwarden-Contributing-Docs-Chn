# 0016 - Move Decryption and Encryption to Views

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/decryption-in-views)
{% endhint %}

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

Bitwarden 有几个不同的模型来表示数据，[数据模型](../clients/data-model.md)中详细描述了这些数据。本 ADR 中，我们将重点关注以下两种模型：

* `<Domain>` - 表示已加密数据状态的域模型。
* `<Domain>View` - 表示域模型的已解密状态的视图模型。

由于我们至少有两个不同的模型来表示同一域的已加密和已解密状态，这也意味着我们需要一种在两个模型之间进行转换的方法，即加密和解密数据。

### 目前是如何做的​ <a href="#how-its-currently-being-done" id="how-its-currently-being-done"></a>

当前完成此操作的方法是让 `<Domain>Service` 公开包含解密视图的 Observable，或者使用基于 Promise 的方法来解密它。`<Domain>Service` 通常还公开一个 `encrypt` 方法，该方法从 `View` 和 `Domain` 模型进行转换。

`Domain` 模型本身通常还有一个 `decrypt` 方法，用于执行实际的解密逻辑。它通过在 `EncString` 对象上调用 `decrypt` 来实现这一点，而对象又依赖于全局容器服务来检索 `CryptoService` 和 `EncryptService` 执行实际操作。

### 存在的问题​ <a href="#the-problems" id="the-problems"></a>

这种方法有几个问题：

* 域模型与视图模型紧密耦合。
* 加密和解密被分成两个不同的地方。解密直接发生在域模型上，而加密发生在服务中。从逻辑上讲，它们是紧密耦合的，并且应该彼此相邻。
* 我们依靠全局容器服务来检索 `CryptoService` 和 `EncryptService`。
* 我们当前的模型充当转型管道。`Request -> Data -> Domain -> View`。
* 将来如果有一种方法可以支持每个域的多个 `View` 模型，那就太好了。

### 为什么是现在？​ <a href="#why-now" id="why-now"></a>

目前，Secret Manager 在加密和解密的管理方式方面遇到了一些摩擦。它不遵循同步本地状态的典型模式，而是依赖于对服务器的直接请求来获取数据。然后需要对数据进行解密。

目前，此加密和解密逻辑由 `<Domain>Service` 处理，但这违反了单一责任原则。这也使得我们的服务难以跟踪，因为它现在需要了解请求、响应、加密和解密。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **将解密转移至 `<Domain>Service`** -- 我们已经为不同的域提供了服务，这些服务目前用于处理加密，因此将逻辑转移到这里是合理的。
* **将逻辑移转移至 `<Domain>View` 本身** -- 将逻辑移至 `View` 模型本身，并结合通用服务来加密和解密视图。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**将逻辑转移至 `<Domain>View` 本身**。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 域服务不再需要实现定制的加密和解密逻辑。这遵循单一责任原则。
* 域模型不再与视图紧密耦合。
* 目前，我们的每个域可以拥有多个视图。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 由于加密和解密现在是在通用 `EncryptService` 上完成的，因此可以绕过预期的流程。一个例子是 `Cipher`，`CipherService` 有一个 `updateHistoryAndEncrypt` 方法，它在加密之前计算密码历史记录。

### 实施​ <a href="#implementation" id="implementation"></a>

[文件夹 PR](https://github.com/bitwarden/clients/pull/3732) 示例。

```typescript
class FolderDomain implements DecryptableDomain {
  id: string;
  name: EncString;
  revisionDate: Date;

  keyIdentifier(): string | null {
    return null;
  }
}

class FolderView implements Encryptable<Folder> {
  id: string = null;
  name: string = null;
  revisionDate: Date = null;

  keyIdentifier(): string | null {
    return null;
  }

  async encrypt(encryptService: EncryptService, key: SymmetricCryptoKey): Promise<Folder> {
    const folder = new Folder();
    folder.id = this.id;
    folder.revisionDate = this.revisionDate;

    folder.name = this.name != null ? await encryptService.encrypt(this.name, key) : null;

    return folder;
  }

  static async decrypt(encryptService: EncryptService, key: SymmetricCryptoKey, model: Folder) {
    const view = new FolderView();
    view.id = model.id;
    view.revisionDate = model.revisionDate;

    view.name = await model.name?.decryptWithEncryptService(encryptService, key);

    return view;
  }
}
```

它将像这样被使用：

```typescript
// Fetch from server
const response: FolderResponse = await this.folderApiService.getFolder(id);
const folderData: FolderData = new FolderData(response);
const folder: Folder = new Folder(folderData);

// Decrypt / Encrypt
const folderView: FolderView = this.encryptionService.decryptView(FolderView, folder, key);
folderView.name = "New folder name";
const encryptedFolder: Folder = this.encryptionService.encryptView(folderView, key);

// Update
const request: FolderRequest = new FolderUpdateRequest(encryptedFolder);
```
