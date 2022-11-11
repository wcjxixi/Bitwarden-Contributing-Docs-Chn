# Firefox 隐私模式

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/clients/browser/ff-private/)
{% endhint %}

您不能使用通常的侧面加载技术在 Firefox 隐私模式下运行附加组件。这是因为它们仅出现在 `about:debugging` 页面中，该页面并没有为您提供在隐私模式下启用扩展的选项。

作为替代方案，您可以使用下面的步骤将开发版本安装为未签名的附加组件。

## 先决条件 <a href="#prerequisites" id="prerequisites"></a>

1. 安装 Firefox 开发者版
2. 配置 Firefox 开发者版：
   * 打开 Firefox 开发者版
   * 导航到 `about:config`
   * 将 `xpinstall.signatures.required` 设置为 `false`（如果该设置项不在列表中，请添加该设置）

## 步骤 <a href="#steps" id="steps"></a>

1. 在命令行上打开本地浏览器存储库
2. 使用 `npm run dist:firefox` 构建并打包浏览器扩展
3. 打开 Firefox 开发者版然后导航到 `about:addons`
4. 点击右上角「管理您的扩展」旁边的齿轮
5. 点击「从文件安装附加组件」
6. 打开本地存储库中的 `dist/dist-firefox.zip`
7. 扩展现在将出现在 about:addons 页面。点击扩展名称将其展开，然后切换「在隐私窗口中运行」。
