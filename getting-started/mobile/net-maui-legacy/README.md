# =.NET MAUI (legacy)

{% hint style="info" %}
对应的[官方文档地址](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/)
{% endhint %}

{% hint style="warning" %}
**Legacy**

在 .NET MAUI 中完成的**旧版**移动 App 入门。
{% endhint %}

## 配置 Git blame <a href="#configure-git-blame" id="configure-git-blame"></a>

我们建议您配置 Git 以忽略 Prettier 修订版本：

```bash
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

## Android 开发[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/#android-development) <a href="#android-development" id="android-development"></a>

请参阅 [Android 移动 App](android.md) 页面以设置 Android 开发环境。

## iOS 开发[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/#ios-development) <a href="#ios-development" id="ios-development"></a>

请参阅 [iOS 移动 App](ios.md) 页面以设置 iOS 开发环境。

## watchOS 开发[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/#watchos-development) <a href="#watchos-development" id="watchos-development"></a>

请参阅  [watchOS App](watchos.md) 页面以设置 watchOS 开发环境。

## 单元测试[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/#unit-tests) <a href="#unit-tests" id="unit-tests"></a>

{% hint style="success" %}
TL;DR;

为了运行单元测试，请在构建/运行时在 `dotnet` 命令中添加参数 `/p:CustomConstants=UT`。要进行单元测试或使用测试运行程序，请在 `Directory.Build.props` 中取消对 `CustomConstants` 行的注释。
{% endhint %}

鉴于 `Core.csproj` 是一个 MAUI 项目，其目标框架为 `net8.0-android;net8.0-ios`，而我们需要在测试中使用 `net8.0`，因此我们需要一种方法来添加该框架。`Core.Test.csproj` 将 `net8.0` 作为目标框架，因此通过添加参数 `/p:CustomConstants=UT`，我们可以将 `UT` 作为常量添加到项目中。这样，接下来会发生以下事情：

* `UT` 被添加为常量，供预编译器指令使用。
* `Core.csproj` 被修改为添加 `net8.0` 作为单元测试的目标框架。
* `FFImageLoading` 被移除作为参考，因为它不支持 `net8.0`。因此，现在我们有一个封装的 `CachedImage`，如果不是 `UT`，则使用库中的 `CachedImage`，如果是 `UT`，则使用带有 NOOP 实现的自定义 `CachedImage`。

如果想构建测试项目，需要前往 `test/Core.Test` 并运行：

```bash
dotnet build -f net8.0 /p:CustomConstants=UT
```

要运行测试，请进入相同的文件夹并运行：

```bash
dotnet test -f net8.0 /p:CustomConstants=UT
```

最后，当工作于 `Core.Test` 项目或想要使用测试运行器时，请转到 `Directory.Build.props`（位于根目录），取消对引用 `CustomConstants` 的行的注释，以便所有内容都将相应地加载到项目中。 由于某些问题，只有在 `UT` 常量就位时，引用的项目（如 `Core`）才会包含在内。取消这行注释后，项目将被引用，可以在该项目上工作或通过测试运行器运行测试。

## 自定义常量[​](https://contributing.bitwarden.com/getting-started/mobile/net-maui-legacy/#custom-constants) <a href="#custom-constants" id="custom-constants"></a>

在构建/运行/发布时，可通过参数 `/p:CustomConstants={Value}` 使用自定义产量：

* `FDROID`：用于指示这是 F-Droid 的构建/发布版本（ 了解更多）
* `UT`：在构建/运行测试项目或在这些项目之一上工作时使用（ [了解更多](./#unit-tests)）

这些常量被添加到已定义的常量中，因此任何人都可以通过预编译指令在代码中使用它们。
