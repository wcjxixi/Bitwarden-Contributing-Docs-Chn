# 工具

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/tools/)
{% endhint %}

## 操作系统 <a href="#operating-system" id="operating-system"></a>

所有 Bitwarden 开发人员都建议配备 Macbook。本文档中的工具建议和说明假设您使用的是 macOS。如果您使用不同的操作系统，这可能需要一些调整。

## 推荐工具 <a href="#recommended-tools" id="recommended-tools"></a>

强烈推荐将以下工具作为「标准」开发人员设置的一部分。我们建议所有新的 Bitwarden 开发人员都安装它们，以作为设置本地开发环境的一部分。

### IDE <a href="#ides" id="ides"></a>

* [Visual Studio Code](https://code.visualstudio.com/) - 用于所有 Typescript 项目，也适用于 C#。一定要安装[扩展](tools.md#visual-studio-code-extensions)
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
  * 强烈建议[提交签名](../tools/commit-signing.md)

### 移动端 <a href="#mobile" id="mobile"></a>

* [Android Studio](https://developer.android.com/studio/) - 非常适合设置和运行 Android 模拟器
* [abd](https://developer.android.com/studio/command-line/adb) - 用于与 Android 模拟人生交互
* [Apple Icons Generator Gist](https://gist.github.com/brutella/0bcd671a9e4f63edc12e) - 用于从图像生成 Apple 图标的脚本

### 数据库 <a href="#databases" id="databases"></a>

* [Azure Data Studio](https://docs.microsoft.com/zh-cn/sql/azure-data-studio/download-azure-data-studio) - 用于与本地 SQL Server 一起使用
* [PgAdmin4](https://www.pgadmin.org/) - 用于 PostgreSQL 数据库的基准测试
* [MySQLWorkbench](https://www.mysql.com/products/workbench/) - 用于 MySQL 数据库的基准测试
* [SQLiteStudio](https://www.sqlitestudio.pl/) - 用于操作 SQLite 数据库

### Visual Studio Code 扩展 <a href="#visual-studio-code-extensions" id="visual-studio-code-extensions"></a>

有一些 VS Code 扩展可以节约我们工作中的时间。强烈推荐下面列表中的这些扩展：

* 通用
  * [Back & Forth](https://marketplace.visualstudio.com/items?itemName=nick-rudenko.back-n-forth) - 在编辑器的右上角添加前进和后退按钮。很简单，但我喜欢它。
  * [Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker) - 可能很烦人，但为我节省了很多 `tmes form writting oragnizations`。
  * [LiveShare](https://marketplace.visualstudio.com/items?itemName=MS-vsliveshare.vsliveshare) - 用于结对编程
* C#
  * [C#](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) - Omnisharp 集成
  * [.NET Core Test Explorer](https://marketplace.visualstudio.com/items?itemName=formulahendry.dotnet-test-explorer) - 用于 .NET 测试的测试资源管理器
  * [.NET Core User Secrets](https://marketplace.visualstudio.com/items?itemName=adrianwilczynski.user-secrets) - 通过右键点击 `.proj` 并选择编辑用户机密来编辑机密文件
* Git
  * [Git Graph](https://marketplace.visualstudio.com/items?itemName=mhutchie.git-graph) - 出色的 git 可视化工具
  * [Git History](https://marketplace.visualstudio.com/items?itemName=donjayamanne.githistory) - 更多的 Git 历史
  * [Git Lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens) - 更多的 Git 选项
* Typescript / Angular
  * [Angular Language Service](https://marketplace.visualstudio.com/items?itemName=Angular.ng-template) - 了解 Angular 模板
  * [Jest](https://marketplace.visualstudio.com/items?itemName=Orta.vscode-jest) - Jest 测试运行器
  * [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) - 与更漂亮的代码格式集成
  * [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) - 用于 ESLint 集成
* Rust
  * [rust-analyzer](https://marketplace.visualstudio.com/items?itemName=matklad.rust-analyzer) - 强大的 rust 语言服务器
  * [Even Better TOML](https://marketplace.visualstudio.com/items?itemName=tamasfe.even-better-toml) - 用于处理 TOML（cargo 配置）
  * [CodeLLDB](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb) - 用于 rust 调试
* 数据库
  * [MySQL Syntax](https://marketplace.visualstudio.com/items?itemName=jakebathman.mysql-syntax) - 用于 MySQL 的语法高亮显示
  * [PostgreSQL](https://marketplace.visualstudio.com/items?itemName=ckolkman.vscode-postgres) - 用于 PostgreSQL 的语法高亮显示

## 可选工具 <a href="#optional-tools" id="optional-tools"></a>

根据您的偏好或您正在开发的内容，以下工具可能会很有用：

* [JetBrains Rider](https://www.jetbrains.com/rider/) ($) - Visual Studio 和/或 Visual Studio Code for MacOS 的替代
* [Microsoft Azure Storage Explorer](https://azure.microsoft.com/zh-cn/features/storage-explorer/) - 用于连接本地 Azure 表存储和队列，或与本地 Azure 表存储和队列一起使用
* [Parallels](https://www.parallels.com/) - 用于运行 Windows VM（虚拟机）
* [Sourcetree](https://www.sourcetreeapp.com/) -Git GUI。注意：在 macOS 上使用 nvm 时，要使 git hooks 正常运行，请遵循[这些说明](https://typicode.github.io/husky/#/?id=command-not-found)。
