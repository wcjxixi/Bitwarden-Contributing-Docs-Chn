# Shadow DOM

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/autofill/shadow-dom)
{% endhint %}

## 简介 <a href="#introduction" id="introduction"></a>

Shadow DOM API 允许将单独的、已封装的 DOM 树嵌入到页面中。

[From MDN](https://developer.mozilla.org/en-US/docs/Web/Web\_Components/Using\_shadow\_DOM)：

> Web 组件的一个重要方面是封装 - 能够隐藏标记结构、样式和行为，以及与页面上的其他代码保持分离，以便不同部分之间不会发生冲突，并且代码可以保持美观和干净。Shadow DOM API 是其中的关键部分，它提供了一种将隐藏的已分离 DOM 附加到元素的方法。
>
> \[...]
>
> Shadow DOM 允许隐藏 DOM 树以便于将其附加到常规 DOM 树中的元素中 - 此 Shadow DOM 树以 Shadow 根开始，在其下面您可以附加任何元素，与普通 DOM 的方式相同。

Shadow DOM API 与我们的自动填充逻辑相关，因为常见的 DOM 查询方法（例如 `document.querySelector` 和 `document.querySelectorAll` ）不会返回位于 Shadow DOM 中的结果。

Shadow 根节点有一个 `mode` 属性，可以设置为 `"open"` 或 `"closed"` 。 `mode` 旨在允许（禁止）主 DOM 访问 Shadow DOM 树，尽管某些浏览器提供了可以忽略此属性的特定 API。

## 我们如何处理 Shadow DOM <a href="#how-we-handle-shadow-doms" id="how-we-handle-shadow-doms"></a>

我们不使用 `querySelector` 方法，而是使用 TreeWalker API 实现类似的逻辑。TreeWalker API 提供了一个单独遍历节点的接口，这意味着我们可以对每个节点运行任意逻辑。在实践中，我们检查每个已访问节点的两个标准是：

1. 节点是否满足提供的过滤器回调 - 这允许我们过滤特定节点，与 `querySelector` 一样，但使用具有完全访问 `Element` 对象的回调，而不是选择器字符串
2. 该节点是否为 Shadow 根

如果该节点是 Shadow 根，我们尝试使用可用的浏览器 API 递归地深入到它：

* **Chrome**：[chrome.dom.openOrClosedShadowRoot](https://developer.mozilla.org/en-US/docs/Mozilla/Add-ons/WebExtensions/API/dom/openOrClosedShadowRoot) - 忽略 `mode` 属性并适用于开放和封闭的 Shadow 根
* **Firefox**：[Element.openOrClosedShadowRoot](https://developer.mozilla.org/en-US/docs/Web/API/Element/openOrClosedShadowRoot) - 忽略 `mode` 属性并适用于开放和封闭的 Shadow 根
* **Safari 和其他浏览器**：[Element.shadowRoot](https://developer.mozilla.org/en-US/docs/Web/API/Element/shadowRoot) - 尊重 `mode` 属性，并且仅适用于 `"open"` Shadow 根。如果其他 API 不可用，我们会回退到此方法
