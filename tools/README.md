# 工具

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/tools/)
{% endhint %}

## 操作系统 <a href="#operating-system" id="operating-system"></a>

所有 Bitwarden 开发人员都建议配备 Macbook。本文档中的工具建议和说明假设您使用的是 macOS。如果您使用不同的操作系统，这可能需要一些调整。

## 推荐工具 <a href="#recommended-tools" id="recommended-tools"></a>

强烈推荐将以下工具作为「标准」开发人员设置的一部分。我们建议所有新的 Bitwarden 开发人员都安装它们，以作为设置本地开发环境的一部分。

### IDE <a href="#ides" id="ides"></a>

* [Visual Studio Code](https://code.visualstudio.com/) - 用于所有 Typescript 项目，也适用于 C#。一定要安装[扩展](./#visual-studio-code-extensions)
* [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) - 特别适合 C#（用于服务器和移动端）
* [Xcode](https://developer.apple.com/xcode/) - 用于 iOS 移动端和 Safari 网页扩展开发

### 本地环境 <a href="#local-environment" id="local-environment"></a>

* [Homebrew](https://brew.sh/) - macOS 的包管理器
* [Iterm2](https://iterm2.com/)（可通过 Homebrew 获得）- 更好的终端模拟器
* 各种浏览器 - 值得庆幸有大量浏览器可用于在许多场景中测试扩展。您也可以使用多个浏览器安装不同版本的浏览器扩展来对这些扩展进行比较
* [Docker](https://docs.docker.com/get-docker/) - 仅服务器开发需要
* [PowerShell](https://learn.microsoft.com/zh-cn/powershell/scripting/install/installing-powershell-on-macos)（可通过 Homebrew 获得：`brew install powershell`）
* [NodeJS](https://nodejs.org/) v16（最好使用[节点版本管理器](https://docs.npmjs.com/downloading-and-installing-node-js-and-npm)）
* [NPM](https://www.npmjs.com/) v8（包含在 Node 中）
* [Rust](https://www.rust-lang.org/tools/install) - 用于本地桌面组件
* [Git](https://git-scm.com/)
  * 所有 _Bitwarden 贡献者_都需要[提交签名](commit-signing.md)，特别鼓励_社区贡献者_提交签名

### 移动端 <a href="#mobile" id="mobile"></a>

* [Android Studio](https://developer.android.com/studio/) - 非常适合设置和运行 Android 模拟器
* [abd](https://developer.android.com/studio/command-line/adb) - 用于与 Android 模拟人生交互

### 数据库 <a href="#databases" id="databases"></a>

* [Azure Data Studio](https://docs.microsoft.com/zh-cn/sql/azure-data-studio/download-azure-data-studio) - 用于与本地 SQL Server 一起使用
* [PgAdmin4](https://www.pgadmin.org/) - 用于 PostgreSQL 数据库的基准测试
* [MySQLWorkbench](https://www.mysql.com/products/workbench/) - 用于 MySQL 数据库的基准测试

### Visual Studio 代码扩展 <a href="#visual-studio-code-extensions" id="visual-studio-code-extensions"></a>

在我们的工作中，有一些 VS 代码扩展可以挽救生命。强烈推荐的列表包括以下内容：

* 通用
  * Back & Forth - 在编辑器的右上角添加前进和后退按钮。很简单，但我喜欢它。
  * Code Spell Checker - 可能很烦人，但为我节省了很多写作组织的时间。
  * LiveShare - 用于结对编程
* C#
  * C# - Omnisharp 集成
  * .NET Core Test Explorer - 用于 .NET 测试的测试资源管理器
  * .NET Core User Secrets - 通过右键单击 .proj 并选择编辑用户机密来编辑机密文件
* Git
  * Git Graph - 出色的 git 可视化工具
  * Git History - 更多 Git 历史
  * Git Lens - 更多 Git 选项
* Typescript / Angular
  * Angular Language Service - 了解 Angular 模板
  * Jest - Jest 测试运行器
  * Prettier - 与更漂亮的代码格式集成
  * ESLint - ESLint 的集成
* Rust
  * rust-analyzer - 伟大的 rust 语言服务器
  * Even Better TOML - 用于处理 TOML（货物配置）
  * CodeLLDB - 用于 rust 调试
* 数据库
  * MySQL Syntax - 用于 MySQL 的语法高亮显示
  * PostgreSQL - 用于 PostgreSQL 的语法高亮显示

## 可选工具 <a href="#optional-tools" id="optional-tools"></a>

根据您的偏好或您正在开发的内容，以下工具可能会很有用：

* [JetBrains Rider](https://www.jetbrains.com/rider/) ($) - Visual Studio 和/或 Visual Studio Code for MacOS 的替代
* [Microsoft Azure Storage Explorer](https://azure.microsoft.com/zh-cn/features/storage-explorer/) - 用于连接本地 Azure 表存储和队列，或与本地 Azure 表存储和队列一起使用
* [Sourcetree](https://www.sourcetreeapp.com/) -Git GUI

**注意**：为了让 git 钩子在使用 nvm 时在 macOS 上正常运行，请创建以下文件：

```sql
    # ~/.huskyrc
    # This loads nvm.sh and sets the correct PATH before running hook
    export NVM_DIR="$HOME/.nvm"

    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"
```
