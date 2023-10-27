# 浏览器端

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/browser/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您必须先完成[客户端存储库设置说明](../)。

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

1、构建并运行扩展：

```bash
cd apps/browser
npm run build:watch
```

2、使用下一节中的说明在浏览器中加载已解压的浏览器扩展。

## 环境设置 <a href="#environment-setup" id="environment-setup"></a>

默认情况下，浏览器扩展将运行指向生产服务器的端点。要覆盖此设置以进行本地开发和测试，有多种选项。

### Using `managedEnvironment` <a href="#using-managedenvironment" id="using-managedenvironment"></a>

浏览器扩展具有「代管式环境」的概念，它是存储在 `devFlags` 对象内的 [`development.json`](https://github.com/bitwarden/clients/blob/master/apps/browser/config/development.json) 中的 JSON 配置。

`managedEnvironment` 设置允许贡献者覆盖服务器的任何或所有 URL。 `managedEnvironment` 在 [`BrowserEnvironmentService`](https://github.com/bitwarden/clients/blob/master/apps/browser/src/services/browser-environment.service.ts) 中被读取，并为任何提供的 URL 覆盖默认的（生产）设置。

有两种使用 `managedEnvironment` 的方法，具体取决于您是否同时运行 Web Vault。

#### 运行 Web Vault 的 **`managedEnvironment`** <a href="#managedenvironment-with-web-vault-running" id="managedenvironment-with-web-vault-running"></a>

如果您还同时运行 Web Vault，则只需在 `managedEnvironment` 中设置 `base` URL：

```json
{
   "devFlags":{
      "managedEnvironment":{
         "base":"https://localhost:8080"
      }
      ...
   }
   ...
}
```

这是因为 Web Vault 在其 [`webpack.config.js`](https://github.com/bitwarden/clients/blob/master/apps/web/webpack.config.js) 中包含了 `webpack-dev-server` 软件包。当它运行时，它根据自己的 [`development.json`](https://github.com/bitwarden/clients/blob/master/apps/web/config/development.json) 配置文件中的配置设置代理每一个端点：

```json
  "dev": {
    "proxyApi": "http://localhost:4000",
    "proxyIdentity": "http://localhost:33656",
    "proxyEvents": "http://localhost:46273",
    "proxyNotifications": "http://localhost:61840"
  },
```

这意味着当 Web Vault 运行时，浏览器 `managedEnvironment` **无需**逐个覆盖每一个 URL。浏览器会将每个 URL 格式化为 `{base}/{endpoint}`，例如 http://localhost:8080/api，但 webpack DevServer 会将该 URL 代理到正确的端口，例如 http://localhost:4000。

#### 未运行 Web Vault 的 **`managedEnvironment`** <a href="#managedenvironment-without-web-vault-running" id="managedenvironment-without-web-vault-running"></a>

如果您在没有运行 Web Vault 的情况下测试浏览器扩展，您将无法利用 webpack DevServer 来代理 URL。这意味着您的 `managedEnvironment` 设置必须显式覆盖您要在本地进行通信的所有 URL。

```json
{
    "devFlags": {
        "managedEnvironment": {
            "webVault": "http://localhost:8080",
            "api": "http://localhost:4000",
            "identity": "http://localhost:33656",
            "notifications": "http://localhost:61840",
            "icons": "http://localhost:50024"
        }
        ...
    }
    ...
}
```

### 手动设置自定义环境 URL <a href="#manually-setting-the-custom-environment-urls" id="manually-setting-the-custom-environment-urls"></a>

加载扩展后，您可能需要调整服务器 URL 以指向本地服务器，而不是在 `managedEnvironment` 中覆盖它们。您可以通过浏览器设置更改。有关如何配置 URL 的说明，请点击[此处](https://help.ppgg.in/self-hosting/connect-clients-to-your-instance)。

配置完成后，您的本地自定义环境看上去应该像这样：

{% embed url="https://contributing.bitwarden.com/assets/images/custom-local-environment-2c78fa111af8dc5186b8da9d12b7fdea.png" %}

## 测试和调试 <a href="#testing-and-debugging" id="testing-and-debugging"></a>

### Chrome 和基于 Chromium 的浏览器 <a href="#chrome-and-chromium-based-browsers" id="chrome-and-chromium-based-browsers"></a>

要加载浏览器扩展构建：

1. 在地址栏中导航到 `chrome://extensions`，这将打开扩展页面
2. 开启「开发者模式」（切换开关）
3. 点击「加载已解压的扩展程序」按钮
4. 打开您本地存储库的 `build` 文件夹然后确认您的选择

现在您已拥有了已安装的浏览器扩展程序的本地构建。

您可以通过点击 `chrome://extensions` 中 Bitwarden 标题下方的「background.html」来调试浏览器扩展的背景页面。您可以在弹出窗口打开时右键点击它并点击「检查」来调试它。

### Firefox

要加载浏览器扩展构建：

1. 在地址栏中导航到 `about:debugging`，这将打开附加组件页面
2. 点击「此 Firefox」
3. 点击「载入临时附加组件」
4. 打开您本地存储库的 `build` 文件夹然后打开 `manifest.json` 文件

现在您已拥有了已安装的浏览器扩展程序的本地构建。

临时附加组件仅安装在当前会话中。如果您关闭然后重新打开 Firefox，您必须再次加载临时附加组件。

您可以通过点击临时附加组件页面中 Bitwarden 标题旁边的「检查」按钮来调试浏览器扩展的背景页面。要调试弹出窗口：

1. 使用上面的说明检查背景页面
2. 点击调试器右上角的「三点」，然后点击「禁用弹出式自动隐藏」
3. 打开扩展弹出窗口
4. 点击「iframe」按钮（「三点」旁边）然后选择「/popup/index.html」

### Safari

Safari WebExtensions 必须通过 Mac App Store 分发，并与常规 Mac App Store 应用程序捆绑在一起。因此，与其他浏览器相比，构建和调试过程略有不同。

#### 卸载以前的版本 <a href="#uninstall-previous-versions" id="uninstall-previous-versions"></a>

如果您已构建、已安装或运行过桌面客户端（包括官方版本），Safari 很可能会继续加载官方浏览器扩展，而不是加载从源代码构建的版本。

要避免这种情况，请按照以下说明卸载 Safari 扩展：

1. 打开 Safari
2. 点击「设置」，然后点击「扩展程序」选项卡
3. 点击 Bitwarden 扩展旁边的「卸载」
4. 使用扩展删除此应用程序
5. 重新打开 Safari 并检查「设置」以确认没有安装 Bitwarden 浏览器扩展。如果仍有 Bitwarden 扩展，请重复步骤 3-4
6. 退出并完全关闭 Safari 浏览器

如果您要从不同来源加载浏览器扩展（例如，在本地构建和官方版本之间切换），您可能需要定期执行此操作。

#### 在 Xcode 中开发 <a href="#developing-in-xcode" id="developing-in-xcode"></a>

开发扩展的最简单方法是使用 Xcode 构建和调试它。

1、构建扩展：

```bash
npm run build
```

2、编辑 `build/manifest.json`。将 `nativeMessaging` 权限从 `optional_permissions` 部分移至 `permissions` 部分。

3、编辑 `build/index.html` 文件，将 `<html class="__BROWSER__">` 替换为 `<html class="browser_safari">`。

3、在 Xcode 中打开 `src/safari/desktop.xcodeproj`

4、运行「桌面」目标。

{% hint style="info" %}
每当对源文件进行任何更改时，请记住通过 Xcode 重新运行它。它不会自动重新加载。
{% endhint %}

#### 生产构建 <a href="#production-build" id="production-build"></a>

另一种方法是通过 gulp 使用「正确的」构建流程。这种方法不需要对输出进行任何手动处理，因为 gulp 会为我们完成这些工作。不过，我们必须为每次更改完全重建扩展，这会比较慢。

要构建并加载浏览器扩展：

1、为 Safari 构建扩展

```bash
npm run dist:safari:dmg
```

2、打开 Safari 并检「设置」以确认扩展已安装且已启用

{% hint style="warning" %}
您可能需要[在 macOS 中配置 Safari 以运行未签名的扩展](https://developer.apple.com/documentation/safariservices/safari\_web\_extensions/running\_your\_safari\_web\_extension#3744467)。
{% endhint %}

要启用调试：

1. 点击「设置」，然后点击「高级」选项卡
2. 启用「在菜单栏中显示开发菜单」

您可以通过点击 `Develop -> Web Extension Background Pages`，然后选择 Bitwarden 来调试浏览器扩展的背景页面。您可以在弹出窗口打开时右键点击它并点击「检查元素」来调试它。

对于大多数调试和测试来说，这应该足够了，除非您使用的是本机代码。
