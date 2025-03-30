# Splunk App

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/business/splunk-app)
{% endhint %}

Bitwarden Splunk App 从 Bitwarden 公共 API 获取事件日志数据，并将其提供给 Splunk。

## 要求[​](https://contributing.bitwarden.com/getting-started/business/splunk-app#requirements) <a href="#requirements" id="requirements"></a>

* Docker。如果您使用的是 Apple Silicon Mac，请启用 _Docker Desktop_ -> _Settings_ -> _General_ -> _Use Rosetta for x86\_64/amd64 emulation on Apple Silicon_
* Python 3.7 - 3.10
* [Poetry](https://python-poetry.org/docs/#installation)
  * 还要使用 `poetry self add poetry-plugin-export` 安装 Poetry 导出插件
* libmagic（仅限 macOS），可通过 homebrew 安装：`brew install libmagic`
* Bitwarden 团队或企业组织
* 如果使用本地开发服务器 - 确保事件和 EventsProcessor 项目正在运行，并且[事件记录](../server/events.md)功能正常

## 设置和配置[​](https://contributing.bitwarden.com/getting-started/business/splunk-app#set-up-and-configuration) <a href="#set-up-and-configuration" id="set-up-and-configuration"></a>

### 配置您的环境[​](https://contributing.bitwarden.com/getting-started/business/splunk-app#configure-your-environment) <a href="#configure-your-environment" id="configure-your-environment"></a>

1.  克隆 Github 存储库：

    ```bash
    git clone https://github.com/bitwarden/splunk.git
    ```
2.  导航到存储库的根目录：

    ```bash
    cd splunk
    ```
3.  告诉 poetry 要使用的 Python 版本：

    ```bash
    poetry env use <executable>
    ```

    其中 `<executable>` 是 Python 的可执行文件。如果它在您的 PATH 变量中，则无需指定完整路径。例如 `poetry env use python3.9`。
4.  安装依赖：

    ```bash
    poetry install --with dev
    ```

### 设置 Splunk Enterprise[​](https://contributing.bitwarden.com/getting-started/business/splunk-app#set-up-splunk-enterprise) <a href="#set-up-splunk-enterprise" id="set-up-splunk-enterprise"></a>

1.  运行 Splunk Enterprise：

    ```bash
    docker compose -f dev/docker-compose.yml up -d
    ```

{% hint style="warning" %}
如果您使用的是 Apple Silicon Mac，则必须使用至高是版本 9.3 的 Splunk。从版本 9.4 开始，Splunk 依赖于 AVX 指令集来使用其 KVStore，而 Apple Silicon 不支持该指令集。
{% endhint %}

请注意这将设置管理员密码为 `password`。仅限开发使用。

2. 通过访问 [http://localhost:8001](http://localhost:8001/) 确认 Splunk 正在运行

### 部署 App <a href="#deploy-the-app" id="deploy-the-app"></a>

1.  打包 App：

    ```bash
    ./package.sh
    ```

    这将生成一个已打包的 Splunk App 到 `output/bitwarden_event_logs.tar.gz` 。
2.  将 App 部署到 Splunk：

    ```
    ./deploy.sh
    ```

    这将重新启动 Splunk，脚本完成后可能需要几秒钟才能再次可用。
3.  （可选）检查日志以查找错误或用于后续调试：

    ```bash
    docker exec -u splunk -it splunk tail -f /opt/splunk/var/log/splunk/bitwarden_event_logs.log
    ```

### 在 Splunk 中配置 App <a href="#configure-the-app-in-splunk" id="configure-the-app-in-splunk"></a>

1. 访问 Splunk Web App：[http://localhost:8001](http://localhost:8001)。
2. 使用用户名 `admin` 和密码 `password` 登录。
3. 点击 _Apps_ -> _Bitwarden Event Logs_。
4. 完成设置。有关配置的更多信息，请参阅 [Bitwarden 帮助中心 ](https://bitwarden.com/help/splunk-siem/)。

{% hint style="warning" %}
Splunk 使用 https，需要额外配置才能与本地开发服务器一起工作。我们还没有这方面的说明。同时，我们建议将 Splunk 配置为使用 Bitwarden 云部署（如生产或内部 QA 环境）。
{% endhint %}

您现在应该可以在 _Apps_ -> _Bitwarden Event Logs_ -> _Dashboards_ 中看到您的组织事件。如果事件日志没有出现，请检查 Splunk 日志（见上文）。
