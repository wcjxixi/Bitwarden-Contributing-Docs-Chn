# 0002 - Public API for modules

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/public-module-npm-packages)
{% endhint %}

| ID：  | ADR-0002   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-06-02 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

目前，我们在不同的包之间使用直接文件引用。这导致包中的所有内容都可供我们项目中的任何其他包使用。这就很难确定更改可能产生的潜在副作用，因为它们不是孤立于单个包的。传统上，只有包的特定子集才会作为公共模块公开，这提供了安全性，即更改将在包内部进行，并且最好由其单元测试覆盖。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **直接引用** -- 我们可以决定继续原样。没有公共 API。
* **使用 index.ts 定义公共模块** -- 我们将 `index.ts` 添加到定义了「公共」接口的每一个文件夹中。然后其他包导入根索引文件，并禁止直接引用内部文件。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**使用 index.ts 定义公共模块**。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 公共模块已定义。
* 导入可以保持清爽，因为并非每个文件都需要手动导入。
* 安全性 - 知道更改是与包隔离的。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 每当添加新的导出 API 时，我们都必须更新 `index.ts` 文件。
