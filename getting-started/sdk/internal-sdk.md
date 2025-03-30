# =内部 SDK

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/sdk/internal/)
{% endhint %}

有关更深入的文档，请查阅 [SDK 架构](https://contributing.bitwarden.com/architecture/sdk)和内部 SDK 项目的 [`README`](https://github.com/bitwarden/sdk-internal)。

## 要求[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#requirements) <a href="#requirements" id="requirements"></a>

* [Rust](https://www.rust-lang.org/tools/install) 最新稳定版本 -（建议通过安装） [rustup](https://rustup.rs/)))
* NodeJS 和 NPM。

更多信息请参阅[工具和库](https://contributing.bitwarden.com/getting-started/tools/)页面。

## 设置说明[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#setup-instructions) <a href="#setup-instructions" id="setup-instructions"></a>

1.  克隆仓库：

    ```
    git clone https://github.com/bitwarden/sdk-internal.git
    cd sdk
    ```
2.  安装依赖项：

    ```
    npm ci
    ```

## 构建 SDK[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#building-the-sdk) <a href="#building-the-sdk" id="building-the-sdk"></a>

SDK 为不同的平台构建，每个平台都有自己的构建说明。有关如何为特定平台构建的更多信息，请参阅不同 crate 的 readmes：

* **网页** ： [`crates/bitwarden-wasm-internal`](https://github.com/bitwarden/sdk-internal/tree/main/crates/bitwarden-wasm-internal)
* **苹果 iOS**: [`crates/bitwarden-uniffi/swift`](https://github.com/bitwarden/sdk-internal/tree/main/crates/bitwarden-uniffi/swift)
* **安卓** : [`crates/bitwarden-uniffi/kotlin`](https://github.com/bitwarden/sdk-internal/tree/main/crates/bitwarden-uniffi/kotlin)

请注意，每个平台都有自己的依赖项，需要在构建之前安装。如果在遇到任何问题时，请务必检查 readme。

## 将 SDK 链接到客户端[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#linking-the-sdk-to-clients) <a href="#linking-the-sdk-to-clients" id="linking-the-sdk-to-clients"></a>

在修改 SDK 之后，测试客户端应用程序中的更改可能是有益的。为此，您需要更新客户端应用程序中的 SDK 引用。

这些说明假设您有一个类似以下目录结构：

```
sdk/
clients/
ios/
android/
```

### 网络客户端[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#web-clients) <a href="#web-clients" id="web-clients"></a>

网页客户端使用 NPM 将 SDK 作为依赖项安装。NPM 提供了专门的命令 [`链接` ](https://docs.npmjs.com/cli/v9/commands/npm-link)，可用于临时用本地版本替换包。

```
npm link ../sdk-internal/crates/bitwarden-wasm-internal/npm
```

警告

运行 `npm ci` 或 `npm install` 将用发布版本替换链接的包。

### 移动[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#mobile) <a href="#mobile" id="mobile"></a>

#### **Android**

1.  在本地 Maven 仓库中构建和发布 SDK：

    ```
    ../sdk-internal/crates/bitwarden-uniffi/kotlin/publish-local.sh
    ```
2. 在 `user.properties` 文件中设置用户属性 `localSdk=true`。

#### iOS[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#ios)

使用设置 `LOCAL_SDK` 环境变量为 true 的 bootstrap 脚本运行，以便使用本地 SDK 构建：

```
LOCAL_SDK=true ./Scripts/bootstrap.sh
```

