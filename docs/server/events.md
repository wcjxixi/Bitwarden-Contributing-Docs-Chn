# 事件记录

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/docs/server/events)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* 完成了[服务器设置指南](guide.md)
* 一个[企业组织](https://help.ppgg.in/organizations/organizations#types-of-organizations)

## Azure Queue（云） <a href="#azure-queue-cloud" id="azure-queue-cloud"></a>

Bitwarden 的云实例使用 Azure 队列和表存储来处理事件。以下是它的工作原理：

1. 由用户执行需要被记录的动作
2. 如果事件发生在客户端（例如查看密码），客户端会将事件的详细信息发送到事件服务器项目，然后事件服务器项目调用 `EventService`。如果事件发生在服务器端，则相关项目调用 `EventService` 本身。
3. 事件暂时存储在 Azure Queue Storage（专为处理大量消息而设计）中
4. EventsProcessor 服务器项目运行常规批处理作业以从队列存储中检索事件并将它们保存到表存储中
5. 从表存储中检索事件以供查看

要在本地模拟：

1. 确保您已安装并设置 Azurite，如[服务器设置指南](guide.md#azurite)中所述
2. 确保未设置 `globalSettings:events:connectionString` 用户机密，或具有默认值 `UseDevelopmentStorage=true`
3. 使用 `dotnet run` 或您的 IDE 启动 Events 和 EventsProcessor 项目。（还要确保你有 Api、Identity 和你的 Web 密码库正在运行。）

您现在应该观察到您的企业组织正在记录事件（例如，当创建项目或邀请用户时）。这些应该出现在组织密码库的事件日志部分。

[Azure Storage Exporer](https://learn.microsoft.com/zh-cn/azure/vs-azure-tools-storage-manage-with-storage-explorer) 允许您检查本地队列和表存储的内容，对调试非常有用。

## 数据库存储（自托管） <a href="#database-storage-self-hosted" id="database-storage-self-hosted"></a>

Bitwarden 的自托管实例使用替代的 `EventService` 实现将事件日志直接写入其数据库中的 `Event` 表。

要为事件使用数据库存储：

1. 在[自托管配置](self-hosted.md)中运行您的本地开发服务器（Api、Identity 和 Web 密码库）
2. 使用 `dotnet run` 或您的 IDE 启动 Events 项目（注意：自托管不需要 EventsProcessor）
