# 桌面端

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/clients/desktop/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您必须完成[客户端存储库设置说明](../)。

macOS：

* Xcode 命令行工具
* [Rust](https://www.rust-lang.org/tools/install)

Windows（这些都是在 Visual Studio 安装程序中作为附加依赖项提供的）：

* Visual C++ 构建工具
* [Rust](https://www.rust-lang.org/tools/install)

&#x20;Linux：

* 以下软件包 `build-essential libsecret-1-dev libglib2.0-dev`
* [Rust](https://www.rust-lang.org/tools/install)

## 构建本机模块 <a href="#build-native-module" id="build-native-module"></a>

桌面应用程序依赖一个使用 rust 编写的本机模块，需要单独编译。这已包含在 `npm run electron` 的构建过程中，但您也可以手动编译它。

```bash
cd apps/desktop/desktop_native
npm run build
```

**注意**：如果本机代码发生了变化，需要重新构建这个模块。

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

配置本机消息传递（桌面应用程序和浏览器扩展之间的通信）的说明位于[浏览器部分](../browser/biometric.md)。

## 构建中的故障 <a href="#trouble-building" id="trouble-building"></a>

如果您看到这样的错误：

```
[Main] Error: Cannot find module '@bitwarden/desktop-native-darwin-arm64'
```

您可能还没有构建本机模块，请参阅[构建本机模块](./#build-native-module)。
