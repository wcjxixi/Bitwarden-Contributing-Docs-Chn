# 故障排除

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/troubleshooting)
{% endhint %}

## 构建错误 / npm 依赖错误 <a href="#build-errors-npm-dependency-errors" id="build-errors-npm-dependency-errors"></a>

如果您在尝试构建客户端 App 时收到 npm 依赖错误，请确保您仅在存储库的根目录中运行了 `npm ci`。您不应该在任何其他目录（例如客户端 App 目录）中运行 `npm i` 或 `npm ci`。

您可以通过在 `libs` 和 `apps` 目录中搜索 `node_modules` 来仔细检查：

```bash
find apps -name node_modules
find libs -name node_modules
```

删除这些搜索到的所有结果，然后再次尝试构建 App。

如果您仍然遇到构建错误，您可以尝试删除存储库根目录中的 `node_modules` 文件夹，重新运行 `npm ci`，然后再次构建 App。
