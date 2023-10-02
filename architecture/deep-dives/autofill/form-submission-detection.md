# 表单提交检测

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/autofill/form-submission-detection)
{% endhint %}

## 为什么表单提交检测很重要？ <a href="#why-is-form-submission-detection-important" id="why-is-form-submission-detection-important"></a>

表单提交检测很重要，原因如下：

* 它允许 Bitwarden 扩展提示用户向其密码库添加新的凭据，用户在 Bitwarden 中的足迹。
* 它使我们能够准确处理密码更改检测并帮助用户保持数据同步，从而提供更好的用户体验。

## 为什么表单提交检测很难？ <a href="#why-is-form-submission-detection-difficult" id="why-is-form-submission-detection-difficult"></a>

表单检测及其提交之所以困难，是因为现代渐进式 Web 应用程序 (Progressive Web App - PWA) 和单页应用程序 (Single-page Application - SPA) 具有很大的灵活性。在经典 HTML 中， `<form>` 元素与将所有表单数据提交给用户的 `<submit>` 一起定义。

另一方面，在现代应用程序中， `<form>` 元素可用于收集用户数据，但完全独立的 API 请求将表单数据发送到服务器。要全面了解其复杂性，请参阅[这篇](https://developer.mozilla.org/en-US/docs/Learn/Forms/Sending\_forms\_through\_JavaScript)优秀的文档。

## 表单提交检测涉及什么？ <a href="#what-is-involved-in-form-submission-detection" id="what-is-involved-in-form-submission-detection"></a>

### 初始化收集 <a href="#initial-collection" id="initial-collection"></a>

我们必须准确地收集页面详细信息，以捕获页面上存在表单的事实。

### DOM 操作 <a href="#dom-manipulation" id="dom-manipulation"></a>

我们必须处理应用程序根据用户交互修改 DOM 的情况。

### 提交检测 <a href="#submission-detection" id="submission-detection"></a>

我们必须能够检测表单何时提交，以便我们可以正确通知用户保存他们的凭据。

## 现在是如何处理的？ <a href="#how-is-this-handled-today" id="how-is-this-handled-today"></a>

表单提交检测在 `notificationBar.ts` 内容脚本中处理。

### 检测新表单的 DOM 更改 <a href="#detecting-dom-changes-for-new-forms" id="detecting-dom-changes-for-new-forms"></a>

`MutationObserver` 附加到窗口并每 1000 毫秒运行一次。它观察 DOM 是否有任何更改，包括尚未收集的表单元素（使用表单上的 `data-bitwarden-watching` 数据元素来查看是否已收集）。

如果检测到新的表单元素，则会触发[页面详细信息收集](collecting-page-details.md)过程以检索页面详细信息。

### 检测表单提交 <a href="#detecting-form-submission" id="detecting-form-submission"></a>

通过将事件附加到 DOM 上的每个表单 `submit()` 按钮来检测表单提交。这是在 `notificationBar.ts` 内容脚本收到 `notificationBarPageDetails` 命令后完成的。
