# 桌面端

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/desktop/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您必须完成[客户端存储库设置说明](../)。

{% tabs %}
{% tab title="Windows" %}
在 Visual Studio 安装程序中，这些都作为附加依赖项提供。

* Visual C++ 构建工具
* [Rust](https://www.rust-lang.org/tools/install)
{% endtab %}

{% tab title="macOS" %}
* Xcode 命令行工具
* [Rust](https://www.rust-lang.org/tools/install)
{% endtab %}

{% tab title="Linux" %}
* 以下软件包
  * `build-essential`
  * `libsecret-1-dev`
  * `libglib2.0-dev`
* [Rust](https://www.rust-lang.org/tools/install)
{% endtab %}
{% endtabs %}

## 构建本机模块 <a href="#build-native-module" id="build-native-module"></a>

桌面应用程序依赖一个使用 rust 编写的本机模块，需要单独编译。这已包含在 `npm run electron` 的构建过程中，但您也可以手动编译它。

```bash
cd apps/desktop/desktop_native
npm run build
```

**注意**：如果本机代码发生了变化，需要重新构建这个模块。

### 交叉编译 <a href="#cross-compile" id="cross-compile"></a>

在某些环境中，例如 WSL（Windows Subsystem for Linux - Linux 的 Windows 子系统），可能需要交叉编译本机模块。为此，首先确保您已安装相关的 Rust 目标。更多信息，请参阅 [`rustup` 文档](https://rust-lang.github.io/rustup/cross-compilation.html)。

```
# 确保 cargo 环境文件的来源。
source "$HOME/.cargo/env"

cd apps/desktop/desktop_native
export PKG_CONFIG_ALL_STATIC=1
export PKG_CONFIG_ALLOW_CROSS=1
npm run build -- --target x86_64-unknown-linux-musl # 替换为相关目标
```

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

构建并运行：

```bash
cd apps/desktop
npm run electron
```

## 调试和测试 <a href="#debugging-and-testing" id="debugging-and-testing"></a>

Electron 应用程序有一个在 Electron 窗口中运行的渲染器进程，以及一个在后台运行的主进程。

渲染器进程可以使用 Chromium 调试器进行检查。它应该在桌面应用程序打开时自动打开，或者您可以从「视图」菜单中打开它。

主进程可以通过从 Visual Studio Code 中的 [Javascript 调试终端](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#\_javascript-debug-terminal)运行此应用程序，然后在 `build/main.js` 中放置断点进行调试。

## 生物识别解锁（本机消息传递） <a href="#biometric-unlock-native-messaging" id="biometric-unlock-native-messaging"></a>

配置本机消息传递（桌面应用程序和浏览器扩展之间的通信）的说明位于[浏览器章节](../browser/biometric.md)。

## 故障排除 <a href="#troubleshooting" id="troubleshooting"></a>

### 构建故障 <a href="#trouble-building" id="trouble-building"></a>

如果您看到这样的错误：

```bash
[Main] Error: Cannot find module '@bitwarden/desktop-native-darwin-arm64'
```

您可能还没有构建本机模块，请参阅[构建本机模块](./#build-native-module)。

### 桌面 Electron 应用程序窗口未打开 <a href="#desktop-electron-app-window-doesnt-open" id="desktop-electron-app-window-doesnt-open"></a>

如果运行 `npm run Electron` 会显示类似以下的错误：

```bash
[Main] npm ERR! Error: Missing script: "build-native"
```

或 electron 窗口无法渲染，您可能需要更新 node 和/或 npm。从旧版本升级后，这个问题将得到解决：

* Node：`16.18.1`
* npm：`8.19.2`
