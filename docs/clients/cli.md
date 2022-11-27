# CLI

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/clients/cli/)
{% endhint %}

{% hint style="success" %}
如果您不熟悉 CLI 客户端，Bitwarden 帮助中心有很多[很棒的文档](https://help.ppgg.in/getting-started/bitwarden-cli)可以帮助您熟悉。
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

在开始之前，您必须完成[客户端存储库设置说明](./)。

## 构建说明 <a href="#build-instructions" id="build-instructions"></a>

构建并运行：

```
cd apps/cli
npm run build:watch
```

默认情况下，这将使用官方的 Bitwarden 服务器。您可以使用 [config 命令](https://help.ppgg.in/getting-started/bitwarden-cli#confirm)定位本地服务器。您可能需要[配置节点以使用您的自签名证书](https://help.ppgg.in/getting-started/bitwarden-cli#using-self-signed-certificates)。

## 测试和调试 <a href="#testing-and-debugging" id="testing-and-debugging"></a>

构建位于 `build/bw.js`。您可以使用 node 运行它，例如：

```
node build/bw.js login
```

首先让文件执行尽可能更方便：

```
cd build
chmod +x bw.js
./bw.js login
```

要调试 CLI 客户端，请从 [Javascript 调试终端](https://code.visualstudio.com/docs/nodejs/nodejs-debugging#\_javascript-debug-terminal)运行它并将断点放置在您的 Typescript 代码中。
