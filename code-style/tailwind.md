# Tailwind

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/code-style/tailwind/)
{% endhint %}

我们目前正在将基于 Web 的客户端迁移到 Tailwind。目前涉及 web vault，但长期计划是迁移桌面端和浏览器。

在开始使用 Tailwind 之前，我们建议您熟悉 [Utility-First Fundamentals](https://tailwindcss.com/docs/utility-first)。博客文章 [CSS Utility Classes and "Separation of Concerns"](https://adamwathan.me/css-utility-classes-and-separation-of-concerns/) 也是一篇很好的读物，可以更好地理解 Utility first CSS 框架背后的动机和目标。

我们还建议使用 [tailwind 文档](https://tailwindcss.com/)的搜索功能来查找类和示例。

## Bitwarden 的 Tailwind <a href="#tailwind-at-bitwarden" id="tailwind-at-bitwarden"></a>

我们已经定义了我们自己的 Tailwind 配置，它严格限制了颜色的使用，以支持多个主题。为了实现这一点，我们将 CSS 变量与 tailwind 配置结合使用。这使我们能够支持比 Tailwind 中内置的深色/浅色更多。

为此，我们不鼓励使用任意值，唯一的例外是支持现有的 Bootstrap 样式。在这种情况下，它应该被记录并添加为技术债务，作为从 Bootstrap 迁移的一部分来解决。

{% hint style="info" %}
所有 Tailwind 类都需要以 tailwind 配置中定义的 `tw-` 为前缀。用法示例：`<div class="tw-bg-background-alt2"> ... </div>`
{% endhint %}

### 组件 <a href="#components" id="components"></a>

由于 Tailwind 是一个实用优先的 CSS 框架，以避免代码重复，这会使设计难以维护，我们大量使用 Angular 组件来封装孤立的设计块。在大多数情况下，这些块是[表示组件](https://angular-training-guide.rangle.io/state-management/ngrx/component\_architecture)。

### 组件库 <a href="#component-library" id="component-library"></a>

Bitwarden 的一项工程计划是[组件库](https://github.com/bitwarden/clients/tree/master/libs/components)，旨在封装最常用的核心组件。

#### Storybook

我们使用 [Storybook](https://storybook.js.org/) 来独立开发组件。要启动 Storybook 开发独立的组件，请在 `client` 存储库的根目录中运行 `npm run storybook` 命令。

#### Chromatic

{% hint style="info" %}
本部分仅适用于 Bitwarden 开发人员。
{% endhint %}

对组件库的更改有一个额外的审查过程，设计师需要批准视觉更改。我们使用一个叫做 [Chromatic](https://www.chromatic.com/builds?appId=622b79f994de1f003a54ce6f) 的工具来跟踪这些变化，GitHub Bitwarden 组织的成员可以使用 GitHub 登录到 Chromatic 来查看视觉变化。

如果您的 PR 将 **UI Review** 审核为待处理或被拒绝，请联系 Design 以使您的更改获得批准。

{% embed url="https://contributing.bitwarden.com/code-style/chromatic.png" %}
