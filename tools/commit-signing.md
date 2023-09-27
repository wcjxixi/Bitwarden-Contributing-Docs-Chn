# 提交签名

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/commit-signing)
{% endhint %}

为了防止提交欺骗，所有 Bitwarden 开发人员都必须对他们的提交进行数字签名。这是可选的，但鼓励社区贡献者使用。

## 设置提交签名 <a href="#setting-up-commit-signing" id="setting-up-commit-signing"></a>

Github 支持使用 GPG、SSH 和 S/MIME 方式的提交签名。如果您不确定要使用哪种方式，我们推荐 GPG。

1、安装 GnuPG：

{% tabs %}
{% tab title="macOS" %}
```bash
brew install gnupg
echo "export GPG_TTY=$(tty)" >> ~/.zshrc
```
{% endtab %}
{% endtabs %}

2、按照 [Github 文档](https://docs.github.com/en/authentication/managing-commit-signature-verification/about-commit-signature-verification)配置提交签名

3、在下面配置您喜欢的 git 工具

4、将测试提交推送到 Github 并确保「Verified」标记出现在提交描述旁边：

<figure><img src="https://contributing.bitwarden.com/assets/images/commit-signing-bd1537917a2ce059f7bdff988017b829.png" alt=""><figcaption></figcaption></figure>

### 命令行 <a href="#command-line" id="command-line"></a>

配置提交签名后，您可以使用 `-S` 标志对提交进行签名：

```bash
git commit -S
```

为了避免每次都使用 `-S` 标志，您可以默认签署所有提交：

```bash
git config --global commit.gpgSign true 
```

（移除 `--global` 标志以仅将此设置应用于当前存储库）

### Visual Studio 代码 <a href="#visual-studio-code" id="visual-studio-code"></a>

在 Preferences -> Settings -> 搜索「commit signing」以启用提交签名。

#### macOS：GPG 密钥密码短语提示故障 <a href="#macos-gpg-key-passphrase-prompt-issue" id="macos-gpg-key-passphrase-prompt-issue"></a>

一些 macOS 用户在使用 VS Code 时遇到问题，并且 gpg-agent 在使用 VS Code git GUI 时没有提示输入 GPG 密钥密码短语以签署提交。VS Code 显示的错误提示消息：`Git: gpg failed to sign the data` 表明了此故障。

此问题的[解决方法](https://github.com/microsoft/vscode/issues/43809#issuecomment-828773909)是将您的 gpg-agent 配置为使用 macOS 的 [pinentry](https://www.gnupg.org/related\_software/pinentry/index.html) 以强制安全提示。在您选择的终端中运行以下命令：

1. `brew install pinentry-mac`
2. `echo "pinentry-program $(which pinentry-mac)" >> ~/.gnupg/gpg-agent.conf`
3. `killall gpg-agent`

**注意**：您可能需要重新启动 VS Code 才能使其生效，但现在应该会根据需要提示您输入 GPG 密钥密码短语。如果这不能解决您的问题，请按照下面的[故障排除](commit-signing.md#troubleshooting)指南进行操作。

### SourceTree <a href="#sourcetree" id="sourcetree"></a>

请参阅 [Setup GPG to sign commits within SourceTree](https://confluence.atlassian.com/sourcetreekb/setup-gpg-to-sign-commits-within-sourcetree-765397791.html)。

## 故障排除 <a href="#troubleshooting" id="troubleshooting"></a>

如果您收到此错误消息「error: gpg failed to sign the data」，请确保将 `export GPG_TTY=$(tty)` 添加到您的 `~/.zshrc`（或 `~/.bashrc`，如果您使用 bash）并重新启动您的终端。有关此错误的更多帮助，请参阅[此故障排除文档](https://gist.github.com/paolocarrasco/18ca8fe6e63490ae1be23e84a7039374)。
