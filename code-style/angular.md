# Angular & TypeScript

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/code-style/angular/)
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

> \[**译者注**]：camelCase - [驼峰命名法](https://zh.wikipedia.org/zh-my/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)。骆峰式命名法是电脑编程时的一套命名规则。当变量名或函数名是由两个或多个单词连结在一起，利用驼峰式命名法来表示，以增加变量和函数的可读性。单词之间不以空格、连接号或下划线等隔开。第一个单词的首字母小写，第二个单词的首字母大写（小驼峰），或者每一个单词的首字母均大写（大驼峰）

## JavaScript / TypeScript <a href="#javascript-typescript" id="javascript-typescript"></a>

### 角度样式指南 <a href="#angular-style-guide" id="angular-style-guide"></a>

### 变量命名 <a href="#variable-naming" id="variable-naming"></a>

## RxJS

### 避免订阅 <a href="#avoid-subscriptions" id="avoid-subscriptions"></a>

### 使用 `takeUntil` 取消订阅 <a href="#unsubscribe-using-takeuntil" id="unsubscribe-using-takeuntil"></a>

### 无异步订阅 <a href="#no-async-subscribes" id="no-async-subscribes"></a>
