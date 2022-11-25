# 贡献指南

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/)
{% endhint %}

## 如何贡献 <a href="#how-to-contribute" id="how-to-contribute"></a>

我们欢迎各种类型的贡献！

请访问我们的[社区论坛](https://community.bitwarden.com/)，了解一般的社区讨论和开发路线图。

以下是您可以参与的方式：

* **请求新功能**：转到社区论坛的 [Feature Requests category](https://community.bitwarden.com/c/feature-requests/) 。请在创建新功能请求之前搜索现有功能请求
* **为新功能编写代码**：在社区论坛的 [Github Contributions category](https://community.bitwarden.com/c/github-contributions/) 中发布新帖子。包括对您提议的贡献的描述、截图和任何相关功能请求的链接。这有助于在您开始编写代码之前从社区和 Bitwarden 团队成员那里获得反馈
* **报告错误或提交错误修复**：使用 Github 话题和拉取请求
* **编写文档**：向 [Bitwarden 帮助存储库](https://github.com/bitwarden/help)提交拉取请求
* **帮助其他用户**：转到社区论坛上的 [Ask the Bitwarden Community category](https://community.bitwarden.com/c/support/)&#x20;
* **翻译**：请参阅下面的本地化 (i10n) 部分
* **报告安全问题或漏洞**：欢迎安全审核和反馈。如果问题很敏感，请私下联系我们或通过我们的 [HackerOne 计划](https://hackerone.com/bitwarden/)提交报告。您可以在下面阅读我们的安全政策。

### 贡献者协议 <a href="#contributor-agreement" id="contributor-agreement"></a>

如果您打算为任何 Github 存储库做出贡献，请签署[贡献者协议](https://cla-assistant.io/bitwarden/clients)。除非作者签署了贡献者协议，否则拉取请求不会被接受和合并。

### 拉取请求指南 <a href="#pull-request-guidelines" id="pull-request-guidelines"></a>

* 在提交拉取请求之前使用 `npm run lint` 并修复任何 linting 建议
* 向 `master` 分支提交任何拉取请求
* 包含指向您的社区论坛帖子的链接

## 本地化 (l10n) <a href="#localization-l10n" id="localization-l10n"></a>

我们使用一个名为 [Crowdin](https://crowdin.com/) 的翻译工具来帮助管理我们跨多种不同语言的本地化工作。

如果您有兴趣帮助将 Bitwarden 应用程序翻译成另一种语言（或进行翻译更正），请在 Crowdin 注册一个帐户并在此处加入我们的项目：

* [https://crowdin.com/project/bitwarden-browser](https://crowdin.com/project/bitwarden-browser)
* [https://crowdin.com/project/bitwarden-desktop](https://crowdin.com/project/bitwarden-desktop)
*  [https://crowdin.com/project/bitwarden-mobile](https://crowdin.com/project/bitwarden-mobile)
*  [https://crowdin.com/project/bitwarden-web](https://crowdin.com/project/bitwarden-web)

如果您有兴趣翻译的语言尚未列出，请在 Crowdin 上创建一个新帐户，加入项目并[联系项目的所有者](https://crowdin.com/profile/dwbit)。

您可以在此处阅读 Crowdin 的译者入门指南：[https://support.crowdin.com/crowdin-intro/](https://support.crowdin.com/crowdin-intro/)。

## 安全政策 <a href="#security-policy" id="security-policy"></a>

Bitwarden 认为，与全球的安全研究人员合作对于确保我们的用户安全至关重要。如果您认为您在我们的产品或服务中发现了安全问题，我们鼓励您通过我们的 [HackerOne 计划](https://hackerone.com/bitwarden/)提交报告。我们欢迎与您合作，尽快解决问题。提前致谢！

### 披露政策 <a href="#disclosure-policy" id="disclosure-policy"></a>

* 一旦发现潜在的安全问题，请尽快通知我们，我们将尽一切努力快速解决问题。
* 在向公众或第三方披露任何信息之前，请为我们提供合理的时间来解决问题。如果合适，我们可能会在解决问题之前公开披露该问题。
* 真诚地努力避免侵犯隐私、破坏数据以及中断或降低我们的服务。仅与您拥有的帐户或经帐户持有人的明确许进行交互。
* 如果您想加密您的报告，请使用长 ID 为 `0xDE6887086F892325FEC04CC0D847525B6931381F` 的 PGP 密钥（在公共密钥服务器池中可用）。

在研究过程中，我们希望您不要：

* 拒绝服务
* 垃圾邮件
* Bitwarden 员工或承包商的社会工程（包括网络钓鱼）
* 对 Bitwarden 财产或数据中心的任何物理尝试

### 我们想帮助你！ <a href="#we-want-to-help-you" id="we-want-to-help-you"></a>

如果您有一些您认为接近被利用的东西，或者如果您想了解有关内部 API 的一些信息，或者通常对您想努力提供帮助的应用程序有任何疑问，请通过 [https://bitwarden](https://bitwarden.com/contact) 联系我们并询问该信息。如上所述，Bitwarden 希望帮助您发现问题，并且非常愿意提供帮助。

感谢您帮助保护 Bitwarden 和我们的用户的安全！
