# 浏览器端

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/clients/browser/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您必须先完成[客户端存储库设置的说明](../)。

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

1、构建并运行扩展：

```
cd apps/browser
npm run build:watch
```

2、使用下一节中的说明在浏览器中加载已解压的浏览器扩展。

## 测试和调试 <a href="#testing-and-debugging" id="testing-and-debugging"></a>

### Chrome 和基于 Chromium 的浏览器 <a href="#chrome-and-chromium-based-browsers" id="chrome-and-chromium-based-browsers"></a>

要加载浏览器扩展构建：

1. 在地址栏中导航到 `chrome://extensions`，这将打开扩展页面
2. 开启「开发者模式」（拨动开关）
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
4. 单击「iframe」按钮（「三点」旁边）然后选择「/popup/index.html」

### Safari

#### 重置扩展引用路径 <a href="#resetting-the-extension-reference-paths" id="resetting-the-extension-reference-paths"></a>

在 MacOS 上，浏览器扩展与桌面客户端打包在一起。 如果您之前构建、安装或运行过桌面客户端（包括官方版本），Safari 可能会继续加载官方的浏览器扩展，而不是您从源代码构建的版本。

要避免这种情况，请按照以下说明来「重置」Safari 的扩展引用路径：

1. 打开 Safari
2. 点击「偏好设置」，然后点击「扩展程序」选项卡
3. 卸载 Bitwarden 扩展程序
4. 退出并完全关闭 Safari 浏览器
5. 如果您已安装了官方的桌面客户端，卸载它
6. 如果您之前从源码构建了桌面客户端，删除 `PlugIns` 目录（如果存在的话）和 `.dmg`（如果你运行了 Mac Apple Store 的构建）。
7. 重新打开 Safari 浏览器，检查偏好设置，确认没有安装 Bitwarden 浏览器扩展。
8. 退出并完全关闭 Safari 浏览器

如果您要从不同来源加载浏览器扩展（例如，在本地构建和官方版本之间切换），您可能需要定期执行此操作。

#### 测试 <a href="#testing" id="testing"></a>

要构建并加载浏览器扩展：

1、为 Safari 构建扩展

```
npm run dist:safari:dmg
```

2、打开 Safari 并检偏好设置以确认扩展已安装且已启用

{% hint style="danger" %}
您可能需要[在 macOS 中配置 Safari 以运行未签名的扩展](https://developer.apple.com/documentation/safariservices/safari\_web\_extensions/running\_your\_safari\_web\_extension#3744467)。
{% endhint %}

要启用调试

1. 点击「偏好设置」，然后点击「高级」选项卡
2. 启用「在菜单栏中显示开发菜单」

您可以通过点击 `Develop -> Web Extension Background Pages`，然后选择 Bitwarden 来调试浏览器扩展的背景页面。您可以在弹出窗口打开时右键点击它并点击「检查元素」来调试它。

对于大多数调试和测试来说，这应该足够了，除非您使用的是原生代码。

#### 在 Xcode 中开发 <a href="#developing-in-xcode" id="developing-in-xcode"></a>

您还可以使用 Xcode 进行构建和调试，这允许使用更迭代的方法，而无需等待很长时间来编译构建。

1、构建扩展：

```
npm run build
```

2、编辑 `build/manifest.json`。将 `nativeMessaging` 权限从 `optional_permissions` 部分移至 `permissions` 部分

3、在 Xcode 中打开 `src/safari/desktop.xcodeproj`
