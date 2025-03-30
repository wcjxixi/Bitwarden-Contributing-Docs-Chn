# Android

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/mobile/android/)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* Android Studio - 最新稳定版本
* Android SDK - 对于 SDK 平台版本，请参考 [libs.versions.toml](https://github.com/bitwarden/android/blob/main/gradle/libs.versions.toml) 文件中的 `compileSdk` 版本

## 设置 <a href="#setup" id="setup"></a>

1.  克隆仓库：

    ```sh
    $ git clone https://github.com/bitwarden/android
    ```
2. 在项目的根目录下创建一个 `user.properties` 文件，并添加以下属性：
   * `gitHubToken`：一个具有 `read:packages` 权限的「经典」 GitHub 个人访问令牌 (PAT)（例如：`gitHubToken=gph_xx...xx`）。这些令牌可以通过访问 [GitHub 令牌页面](https://github.com/settings/tokens)生成。参阅[有关身份验证的 GitHub 包用户文档](https://docs.github.com/zh/packages/working-with-a-github-packages-registry/working-with-the-gradle-registry)了解更多详情。
   * `localSdk`：一个布尔值，用于确定是否应从本地 Maven 仓库加载 SDK（例如：`localSdk=true`）。这在开发新 SDK 时特别有用。查看[将 SDK 链接到客户端](../../sdk/internal-sdk.md#linking-the-sdk-to-clients)了解更多详情。
3. 设置代码风格格式化工具：

所有代码必须遵循以下指南中描述的[代码样式指南文档](../../../contributing/code-style/android-and-kotlin.md) 。为了帮助遵守这些规则，所有贡献者应将 `docs/bitwarden-style.xml` 作为他们的代码样式方案应用。在 IntelliJ / Android Studio 中：

* 导航到 `Preferences > Editor > Code Style` 。
* 点击 `Scheme` 旁边的 `Manage` 按钮。
* 选择 `Import`。
* 在项目的 `docs/` 目录中找到 `bitwarden-style.xml` 文件。
* 从 `BitwardenStyle` 导入到 `BitwardenStyle`。
* 勾选 `Enable EditorConfig support`。
* 点击 `Apply` 和 `OK` 保存更改然后退出 `Preferences`。

注意，在某些情况下，您可能需要重新启动 Android Studio 才能使更改生效。

所有代码在提交拉取请求之前都应格式化。这可以手动完成，但创建一个带有自定义键盘绑定的宏以在保存时自动格式化也是有帮助的。在 OS X 的 Android Studio 中：

* 选择 `Edit > Macros > Start Macro Recording`
* 选择 `Code > Optimize Imports`
* 选择 `Code > Reformat Code`
* 选择 `File > Save All`
* 选择 `Edit > Macros > Stop Macro Recording`

这可以通过导航到 `Android Studio > Preferences` 并编辑 `Keymap` 下的宏来实现一组键（例如：shift + command + s）。

请避免在同一个提交/PR 中混合格式化和逻辑更改。如果可能，在提交逻辑更改之前，先在单独的 PR 中修复任何大的格式化问题。这有助于他人专注于代码审查时的有意义代码更改。

## 依赖 <a href="#dependencies" id="dependencies"></a>

对于应用程序、开发环境和 CI/CD 依赖，请参阅 [libs.versions.toml](https://github.com/bitwarden/android/blob/main/gradle/libs.versions.toml) 文件。
