# Native Messaging Test Runner

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/clients/desktop/native-messaging-test-runner)
{% endhint %}

Native Messaging Test Runner 是一个 Node 应用程序，用于测试桌面端中的 Native Messaging 功能，特别是从 DuckDuckGo 浏览器接收到的命令。它使用进程间通信 (IPC) 与桌面应用程序进行通信。它的创建是为了支持对本机消息传递本身进行开发，并使 QA 能够测试这些命令。它被放置于 `bitwarden/clients` 存储库中桌面应用程序的根目录的[此处](https://github.com/bitwarden/clients/tree/master/apps/desktop/native-messaging-test-runner)。

## 入门 <a href="#getting-started" id="getting-started"></a>

1、克隆 [bitwarden/clients](https://github.com/bitwarden/clients) 存储库

2、按照[这些](./)说明在本地运行桌面应用程序

3、在正在运行的桌面应用程序中，转到 `Preferences` 然后打开 `Allow DuckDuckGo browser integration` 设置：

{% embed url="https://user-images.githubusercontent.com/8926729/191100543-208fa2f4-8859-4bbf-86f6-5ea0795f504c.png" %}

4、在另一个终端中，导航到 `apps/desktop/native-messaging-test-runner`

5、运行 `npm ci`

6、选择一个命令然后运行它！一个好的开始是 `status`。完整的命令列表可以在本文档的 `Commands` 部分中看到。有的命令带有参数，例如 `create`。运行这些命令时，通过在所有参数前加上两个破折号的方式传递参数：`npm run create -- --name NewLogin!` **注意**，您需要在每个命令之前接受桌面应用程序中的提示。这肯定是一个需要改进的地方。

{% embed url="https://user-images.githubusercontent.com/8926729/191779284-dfce1764-c92a-4728-8aa5-994607741eef.png" %}

{% hint style="warning" %}
这些命令针对您本地正在运行的桌面实例以及您在那里拥有的任何帐户运行的。您需要预先设置您的帐户和密码库，以正确测试这些命令。
{% endhint %}

## 架构和结构 <a href="#architecture-and-structure" id="architecture-and-structure"></a>

### 命令 <a href="#commands" id="commands"></a>

命令文件夹包含可运行的节点脚本/命令。当前，每个本机消息传递命令都有一个文件用于测试。

1. **`handshake`** 发送 `bw-handshake` 命令并与桌面应用程序中的本机消息服务建立通信
   * **参数**：无
   * **示例用法**：`npm run handshake`
2. **`status`** 发送 `bw-status` 命令并返回桌面应用程序中配置的帐户数组。
   * **参数**：无
   * **示例用法**：`npm run status`
3. **`create`** 发送 `bw-credential-create` 命令并使用提供的名称和其他字段的测试数据创建新的登录
   * **参数**：`--name`
   * **示例用法**：`npm run create -- --name NewLoginFromTestRunner`
4. **`update`** 发送 `bw-credential-update` 命令并使用提供的字段更新凭证
   * **参数**：`--name`，`--username`，`--password`，`--uri`，`--credentialId`
   * **示例用法**：`npm run update -- --name UpdateLoginFromTestRunner --username rmaccallum --password dolphin123 --uri google.com --credentialId 8fdd5921-4b10-4c47-9f92-af2b0106d63a`
5. **`retrieve`** 发送 `bw-credential-retrieval` 命令并返回与提供的 uri 匹配的凭据列表
   * **参数**：`--uri`
   * **示例用法**：`npm run retrieve -- --uri google.com`
6. **`generate`** 发送 `bw-generate-password` 命令并使用传递给它的 userId 的设置返回密码/密码短语
   * **参数**：`--userId`
   * **示例用法**：`npm run generate -- --userId fe2af956-a6a6-468c-bc8c-ae6600e48bdd`

### IPCService

该服务管理与套接字的连接以及在该套接字上的消息发送和消息接收。

### NativeMessageService

该服务使用 IPCService 连接到本地运行的 Bitwarden 桌面应用程序的 IPC 代理服务。它使用 Bitwarden 的加密服务和功能来处理消息的加密和解密。它使用位于 `/native-messaging-test-runner/src/variables.ts` 文件中的测试公钥/私钥对。

### 其他 <a href="#other" id="other"></a>

#### 我以为 Node 使用了 JavaScript？我们如何使用 TypeScript 类，包括来自 libs 的类？ <a href="#i-thought-node-used-javascript-how-are-we-using-typescript-classes-including-the-ones-from-libs" id="i-thought-node-used-javascript-how-are-we-using-typescript-classes-including-the-ones-from-libs"></a>

好问题！用一点魔法✨我们将这些 TypeSript 文件编译成 Node 熟悉和喜爱的 JavaScript。它通过以下几个机制完成的：

* **`tsconfig.json`**：在该文件的 `paths` 节点中，将提供的 `paths` 中的文件编译成纯 JavaScript，然后放在 `dist` 文件夹中。
* **`package.json`**：安装了 `module_alias`，它允许我们将 TypeScript 文件中使用的任何服务映射到它们编译的 JavaScript 等价物。在任何命令文件中直接使用的文件的路径被定义在 `package.json` 的 `"_moduleAliases"` 节点中。

#### **Deferred** <a href="#deferred" id="deferred"></a>

覆盖 JavaScript `Promise` 接口的 `resolve` 和 `reject` 方法的默认实现的类，以允许我们等待对通过 IPC 发送的消息的响应。

#### **logUtils** <a href="#logutils" id="logutils"></a>

在整个应用程序中为 `console.log()` 添加漂亮的颜色格式的 Utils 类。

#### race

`IPCService` 在创建允许在等待消息时使用超时的承诺时使用的实用程序。我们不能保证我们会从桌面应用程序中获得响应，因此如果没有及时收到响应，我们可以优雅地取消。

## 故障排除 <a href="#troubleshooting" id="troubleshooting"></a>

* 如果您在测试运行程序使用的服务或编辑命令时看到意外行为，请删除 `native-messaging-test-runner` 顶层的 `dist` 文件夹并重新运行该命令。
* 在运行命令时，如果您正在添加/编辑命令文件时收到 `MODULE_NOT_FOUND` 错误，请确保您已在你的命令文件中 `import "module-alias/register";`。这会将已编译的 JavaScript 类映射到 Typescript 文件中使用的类。

{% embed url="https://user-images.githubusercontent.com/8926729/191111302-8ab7e795-4cd9-4494-965d-67ae7b92cbba.png" %}
