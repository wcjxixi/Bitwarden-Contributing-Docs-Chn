# 工具

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/tools/)
{% endhint %}

## 操作系统 <a href="#operating-system" id="operating-system"></a>

所有 Bitwarden 开发人员都配有 Macbook。本文档中的工具建议和说明假设您使用的是 macOS。如果您使用不同的操作系统，这可能需要一些调整。

## 推荐工具 <a href="#recommended-tools" id="recommended-tools"></a>

强烈推荐将以下工具作为“标准”开发人员设置的一部分。我们建议任何新的 Bitwarden 开发人员都安装它们，以作为设置本地开发环境的一部分。 IDE

* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) - 用于服务器和移动端
* [Visual Studio Code](https://code.visualstudio.com/) - 用于所有 Typescript 项目，具有以下推荐的扩展：
  * [Xcode](https://developer.apple.com/xcode/) - 需要支持移动端开发&#x20;

## 本地环境 <a href="#local-environment" id="local-environment"></a>

* [Docker](https://docs.docker.com/get-docker/) - 仅服务器开发需要
* [Homebrew](https://brew.sh/)
* [PowerShell](https://learn.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell-on-macos)（可通过 Homebrew 获得：`brew install powershell`）
* [NodeJS](https://nodejs.org/) v16（最好使用[节点版本管理器](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)）
* [NPM](https://www.npmjs.com/) v8（包含在 Node 中）
* [Rust](https://www.rust-lang.org/tools/install)

## 提交签名 <a href="#commit-signing" id="commit-signing"></a>

所有 Bitwarden 开发人员都需要[提交签名](contributing/commit-signing.md)，并且强烈鼓励社区贡献者使用。

## 其他工具 <a href="#other-tools" id="other-tools"></a>

* [Azure Data Studio](https://docs.microsoft.com/zh-cn/sql/azure-data-studio/download-azure-data-studio) - 用于与本地 SQL Server 一起使用
* [Iterm2](https://iterm2.com/)（可通过 Homebrew 获得）- 更好的终端仿真器
* [Sourcetree](https://www.sourcetreeapp.com/) -Git 图形用户界面

**注意**：为了让 git 钩子在使用 nvm 时在 macOS 上正常运行，请创建以下文件：

```
    # ~/.huskyrc
    # This loads nvm.sh and sets the correct PATH before running hook
    export NVM_DIR="$HOME/.nvm"

    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```

## 可选工具 <a href="#optional-tools" id="optional-tools"></a>

根据您的偏好或您正在开发的内容，以下工具可能会很有用：

* [JetBrains Rider](https://www.jetbrains.com/rider/) ($) - Visual Studio 和/或 Visual Studio Code for MacOS 的替代品
* [Microsoft Azure Storage Explorer](https://azure.microsoft.com/zh-cn/features/storage-explorer/) - 用于连接本地 Azure 表存储和队列，或与本地 Azure 表存储和队列一起使用
