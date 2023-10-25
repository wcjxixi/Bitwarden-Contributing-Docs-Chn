# \*Ingress 隧道

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/tunnel)
{% endhint %}

有时，允许其他团队成员访问本地运行的 Bitwarden 实例也是有用处的。通常这需要在防火墙中打开端口，即便如此，通常也只能通过 IP 地址进行连接。

## 配置网络 <a href="#configure-web" id="configure-web"></a>

如果目标是公开本地网络保险库（包括对大多数服务的访问），那么网络保险库就需要配置为不使用 `https`，而是以未加密的方式提供内容。

打开 `webpack.config.js` 并注释掉 `const devServer = {` 中的以下几行：

```typescript
https: {
  key: fs.readFileSync('dev-server' + certSuffix + '.pem'),
  cert: fs.readFileSync('dev-server' + certSuffix + '.pem'),
},
```

并将域添加到 `local.json` 中的 `allowedHosts`：

```json
{
  "allowedHosts": ["<super-secret-tunnel>"]
}
```

## Cloudflare Argo 隧道 <a href="#cloudflare-argo-tunnels" id="cloudflare-argo-tunnels"></a>

另一种方法是使用 [Cloudflare Argo Tunnels](https://www.cloudflare.com/products/tunnel/)，这种方法有一些好处。它的工作原理是在 Cloudflare 和本地计算机之间建立一个本地隧道，以便访问本地运行的服务。此外，该隧道还可以放置在提供有效 SSL 证书的 Cloudflare 代理后面，因此非常适合移动应用程序的测试。

### 设置 <a href="#setup" id="setup"></a>

1. 安装 `cloudflared`。请访问 [https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation](https://developers.cloudflare.com/cloudflare-one/connections/connect-apps/install-and-setup/installation)。
2. 启动本地网络服务器，并注意其运行的 `$PORT`
3. 使用 `cloudflared tunnel --url http://127.0.0.1:$PORT` 启动隧道

Cloudflare 将为您建立一个隧道，并提供其 URL：`*.trycloudflare.com`。在尝试访问之前，请等待 DNS 开始解析。

**注意**：任何拥有此 URL 的人都可以在你的机器上访问转发的 URL。

## Ngrok（需要免费账户） <a href="#ngrok-requires-a-free-account" id="ngrok-requires-a-free-account"></a>

1、导航至 [https://ngrok.com/](https://ngrok.com/) 并注册账户

2、请按照[官方说明](https://dashboard.ngrok.com/get-started/setup)下载。或使用 [brew](https://formulae.brew.sh/cask/ngrok) 安装，它支持一个账户多个实例。

3、使用 ngrok 暴露本地端口：

```bash
ngrok http <port>
```

4、ngrok 的界面将显示「转发中」URL，例如：

```
https://abcd-123-456-789.au.ngrok.io -> http://localhost:<port>
```

5、通过导航到转发 URL，并在末尾添加 `/alive` 来验证转发 URL 是否有效。例如， `https://abcd-123-456-789.au.ngrok.io/alive` ..

{% hint style="info" %}
任何拥有此 URL 的人都可以在你的机器上访问转发的 URL。
{% endhint %}
