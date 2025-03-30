# Secrets Manager

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/)
{% endhint %}

本章节包含 Bitwarden Secrets Manager CLI、语言包装器和基于 SDK 集成的开发信息。

有关更深入的文档，请查阅 [SDK 架构](../../../architecture/sdk/)和 Secrets Manager SDK 项目的 [`README`](https://github.com/bitwarden/sdk-sm)。

## 要求[​](https://contributing.bitwarden.com/getting-started/sdk/internal/#requirements) <a href="#requirements" id="requirements"></a>

* [Rust](https://www.rust-lang.org/tools/install) 最新稳定版本 -（建议通过 [rustup](https://rustup.rs/) 安装）
* NodeJS 和 NPM。

更多信息请参阅[工具和库](../../tools.md)页面。

## 设置说明[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/#setup-instructions) <a href="#setup-instructions" id="setup-instructions"></a>

1.  克隆存储库：

    ```bash
    git clone https://github.com/bitwarden/sdk-sm.git
    cd sdk
    ```
2.  安装依赖：

    ```bash
    npm ci
    ```

## 构建 SDK[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/#building-the-sdk) <a href="#building-the-sdk" id="building-the-sdk"></a>

要构建 SDK，请运行以下命令：

```bash
cargo build
```

## 网页客户端[​](https://contributing.bitwarden.com/getting-started/sdk/secrets-manager/#web-client) <a href="#web-client" id="web-client"></a>

要启动网页客户端，请按照[网页密码库设置说明](../../clients/web-vault/)，创建一个启用了 Secrets Manager 的组织。然后使用组织切换器进入 Secrets Manager。

{% embed url="https://contributing.bitwarden.com/assets/images/sm-product-switcher-7b3de19f3d4c8cacc95558f947ced76f.png" %}

{% hint style="info" %}
如果您已经为您的组织启用了 Secrets Manager，但在产品切换器中看不到 Secrets Manager，您可能需要为您的用户手动启用它，方法是进入**管理控制台** -> **成员** -> 为您的用户选中「此用户可以访问 Secrets Manager」。

<img src="https://contributing.bitwarden.com/assets/images/enable-sm-4f772badfe704c8e7998c61042ff3e3c.png" alt="" data-size="original">
{% endhint %}
