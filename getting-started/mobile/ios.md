# iOS

{% hint style="info" %}
对应的[官方文档地址](https://contributing.bitwarden.com/getting-started/mobile/ios/)
{% endhint %}

## 要求[​](https://contributing.bitwarden.com/getting-started/mobile/ios/#requirements) <a href="#requirements" id="requirements"></a>

1. [Xcode](https://developer.apple.com/cn/xcode/)（版本 15.4）
2. 设置一个 iPhone 15 Pro 模拟器（iOS 17.0.1）

## 设置[​](https://contributing.bitwarden.com/getting-started/mobile/ios/#setup) <a href="#setup" id="setup"></a>

1、克隆存储库：

```sh
$ git clone https://github.com/bitwarden/ios
```

2、安装 [Mint](https://github.com/yonaskolb/mint)：

```sh
$ brew install mint
```

或者，如果您想不使用 `brew` 安装 Mint，请将 Mint 存储库克隆到临时目录中并运行 `make`：

```sh
$ git clone https://github.com/yonaskolb/Mint.git
$ cd Mint
$ make
```

3、引导项目：

```sh
$ Scripts/bootstrap.sh
```

> **注意**：因为 `Scripts/bootstrap.sh` 用于生成项目，因此每次项目配置或文件结构发生变化（例如添加、删除或移动文件）时，都需要运行 `bootstrap.sh`。 通常情况下，最佳做法是在切换分支或拉取更改时运行 `bootstrap.sh`。

或者，您可以创建 git 钩子，以便在每次 git 钩子事件发生时自动执行 `bootstrap.sh` 脚本。要使用 `Scripts` 目录中已定义的 git 钩子脚本，请将脚本复制到 `.git/hooks` 目录：

```sh
$ cp Scripts/post-merge .git/hooks/
$ cp Scripts/post-checkout .git/hooks/
```

### 运行 App <a href="#run-the-app" id="run-the-app"></a>

1. 在 Xcode 15.4+ 中打开项目。
2. 在模拟器中运行带有 `Bitwarden` 目标的 App。

### 运行测试[​](https://contributing.bitwarden.com/getting-started/mobile/ios/#running-tests) <a href="#running-tests" id="running-tests"></a>

由于 iOS 版本之间快照测试的细微差异，测试目标需要在 iPhone 15 Pro 模拟器（iOS 17.0.1）中运行。

1. 在 Xcode 的工具栏中，选择项目以及连接的设备或模拟器。
   * 用于构建的 `Generic iOS Device` 将无法用于测试。
2. 在 Xcode 的菜单栏中，选择 `Product > Test`。
   * 测试结果将出现在调试区域。如果调试区域未显示，您可以通过以下方式访问： `View > Debug Area > Show Debug Area` 。

### Linting[​](https://contributing.bitwarden.com/getting-started/mobile/ios/#linting) <a href="#linting" id="linting"></a>

本项目使用 [SwiftLint](https://github.com/realm/SwiftLint) 和 [SwiftFormat](https://github.com/nicklockwood/SwiftFormat) 进行着色。在每次构建 `Bitwarden` 目标时，这两个工具都会以着色模式运行。不过，如果您想让 SwiftFormat 自动纠正在着色过程中发现的任何问题，可以手动运行修复命令 `mint run swiftformat`。

此外，如果您希望 SwiftFormat 在每次提交前自动更正任何问题，可以使用 git 钩子脚本。要使用 `Scripts` 目录中已定义的 git 钩子脚本，请将脚本复制到 `.git/hooks` 目录：

```sh
$ cp Scripts/pre-commit .git/hooks/
```
