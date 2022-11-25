# 客户端

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/clients/)
{% endhint %}

本章节涵盖了各个 Bitwarden Typescript 客户端应用程序的开发信息：

* [Web Vault](web-vault/)
* [浏览器端](browser/)
* [桌面端](desktop/)
* [CLI](cli.md)

在这里，「客户端」通常指的是 Typescript 客户端，它们位于 `client` 单一存储库中。[移动应用程序](../mobile/)位于单独的章节（以及存储库），因为它不共享公共代码。

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您应该已经安装了 Node 和 npm。有关详细信息，请参阅[工具和库](../tools.md)页面。

## 设置说明 <a href="#setup-instructions" id="setup-instructions"></a>

在对任何客户端进行操作之前，您需要克隆和设置 `client` 单一存储库。

1、克隆存储库：

```
git clone https://github.com/bitwarden/clients.git
```

2、安装依赖：

```
npm ci
```

{% hint style="warning" %}
您应该只从存储库的根目录安装依赖。不要尝试为单个客户端应用程序安装依赖。
{% endhint %}

3、配置 git blame 以忽略某些提交（一般是管理性修改，如格式化）：

```
git config blame.ignoreRevsFile .git-blame-ignore-revs
```

4、在 Visual Studio Code 中打开 `client.code-workspace` 文件。这已经被配置为使用[多 root 工作区](https://code.visualstudio.com/docs/editor/multi-root-workspaces)来改善您的开发体验。每个客户端将在左侧的资源管理器面板中显示为自己的工作区。

现在您已准备好继续为您要处理的特定客户提供任何其他说明了。
