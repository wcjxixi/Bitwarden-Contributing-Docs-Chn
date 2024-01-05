# 生成并执行填充脚本

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/autofill/generating-fill-scripts/)
{% endhint %}

本文档假设您已了解[页面详细信息收集](collecting-page-details.md)，它是自动填充流程的第一部分。

一旦 `autofill-init.ts` 内容脚本从页面源收集了页面详细信息后，下一步就是生成我们所说的「填充脚本」。填充脚本是一系列指令，告诉 `autofill-init.ts` 内容脚本要填充哪些字段以及每个字段应该填充哪些数据。

`AutofillService` 的职责生成填充脚本。可以使用三种方法来生成填充脚本：

* `doAutoFill()`
* `doAutoFillOnTab()`
* `doAutoFillActiveTab()`

所有这些方法都将执行相同的 `generateFillScript()` 私有方法来实际生成填充脚本，但单独的方法为语法糖提供不同的入口点。

## 生成填充脚本 <a href="#generating-the-fill-script" id="generating-the-fill-script"></a>

填充脚本的生成发生在 `generateFillScript()` 方法中。我们将在下面更详细地研究脚本是如何生成的。

### 输入 <a href="#input" id="input"></a>

向 `generateFillScript()` 方法提供以下信息：

1. 页面详细信息，表示从 `autofill-init.ts` 页面收集的信息。这包括：
   * AutoFillForm 对象的列表，代表页面上的每个表单
   * AutoFillField 对象的列表，代表页面上的每个字段
2. 页面上填写的 CipherView
3. 用户的填充选项

### 输出 <a href="#output" id="output"></a>

生成例程的结果是填充脚本，它是一系列指令（因此称为「脚本」），用于告诉填充程序对页面执行哪些操作。

该脚本包含具有以下类型的指令数组，每个指令都有一个相应的 `opid` 唯一标识符，用于将其与页面详细信息上的元素联系起来：

| 指令类型            | 描述                         |
| --------------- | -------------------------- |
| `click_on_opid` | 点击 `opid` 代表的元素            |
| `focus_by_opid` | 将焦点设置到 `opid` 代表的元素        |
| `fill_by_opid`  | 用值 `value` 填充 `opid` 代表的元素 |

这些指令的目标是尽我们最大的能力模拟页面如何响应实际用户将值输入到 HTML 元素中。这就是为什么您会经常看到 **Click** -> **Focus** -> **Fill** 序列，这将模拟用户输入数据时会做哪些操作。

### 处理 <a href="#processing" id="processing"></a>

生成脚本的逻辑有两部分：

* 处理自定义字段，以及
* 处理密码特定类型字段

对于自定义字段，逻辑很简单：用户已经定义了字段属性名称，所以我们只需要在 DOM 中找到它即可。如果找到匹配的字段，我们找到它的 `opid` 并向脚本添加三个命令：

1. `click_on_opid`
2. `focus_by_opid`
3. `fill_by_opid`

对于密码特定类型字段，逻辑取决于类型。我们将在下面依次查看每一种类型。

#### 登录 <a href="#login" id="login"></a>

`Login` 类型的核心是用户名和密码。最容易找到的字段通常是密码，因此例程会查找密码字段，然后尝试查找 DOM 中附近最有可能作为候选的用户名字段。

对于从 DOM 收集的页面详细信息中的每一个字段，我们检查以下属性是否包含单词「password」：

* `htmlID`
* `htmlName`
* `placeholder`

这些将是我们要填写在表单上的「密码字段」。

现在，我们遍历页面上的每一个表单。对于每个表单，我们都要检查每个密码字段，找到表单上的密码字段后，然后尝试找到相应的用户名字段。具体方法是查看 DOM 中密码之前的页面详细信息中的所有字段，并找到其中之一：

* 与 `UsernameFieldNames` 列表完全匹配，或者
* DOM 中最接近密码字段的文本、电子邮件或电话字段

如果我们找不到任何包含密码字段的表单，我们将采用页面上的第一个密码字段，并使用其前面的输入字段作为用户名。

**处理没有密码字段的页面**

如果我们在页面上根本找不到任何密码字段，我们会使用「模糊匹配」来查看是否有任何匹配的用户名。我们不想输入任何基于模糊匹配的密码并意外地将其暴露在屏幕上；这些只是用户名。我们使用这个列表来进行模糊匹配。我们移除页面详细信息字段中的所有回车符、换行符和大小写，并与此列表进行比较，以寻找潜在的匹配项。

**添加填充指令**

如果我们能够找到匹配的用户名和密码字段，我们将通过 `opid` 向每个字段的填充脚本添加指令：

1. `click_on_opid`
2. `focus_by_opid`
3. `fill_by_opid`

#### 支付卡 <a href="#card" id="card"></a>

支付卡填充逻辑的目标是查找页面上的字段以匹配典型的信用卡条目：

* 持卡人姓名
* 卡号
* 卡到期月份
* 卡有效期
* 卡安全码 (CCV)
* 卡品牌

为了尝试自动填充支付卡信息，我们循环遍历页面详细信息数组上的每个字段。对于该字段，我们然后循环访问 `CardAttributes` 列表中的所有字段属性：

* `autoCompleteType`
* `data-stripe`
* `htmlName`
* `htmlID`
* `label-tag`
* `placeholder`
* `label-left`
* `label-top`
* `data-recurly`

在 `CreditCardAutoFillConstants` 中，我们为支付卡密码类型的每个字段定义可能匹配值的静态列表。例如，我们定义一个 `CardHolderFieldNames` 数组，其中包含我们要在 DOM 中查找的可能的「Cardholder Name」字段的所有属性值 - 因为我们要使用持卡人姓名填充该字段。我们使用 `AutoFillService` 上的 `isFieldMatch()` 方法来比较每个字段上每个属性的值以查看是否匹配。

如果我们能够找到匹配的字段，我们将为每个字段通过 `opid` 添加指令到填充脚本中：

1. `click_on_opid`
2. `focus_by_opid`
3. `fill_by_opid`

#### 身份 <a href="#identity" id="identity"></a>

身份填充逻辑的目标是查找页面上的字段以匹配 Bitwarden 中定义的身份，其中包含以下字段：

* 标题
* 名
* 中间名字
* 姓
* 地址 1
* 地址 2
* 地址 3
* 城市
* 邮政编码
* 公司
* 电子邮件
* 电话
* 用户名

要尝试自动填充身份信息，我们循环遍历页面详细信息数组上的每个字段。对于该字段，我们然后循环访问 `IdentityAttributes` 列表中的所有字段属性：

* `autoCompleteType`
* `data-stripe`
* `htmlName`
* `htmlID`
* `label-tag`
* `placeholder`
* `label-left`
* `label-top`
* `data-recurly`

在 `IdentityAutoFillConstants` 中，我们为身份密码类型的每个字段定义可能匹配值的静态列表。例如，我们定义一个 `FirstnameFieldNames` 数组，其中包含我们要在 DOM 中查找的可能的「First Name」字段的所有属性值 - 因为我们要使用身份信息的 First Name 来填充该字段。我们使用 `AutoFillService` 上的 `isFieldMatch()` 方法来比较每个字段上的每个属性的值以查看是否匹配。

如果我们能够找到匹配的字段，我们将为每个字段通过 `opid` 添加指令到填充脚本中：

1. `click_on_opid`
2. `focus_by_opid`
3. `fill_by_opid`

## 执行填充 <a href="#performing-the-fill" id="performing-the-fill"></a>

生成脚本后，`AutoFillService` 会发出 `fillForm` 命令。`autofill-init.ts` 内容脚本会侦听该命令，然后根据脚本中的指令在页面上执行填写内容的操作。填充这些内容的逻辑结构位于 `InsertAutofillContentService` 类中。
