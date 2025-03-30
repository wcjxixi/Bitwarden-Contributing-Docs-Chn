# =F-Droid

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/)
{% endhint %}

## 概述[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#overview) <a href="#overview" id="overview"></a>

Bitwarden F-Droid 仓库托管在 [GitHub](https://github.com/bitwarden/f-droid) 上。它包含所有在 F-Droid 上可用的 Bitwarden App。

Bitwarden F-Droid 仓库会定期自动更新，以确保仓库中托管的 App 与 Google Play Store 发布的最新版本保持同步。

## 设置[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#setup) <a href="#setup" id="setup"></a>

### Go <a href="#go" id="go"></a>

构建和运行 `metascoop` 应用程序需要 Go 语言。

使用 Homebrew 下载和安装 Go 的命令如下：

```sh
brew install go
```

其他下载和安装选项可以在 [Go 安装文档](https://go.dev/doc/install)中找到。

### F-Droid 服务器和仓库工具[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#f-droid-server-and-repo-tools) <a href="#f-droid-server-and-repo-tools" id="f-droid-server-and-repo-tools"></a>

要手动更新 F-Droid 仓库，需要使用 F-Droid 服务器和仓库工具。安装说明可在官方的 [F-Droid 服务器和仓库工具文档](https://f-droid.org/zh_Hans/docs/Installing_the_Server_and_Repo_Tools/)中找到。

### 安卓 SDK[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#android-sdk) <a href="#android-sdk" id="android-sdk"></a>

F-Droid 服务器和仓库工具需要 `apksigner`，它是安卓 SDK 的一部分。

要使用 Homebrew 安装所需的安卓 SDK 工具，请运行以下命令：

```sh
brew install android-sdk
android update sdk --no-ui --all --filter tools,platform-tools,build-tools-25.0.0
```

可以在[此处](https://developer.android.com/tools/releases/platform-tools?hl=zh-cn)找到下载和安装所需 Android SDK 工具的替代说明。

## 文件结构[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#file-structure) <a href="#file-structure" id="file-structure"></a>

该存储库的结构如下：

* `fdroid/`：存放应用的 F-Droid 仓库。
* `metascoop/`：用于在 Bitwarden App 发布新版本时更新 F-Droid 仓库的 Go App。
* `repos.yml`：定义源代码库和可托管的 F-Droid App 的文件。

### `repos.yml`[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#reposyml) <a href="#reposyml" id="reposyml"></a>

此文件包含有关 `metascoop` 将搜索新 F-Droid 发布的源仓库的详细信息。它使用以下结构来声明仓库及其应用：

```
my-repository:
  git: "https://github.com/bitwarden/android"
  applications:
    - filename: "com.x8bit.bitwarden-fdroid.apk"
      id: "bitwarden"
      name: "Bitwarden"
      categories:
        - Security
      description: |
        My awesome app description.
```

* `my-repository`：仓库的名称。用于在索引中识别仓库。
* `git`：源仓库的 URL。
* `applications`：仓库中可用的应用程序。可以通过向 `applications` 列表中添加新条目来向仓库添加多个应用程序。
  * `filename`：将从源仓库下载的 APK 文件的名称。
  * `id`：应用程序的 ID。这必须是唯一的，并用于在 F-Droid 仓库中识别应用程序。
  * `name`：在 F-Droid 中查看应用程序时显示给用户的名称。
  * `categories`：应用程序所属的分类。这用于在 F-Droid 中对应用程序进行分类。
  * `description`：在 F-Droid 中查看应用程序时显示给用户的描述。

### `metascoop/`[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#metascoop) <a href="#metascoop" id="metascoop"></a>

Bitwarden 的 F-Droid 仓库配置为在源仓库中检测到新版本时自动更新应用程序，这些源仓库在 `repos.yml` 中定义。

这通过使用 `metascoop` 应用程序来获取源仓库的最新版本，然后更新仓库索引来完成。

`metascoop` 应用程序由 CI/CD 管道定期运行，以确保仓库索引保持最新。

当检测到源代码库中的更改时，任何新的发布版本都将添加到 F-Droid 仓库中，并执行 `fdroid update` 命令以更新 F-Droid 服务器和仓库元数据。CI/CD 管道将自动创建一个拉取请求以更新仓库中的这些更改。

### `fdroid/`[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#fdroid) <a href="#fdroid" id="fdroid"></a>

此目录中的大多数文件由 `metascoop` 应用和 `fdroid` 工具生成。一些文件无法自动生成，必须手动编辑。

#### F-Droid 仓库配置[**​**](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#f-droid-repo-configuration) <a href="#f-droid-repo-configuration" id="f-droid-repo-configuration"></a>

F-Droid 仓库配置在 `config.yml` 文件中。这包括名称、描述和存档设置等详细信息。出于安全考虑，此文件不会被跟踪。

#### 仓库图标[**​**](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#repository-icon) <a href="#repository-icon" id="repository-icon"></a>

F-Droid 仓库图标存储在 `fdroid/icon.png`。

#### 应用程序图片[**​**](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#application-images) <a href="#application-images" id="application-images"></a>

一些应用程序元数据，如应用程序图标、特色图形和截图，未在 `repos.yml` 文件中定义，必须放置在 F-Droid 仓库的正确位置。

以下目录结构用于存储应用程序图像：

* `fdroid/repo/<app-id>/<locale>/icon.png` : 应用程序图标。
* `fdroid/repo/<app-id>/<locale>/feature-graphic.png` : 特色图形。
* 各种设备的截图。即 `fdroid/repo/com.x8bit.bitwarden/en-US/phoneScreenshots/login-screenshot.png` 。

元数据文件结构详情可在官方 F-Droid 文档中找到， [这里 ](https://f-droid.org/en/docs/All_About_Descriptions_Graphics_and_Screenshots/#in-the-f-droid-repo).

### 测试[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#testing) <a href="#testing" id="testing"></a>

#### 本地测试[**​**](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#local-testing) <a href="#local-testing" id="local-testing"></a>

运行 `run_metascoop.sh` 和 `update_repo.sh` 脚本可以手动检查新版本并更新 F-Droid 仓库。这在进行测试时特别有帮助。

当在本地执行 `run_metascoop.sh` 时，需要仓库密钥库，因为 `fdroid update` 作为过程的一部分被执行。

可以通过在 `fdroid` 目录下运行 `fdroid init` 命令并按照提示操作来生成一个临时密钥库。这将生成具有默认值的 `config.yml` 和 `keystore.p12` 文件。

运行以下命令以生成新的密钥库和配置：

```
cd fdroid
fdroid init
```

{% hint style="info" %}
如果您需要访问生产环境的 F-Droid 密钥库和配置，请联系您的经理。
{% endhint %}

{% hint style="danger" %}
请勿推送由本地生成的密钥库或配置签名的更改。

使用本地生成的密钥库或配置将强制重新生成所有元数据和重新签名仓库。这些更改仅应用于本地测试。
{% endhint %}

可以运行本地 F-Droid 服务器进行端到端测试。此类测试需要将您的计算机设置为 Web 服务器，并将整个仓库复制到 Web 根目录中。有关设置本地演示仓库的说明，请参阅官方 F-Droid 文档，\[此处]\([here](https://f-droid.org/docs/Setup_an_F-Droid_App_Repo/#local-demo-repo-howto))。

{% hint style="info" %}
为了从 Android 模拟器连接到您的本地仓库，请使用 `10.0.2.2` 而不是 `localhost`。
{% endhint %}

{% hint style="success" %}
MacOS 用户

如果使用 _nginx_ 作为 Web 服务器，并且使用 Homebrew 安装，Web 根目录位于 `/opt/homebrew/var/www/`。

要启动/停止 _nginx_，请运行：

```
brew services start nginx
brew services stop nginx
```

默认情况下，从 _homebrew_ 启动时，_nginx_ 将监听端口 `8080`。从模拟器进入时，本地服务器 URL 的示例应如下所示： `http://10.0.2.2:8080/fdroid/repo`
{% endhint %}

#### 远程测试[**​**](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#remote-testing) <a href="#remote-testing" id="remote-testing"></a>

可以从 GitHub Actions 标签页触发 `fdroid.yml` 工作流程。勾选“Dry run”复选框可以在不发布更改的情况下运行工作流程。默认情况下，工作流程会将更改发布到 F-Droid 仓库。

## 安全[​](https://contributing.bitwarden.com/getting-started/mobile/android/f-droid/#security) <a href="#security" id="security"></a>

F-Droid 仓库使用 Bitwarden 拥有的证书进行签名。用户可以验证 仓库的有效性通过将签名与提供的指纹进行比较来验证 README.md 文件。
