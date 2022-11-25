# Angular & TypeScript

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/code-style/angular)
{% endhint %}

## HTML

请确保每个输入字段和按钮都有一个描述性 ID。这将方便 QA 更有效地编写测试自动化。

ID 应具有以下三个组件：

* **组件名称**：为确保 ID 的唯一性，我们在它们前面加上组件名称。用很少的改变，同时避免我们多次重复使用相同的组件名称，以保持唯一性。
* **HTML 元素**：这使您可以快速了解我们正在访问的内容。
* **可读名称**：我们正在访问的内容的描述性名称。

请在组件内使用破折号，并使用下划线分隔_组件_。

```
<component name>_<html element>_<readable name>

register_button_submit
register-form_input_email
```

在为组件库编写组件时，有时需要确保 ID 存在，以便正确处理对其他元素的引用的可访问性。可以考虑使用自动生成的 ID，但要确保它可以被覆盖。对自动 ID 使用以下命名约定：

```
<component selector>-<incrementing number>

bit-input-0
```

请确保选择器中的单词使用破折号分隔，并且不要使用 camelCase 格式。

> \[**译者注**]：camelCase - 驼峰命名法。[骆峰式命名](https://zh.wikipedia.org/zh-my/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)法是电脑编程时的一套命名规则。当变量名或函数名是由两个或多个单词连结在一起，利用驼峰式命名法来表示，以增加变量和函数的可读性。单词之间不以空格、连接号或下划线等隔开。第一个单词的首字母小写，第二个单词的首字母大写（小驼峰），或者每一个单词的首字母均大写（大驼峰。也被称为 **Pascal 命名法**）。

## JavaScript / TypeScript <a href="#javascript-typescript" id="javascript-typescript"></a>

我们使用 [Prettier](https://prettier.io/) 和 [ESLint](https://eslint.org/) 来自动格式化和 lint 代码库。每次创建提交时，`npm ci` 都会自动安装 pre-commit 钩子以在您的更改上运行 Prettier 和 ESLint。

或者，您可以手动运行它们：

```
npm run prettier
npm run lint:fix
```

### 角度样式指南 <a href="#angular-style-guide" id="angular-style-guide"></a>

我们通常遵循 [Angular 样式指南](https://angular.io/guide/styleguide)。

### 变量命名 <a href="#variable-naming" id="variable-naming"></a>

对于 `boolean` 变量，使用基本词，**不要**包含前缀，如 `is`、`has` 等，除非没有它就无法传达含义，例如避免与其他属性混淆。

## RxJS

在编写 RxJS 代码时，我们有一些准则，这些准则是使用 [`eslint-plugin-rxjs`](https://github.com/cartant/eslint-plugin-rxjs) 和 [`eslint-plugin-rxjs-angular`](https://github.com/cartant/eslint-plugin-rxjs-angular) 强制执行。这些规则旨在帮助避免常见的 RxJS 陷阱，这些陷阱可能导致 Observables 无法清理或出现意外行为。

### 避免订阅 <a href="#avoid-subscriptions" id="avoid-subscriptions"></a>

只要有可能，我们应该避免显式订阅，而是使用模板中的 `| async` 管道。这将确保在没有任何样板的情况下销毁组件时清理订阅。

为此，我们可以使用 `.pipe` 操作和 rxjs 操作符将输入的 observable 修改为我们可以显示的内容。

研究以下示例，很容易忘记取消订阅 observable，我们也有比我们想要的更多的样板。

```javascript
private destroy$ = new Subject();
public transformed = [];

observable$
  .pipe(takeUntil(this.destroy$))
  .subscribe((v) => {
    transformed = transform(v);
  });

ngOnDestroy() {
  this.destroy$.next();
  this.destroy$.complete();
}

// Template
<div *ngFor="let t of transformed">
  {{ t }}
</div>
```

现在另外研究以下示例，其中我们将订阅替换为 `| async`。

```javascript
transformed$ = observable$.pipe(map(transform));

// Template
<div *ngFor="let t of transformed$ | async">
  {{ t }}
</div>
```

### 使用 `takeUntil` 取消订阅 <a href="#unsubscribe-using-takeuntil" id="unsubscribe-using-takeuntil"></a>

悬空订阅是内存泄漏的常见原因。为了避免这种情况，我们使用了 `prefer-takeUntil` 规则。这要求任何订阅首先通过 `takeUntil` 操作符进行管道传输。

`takeUntil` 模式的主要好处是审阅者可以快速确认订阅是否已清理。

```javascript
private destroy = new Subject<void>();

ngOnInit() {
  this.observable$
    .pipe(takeUntil(this.destroy$))
    // This subscription will automatically be cleaned up when `this.destroy$` emits.
    .subscribe(value => console.log);
}

ngOnDestroy() {
  this.destroy.next();
  this.destroy.complete();
}
```

### 无异步订阅 <a href="#no-async-subscribes" id="no-async-subscribes"></a>

异步订阅很少如您期望的那样工作。它们不是按顺序执行，而是有可能并行执行。这很容易导致意外行为。为避免这种情况，我们的代码库中禁止异步订阅，您需要选择正确的操作。

一些合适的操作符如下：

* [`switchMap`](https://www.learnrxjs.io/learn-rxjs/operators/transformation/switchmap)：取消之前的操作，使其适用于我们在收到新的输入后不关心旧结果的场景。
* [`concatMap`](https://www.learnrxjs.io/learn-rxjs/operators/transformation/concatmap)：按顺序运行异步操作，防止并行和乱序执行。如果我们关心每一个事件的处理，请使用它。
* [`mergeMap`](https://www.learnrxjs.io/learn-rxjs/operators/transformation/mergemap)：请仔细考虑这是否适合您的用例。mergeMap 将展平可观察对象，但不关心顺序。如果排序很重要，请使用 `concatMap`。如果您只关心最新值，请使用 `switchMap`。
