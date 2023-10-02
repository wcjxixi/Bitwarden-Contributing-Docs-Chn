# 服务器架构

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/server/)
{% endhint %}

## CQRS ([ADR-0008](https://contributing.bitwarden.com/architecture/adr/server-CQRS-pattern)) <a href="#cqrs-adr-0008" id="cqrs-adr-0008"></a>

我们的服务器架构使用命令和查询职责分离 ([Command and Query Responsibility Segregation - CQRS](https://learn.microsoft.com/zh-cn/azure/architecture/patterns/cqrs)) 的模式。

此模式的主要目标是分解专注于单个实体的大型服务（例如 `CipherService` ），并转向基于操作或任务的更小、可重用的类（例如 `CreateCipher` ）。将来，这可能会带来其他好处，例如将命令排队执行，但目前的重点是拥有更小的、可重用的代码块。

### 命令与查询 <a href="#commands-vs-queries" id="commands-vs-queries"></a>

**命令**是写入操作，例如 `RotateOrganizationApiKeyCommand` 。他们永远不应该从数据库中读取数据。

**查询**是读取操作，例如 `GetOrganizationApiKeyQuery` 。他们永远不应该写入数据到数据库。

数据库是我们处理的最常见的数据源，但其他数据源也是可能的。例如，查询还可以从远程服务器获取数据。

每个查询或命令应该有一个单一的职责。例如：删除用户、获取许可证文件、轮换 API 密钥。它们是围绕动词或动作（例如 `RotateOrganizationApiKeyCommand` ）而不是域名或实体（例如 `ApiKeyService` ）设计的。

### 编写命令或查询 <a href="#writing-commands-or-queries" id="writing-commands-or-queries"></a>

一个简单的查询可能只是一个从数据库获取数据的存储库调用。（我们已经使用存储库，这不是我们在这里关心的）但是，更复杂的查询可能需要围绕存储库调用的附加逻辑，这将需要它们自己的类。命令总是需要自己的类。

类、接口和公共方法应以操作命名。例如：

```javascript
namespace Bit.Core.OrganizationFeatures.OrganizationApiKeys;

public class RotateOrganizationApiKeyCommand : IRotateOrganizationApiKeyCommand
{
  public async Task<OrganizationApiKey> RotateApiKeyAsync(OrganizationApiKey organizationApiKey)
  {
    ...
  }
}
```

查询/命令应该只公开运行完整操作的公共方法。它不应该有公共助手方法。

目录结构和命名空间应按功能组织。接口应存储在单独的子文件夹中。例如：

```
  Core/
    └── OrganizationFeatures/
        └── OrganizationApiKeys/
            ├── Interfaces/
            │   └── IRotateOrganizationApiKeyCommand.cs
            └── RotateOrganizationApiKeyCommand.cs
```

### 维护命令/查询的卓越性 <a href="#maintaining-the-command-query-distinction" id="maintaining-the-command-query-distinction"></a>

通过分离读写操作，CQRS 鼓励我们保持类之间的松散耦合。在我们的代码库中使用 CQRS 时，需要遵循两条黄金法则：

* **命令永远不应该读取，查询永远不应该写入**
* **命令和查询不应该互相调用**

这两者都会导致类之间的紧密耦合，减少代码重用的机会，并将命令/查询的区别混为一谈。

通常可以通过以下方式避免这些问题：

* 编写命令，以便它们在参数中接收所需的所有数据，而不是自己获取数据
* 按顺序调用查询和命令（一个接一个），沿调用链传递结果

例如，如果我们需要更新组织的 API 密钥，可能会倾向于使用 `UpdateApiKeyCommand` 来获取当前 API 密钥然后更新它。但是，我们可以将其分解为两个单独的查询/命令，分别调用：

```javascript
var currentApiKey = await _getOrganizationApiKeyQuery.GetOrganizationApiKeyAsync(orgId);
await _rotateOrganizationApiKeyCommand.RotateApiKeyAsync(currentApiKey);
```

这也有单元测试的好处 - 您可以简单地使用 `Autodata` 属性提供不同的参数值，而不是在模拟查询结果时进行冗长的「编排」阶段。

### 避免 [Primitive Obsession](https://refactoring.guru/smells/primitive-obsession) <a href="#avoid-primitive-obsession" id="avoid-primitive-obsession"></a>

在实际情况下，您的命令和查询应该获取并返回整个对象（例如 `User` ）而不是单个属性（例如 `userId` ）。

### 避免过多的可选参数 <a href="#avoid-excessive-optional-parameters" id="avoid-excessive-optional-parameters"></a>

过多的可选参数很快就会变得难以使用。相反，请考虑使用方法重载来为命令或查询提供不同的入口点。
