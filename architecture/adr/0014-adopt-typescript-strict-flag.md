# 0014 - Adopt Typescript Strict flag

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/typescript-strict)
{% endhint %}

| ID：  | ADR-0014   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2022-09-02 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

由于代码库最初是使用 JavaScript 编写的，因此出于兼容性的原因，我们一直无法使用 TypeScript 严格标记。这导致了一些 bug 问题，以及降低了代码质量。

### TypeScript Strict​ <a href="#typescript-strict" id="typescript-strict"></a>

> `strict` 标记支持广泛的类型检查行为，从而更好地保证程序的正确性。打开此标记相当于启用了严格模式系列的所有选项。
>
> _https://www.typescriptlang.org/tsconfig#strict_

特别值得注意的是[严格的空检查](https://www.typescriptlang.org/tsconfig#strictNullChecks)。默认情况下，TypeScript 将忽略 `false`、`null` 和 `undefined` ，允许它们用于任何类型。当启用 `strictNullChecks` 时，必须在类型定义中明确允许它们。

### Typescript 严格模式插件​ <a href="#typescript-strict-mode-plugin" id="typescript-strict-mode-plugin"></a>

[Typescript 严格模式插件](https://github.com/allegro/typescript-strict-plugin)是一个 TypeScript 插件，允许您逐个文件地逐步打开严格模式。这将使我们能够逐步更新代码库以支持严格模式，而无需进行大量的初始工程工作。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

* **保持严格禁用** -- 我们将继续遇到空问题，并且代码质量将低于应有的水平。
* **立即启用严格标记** -- 立即打开严格标记并指派工程师解决由此引起的所有错误。这将需要很长时间，并导致大量冲突。
* **逐步启用严格标记** -- 使用 TypeScript 插件逐一迁移文件。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**逐步启用严格标记**。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 我们可以立即开始使用 strict 标记。
* 降低初始开发人员的工作量。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 迁移会更慢。
* 我们将继续保留部分存在空问题的代码。
* 如果文件是严格的但使用非严格依赖项，则可能会导致错误的安全性。
* 最佳实践较少。

### 待更新文件​ <a href="#files-to-be-updated" id="files-to-be-updated"></a>

* libs/common: 310
  * models: 174
  * services: 40
  * abstractions: 45
* libs/angular: 64
* libs/node: 11
* libs/electron: 10
* apps: 309
  * web: 163
  * browser: 80
  * desktop: 30
  * cli: 36

### 计划​ <a href="#plan" id="plan"></a>

逐步启用严格模式存在几个潜在的隐患。制定一个关于如何应对迁移的计划将受益匪浅。每个类别的文件都应该有一个正确的示例，说明如何迁移它们。在最初的迁移期间，应尽最大努力严格遵守，但我们不应该阻止人们添加不严格的新文件。我们应该防止人们在我们当前正在迁移的类别中添加新的非严格文件。

逐步启用严格模式有几个潜在的隐患。因此，我们需要制定一个迁移计划。每一类文件都应该有一个适当的示例，说明如何迁移它们。在迁移初期，我们应尽力做到严格合规，但不应阻止人们添加非严格的新文件。我们应该防止的是人们在当前正在迁移的类别中添加新的非严格文件。

#### 准备​ <a href="#preparation" id="preparation"></a>

启用插件并运行 `update-strict-comments` ，这会将 `//@ts-strict-ignore` 注释添加到产生严格错误的所有文件中。

#### 过渡​ <a href="#transition" id="transition"></a>

1. 迁移 `libs/common/models` 。
2. 迁移 `libs/common/services` 和 `libs/common/abstractions` 。
3. 迁移剩余的 `libs/common` 。
4. 迁移 `libs/angular` 。
5. 迁移应用程序（并禁止任何人添加新的非严格文件）。

### 指南​ <a href="#guidelines" id="guidelines"></a>

以下是将文件迁移到严格模式的一些有用指南。

#### 避免 null，更喜欢 undefined <a href="#avoid-null-prefer-undefined" id="avoid-null-prefer-undefined"></a>

`strictNullChecks` 标志本质上要求我们在几乎所有类型中添加 `| null`。这很烦人，更好的方法是使用 `undefined` 代替。这允许我们使用 `?` 运算符将字段定义为可选。这实质上是在类型中添加 `| undefined` 。

有关此讨论背后的一些其他背景信息，请参阅[使用 `strictNullChecks` 在 `null` 和 `undefined` 之间进行选择的指南 · 议题 #9653 · microsoft/TypeScript。](https://github.com/microsoft/TypeScript/issues/9653)

#### 使用查找参考/搜索​ <a href="#use-find-references-search" id="use-find-references-search"></a>

在迁移期间，仅允许在文件的子集上使用严格模式。这意味着将接口从 `null` 更改为 `undefined` 可能会导致某些地方仍在使用 `undefined`。请仔细检查 `null` 是否已删除以确保达到预期行为。

#### 避免将非可选字段设为可选​ <a href="#avoid-making-non-optional-fields-optional" id="avoid-making-non-optional-fields-optional"></a>

在迁移代码时，可能很想在所有字段中添加 `?` 。但请仔细考虑该字段是否应该应为可选。例如，数组几乎不应该是 `undefined` ，更合理的默认值是 `[]` 。

#### 使用可选链 <a href="#use-optional-chaining" id="use-optional-chaining"></a>

利用可选链来避免空检查。可选链允许您重新连接以下代码。

```typescript
// Avoid
if (obj == null || obj.field == null) {
  return false;
}
return true;

// Prefer
return obj?.field != null;
```

#### 使用 Nullish 合并运算符 ( `??` ) ​ <a href="#use-nullish-coalescing-operator" id="use-nullish-coalescing-operator"></a>

[https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish\_coalescing\_operator](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Nullish\_coalescing\_operator)

```typescript
const foo = null ?? "default string";
```
