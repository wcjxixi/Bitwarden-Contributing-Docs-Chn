# C\#

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/code-style/csharp)
{% endhint %}

我们使用 [dotnet-format](https://github.com/dotnet/format) 工具来格式化我们的 C# 代码。像如下运行：

```bash
dotnet format
```

然而，由于它相当初级，我们还遵循一些额外的代码样式指南。

我们尝试遵循 C# 社区标准（有一些例外）。查看以下文章获取总体概述。

1. [https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/inside-a-program/coding-conventions](https://docs.microsoft.com/zh-cn/dotnet/csharp/programming-guide/inside-a-program/coding-conventions)
2. [https://stackoverflow.com/a/310967/1090359](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/inside-a-program/coding-conventionshttps://stackoverflow.com/a/310967/1090359)

## 私有字段 <a href="#private-fields" id="private-fields"></a>

私有字段应使用 camelCase 格式并以 `_` 作为前缀。

示例：

```bash
private readonly IUserService _userService;
```

请参阅以下文章，了解如何配置 Visual Studio 代码生成快捷方式以协助此命名约定：[https://stackoverflow.com/q/45736659/1090359](https://stackoverflow.com/q/45736659/1090359)

## 公共属性 <a href="#public-properties" id="public-properties"></a>

* 属性应使用 PascalCase 格式并且没有前缀
* 属性应拼写出来，而不要使用缩写或简称，例如「OrganizationConfiguration」（正确）与「OrgConfig」（错误）
* 属性应在属性组和以下方法之间包含空行

> \[**译者注**]：PascalCase - 大驼峰命名法。[骆峰式命名法](https://zh.wikipedia.org/zh-my/%E9%A7%9D%E5%B3%B0%E5%BC%8F%E5%A4%A7%E5%B0%8F%E5%AF%AB)是电脑编程时的一套命名规则。当变量名或函数名是由两个或多个单词连结在一起，利用驼峰式命名法来表示，以增加变量和函数的可读性。单词之间不以空格、连接号或下划线等隔开。第一个单词的首字母小写，第二个单词的首字母大写（小驼峰），或者每一个单词的首字母均大写（大驼峰。也被称为 **Pascal 命名法**）。

## 空白区 <a href="#whitespace" id="whitespace"></a>

* 我们对所有代码文件使用空格（不是制表符），包括 C#。缩进应该是标准的 4 个空格。
* 代码文件应在最后的 `}` 后以换行符结尾
* 空行用于分隔每组代码组合类型（字段、构造函数、属性、公共方法、私有方法、子类）

## 构造函数 <a href="#constructors" id="constructors"></a>

* 多个**构造函数**应使用换行符分隔（之间有空行）
* 具有多个参数的构造函数应每行 1 个参数
* 必要时，空的构造函数应全位于为 1 行，即 `public ClassName() { }`

## 控制块 <a href="#control-blocks" id="control-blocks"></a>

* 控制块应始终使用大括号（即使是 1 行大括号）
* `using` 和 `foreach` 块应该用 `var` 声明上下文变量
* 在控制关键字和 `()` 之间始终包含一个空格

## 条件句 <a href="#conditionals" id="conditionals"></a>

当跨多行分隔时，长条件应使用尾随运算符。

```json
// Good example
if (someBooleanExpression &&
    someVariable != null &&
    someVariable.IsTrue)
{
}

// Bad examples (don't do)
if (someBooleanExpression
    && someVariable != null
    && someVariable.IsTrue)
{
}
// Too long, separate
if (someBooleanExpression && someVariable != null && someVariable.IsTrue)
{
}
```
