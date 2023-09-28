# 命名约定

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/contributing/testing/unit/naming-conventions)
{% endhint %}

{% hint style="info" %}
本节章节目前只涉及使用 Typescript 编写的客户端测试。本节无意用于当前形式的使用 C# 编写的服务器端测试，即使其基本原理可能仍然适用。
{% endhint %}

名称的主要目的是以一种易于理解的方式来描述测试，让人明白测试是做什么的，以及它打算覆盖哪些用例。一个好的测试名称列表能让开发人员快速检查套件是否涵盖了所有重要用例，并在未涵盖时轻松找出漏洞。

描述测试的方法有很多，但大多数方法的共同点是都包括以下内容：

* 被测单元
* 被测状态
* 预期行为

因此，我们选择使用以下格式（ `[]` 中内容表示可选）：

`<unit> [given <prerequisite>] <behavior> when <state>`

## 基本示例

#### 示例 1

* **单元**： `FormSelectionList.deselectItem`
* **状态**：调用了有效 ID
* **行为**：创建 `selectedItems` 和 `deselectedItems` 数组的新副本

#### 示例 2

* **单元**： `FormSelectionList.deselectItem`
* **状态**：调用了无效 ID
* **行为**：什么也不做

#### 实践

您可能已经注意到，我们的测试框架 `jest` 鼓励编写以 `it` 开头的测试。因此，我们选择了遵循这一模式的约定。下面是使用该约定编写的示例：

```
FormSelectionList
  deselectItem
    ✓ creates new copies of the selectedItems and deselectedItems arrays when called with a valid id
    ✓ does nothing when called with a invalid id
```

在代码中看上去类似这样：

```typescript
describe("FormSelectionList", () => {
  describe("deselectItem", () => {
    it("creates new copies of the selectedItems and deselectedItems arrays when called with a valid id", () => {...});
    it("does nothing when called with an invalid id", () => {...});
  })
})
```

## 测试中的状态排列和复杂状态

有时，我们的测试需要大量代码来设置状态，而这些代码中通常有一部分与理解测试本身无关，例如全局对象的复杂构造函数。

#### 重复的代码和函数工具 <a href="#duplicated-code-and-utility-functions" id="duplicated-code-and-utility-functions"></a>

大多数情况下，只需创建测试实用功能，就能去除重复代码和无关细节。以下面代码段中的 `createCipher` 函数为例：

```typescript
describe("VaultFilter", () => {
  describe("filterFunction", () => {

    it("returns true when cipher is deleted and function is filtering for trash", () => {
      const cipher = createCipher({ deletedDate: new Date() });
      const filterFunction = createFilterFunction({ status: "trash" });

      const result = filterFunction(cipher);

      expect(result).toBe(true);
    });

    it("returns false when cipher is deleted and function is filtering for favorites", () => {
      const cipher = createCipher({ deletedDate: new Date() });
      const filterFunction = createFilterFunction({ status: "favorites" });

      const result = filterFunction(cipher);

      expect(result).toBe(false);
    });

  })
})

function createCipher(options: Partial<CipherView> = {}) {
  const cipher = new CipherView();

  cipher.favorite = options.favorite ?? false;
  cipher.deletedDate = options.deletedDate;
  cipher.type = options.type;
  cipher.folderId = options.folderId;
  cipher.collectionIds = options.collectionIds;
  cipher.organizationId = options.organizationId;

  return cipher;
}

function createFilterFunction(...) {...}
```

正如您所看到的， `createCipher` 实用程序隐藏了大量代码，否则这些代码将在两个测试中重复出现。

请注意，该功能还允许我们隐藏与我们试图验证的行为无关的细节。测试清楚地表明， `deletedDate` 字段是唯一会对被测单元的行为产生影响的字段。

#### 共享状态和通用设置模块 <a href="#shared-state-and-common-setup-blocks" id="shared-state-and-common-setup-blocks"></a>

在某些情况下，多个测试的部分状态是相同的，因为它们共享一组共同的前提条件。在这种情况下，我们可以使用 `describe` 和 `beforeEach` 块将测试分组，并设置它们共享的状态的共同部分。您可能已经注意到，前面的代码段就是一个很好的例子，其中两个测试都需要删除密码。我们可以像这样将这些测试分组（注意 `given` 关键字）：

```typescript
describe("VaultFilter", () => {
  describe("filterFunction", () => {

    describe("given a deleted cipher", () => {
      let cipher;

      beforeEach(() => {
        cipher = createCipher({ deletedDate: new Date() });
      })

      it("returns true when filtering for trash", () => {
        const filterFunction = createFilterFunction({ status: "trash" });

        ...
      });

      it("returns false when filtering for favorites", () => {
        const filterFunction = createFilterFunction({ status: "favorites" });

        ...
      });
    });

  })
})

function createCipher(...) {...}
function createFilterFunction(...) {...}
```

结果是测试名称看起来像这样：

```
VaultFilter
  filterFunction
    given a deleted cipher
      ✓ returns true when filtering for trash
      ✓ returns false when filtering for favorites
```

## 陷阱 <a href="#pitfalls" id="pitfalls"></a>

### 验证一个行为 <a href="#verify-one-behavior" id="verify-one-behavior"></a>

如果您发现自己在编写冗长的名称时，使用了「_and_」一词将多个期望串联在一起，那么请考虑将测试拆分开来。理想情况下，测试应验证单一行为，以便开发人员更容易发现问题，审核人员也更容易理解修改测试的原因。

#### 示例 <a href="#example" id="example"></a>

* `adds item to selectedItems, removes from deselectedItems, and creates a form control when called with a valid id`

可以写成三个不同的测试：

* `adds item to selectedItems when called with a valid id`
* `removes item from deselectedItems when called with a valid id`
* `creates a form control when called with a valid id`

请记住，最终还是要由开发人员根据具体情况来决定什么算作「验证单一行为」。

### 包含被测状态 <a href="#include-state-under-test" id="include-state-under-test"></a>

一个容易陷入的常见模式是在编写测试名称时不考虑被测试状态。下面是一个虚构的排序函数的例子：

#### 无状态示例 <a href="#example-without-state" id="example-without-state"></a>

* `returns items in alphabetic order`
* `returns empty array`
* `throws error`

我们并不能立即看出我们实际测试了多少种行为，相反，我们是根据预期输出对测试进行分组的。要了解排序功能是如何工作的，以及我们是否有足够的覆盖率，我们必须深入到测试的源代码中。您可能会提出以下问题

* 如果我输入一个包含非字符串值的数组，会发生什么情况？会出错吗？
* 是什么原因导致函数返回一个空数组？
* 如果输入 `null` 呢？

如果我们把状态也加进来，就不得不分开测试，并正确回答上述问题：

#### 考虑状态时的示例 <a href="#example-when-taking-state-into-consideration" id="example-when-taking-state-into-consideration"></a>

* `returns empty array when input is empty`
* `returns empty array when input contains non-string values`
* `returns item when input only contains one item`
* `returns items in alphabetic order when input contains multiple items`
* `throws error when input is not an array`
