# 事件日志

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/deep-dives/event-logs/)
{% endhint %}

Bitwarden 事件日志用于团队和企业组织捕获组织内发生的事件的带时间戳的记录。有关如何查看事件的文档，请参阅[帮助中心](https://help.ppgg.in/admin-console/reporting/event-logs)。

## 事件类型 <a href="#types-of-events" id="types-of-events"></a>

根据事件触发的位置，我们记录的事件可以分为两种不同的类型：

| 触发位置 | 描述                                                                                   |
| ---- | ------------------------------------------------------------------------------------ |
| 服务器  | 服务器端操作的用户操作，通常是与组织管理相关的任何操作。                                                         |
| 客户端  | 用户对本地同步数据的交互不会导致服务器端操作。它们通常带有 `Client_` 前缀。例如 `Cipher_ClientAutofill` ，它记录组织项目的自动填充。 |

只要有可能，我们更喜欢使用服务器端事件，因为它们会产生较少的网络流量，并且无法通过修改客户端来规避。

## 编写事件 <a href="#writing-events" id="writing-events"></a>

客户端上的事件通过 JavaScript 客户端的 [`EventCollectionService`](https://github.com/bitwarden/clients/blob/master/libs/common/src/services/event/event-collection.service.ts) 和 [`EventUploadService`](https://github.com/bitwarden/clients/blob/master/libs/common/src/services/event/event-upload.service.ts) 以及移动客户端的 [`EventService`](https://github.com/bitwarden/mobile/blob/master/src/Core/Services/EventService.cs) 进行处理。这些服务将事件排队到存储在客户端的集合中，并定期上传到服务器，目前间隔为 60 秒。日志也会在注销时上传，因此集合中没有孤立的事件。

已上传的事件日志通过对事件服务上的 `/collect` 端点的 `POST` 请求发送到服务器，该端点由 `CollectController` 处理。控制器在将事件传递到 `EventService` 之前执行一些基本映射。

服务器端事件直接发送到 `EventService` ，完全绕过事件服务。

云实例和自托管实例的 `EventService` 实现有区别。它由 `IEventWriteService` 处理。

### 云托管 <a href="#cloud-hosted" id="cloud-hosted"></a>

对于云托管实例，我们使用 [`AzureQueueEventWriteService`](https://github.com/bitwarden/server/blob/master/src/Core/Services/Implementations/AzureQueueEventWriteService.cs) 实现，它将事件写入 `globalSettings.Events.ConnectionString` 配置设置中指定的 Azure 队列。

然后，Azure 队列中的事件由 Bitwarden 云托管实例中运行的 `EventsProcessor` 服务进行处理。`EventsProcessor` 正在运行的 [`AzureQueueHostedService`](https://github.com/bitwarden/server/blob/master/src/EventsProcessor/AzureQueueHostedService.cs) 将事件日志从 Azure 队列中出列，并使用 [`EventRepository`](https://github.com/bitwarden/server/blob/master/src/Core/Repositories/TableStorage/EventRepository.cs) 将它们写入 Azure 表存储。

### 自托管 <a href="#self-hosted" id="self-hosted"></a>

在自托管实例上， [`RepositoryEventWriteService`](https://github.com/bitwarden/server/blob/master/src/Core/Services/Implementations/RepositoryEventWriteService.cs) 使用 `EventRepository` 直接将事件日志写入 `Events` 数据库表。

## 查询事件 <a href="#querying-events" id="querying-events"></a>

通过 Bitwarden API 上的 [`EventsController`](https://github.com/bitwarden/server/blob/master/src/Api/Public/Controllers/EventsController.cs) 查询事件日志。

与编写事件一样，事件的查询根据 Bitwarden 实例使用的托管方法而有所不同。由于事件记录到不同的位置（Azure 表存储和 Bitwarden SQL 数据库），因此对这些事件的查询也必须有所不同。

我们通过 `Api` 项目中的依赖注入来实现这一点。根据托管环境和使用的数据库提供程序， [`IEventRepository`](https://github.com/bitwarden/server/blob/master/src/Core/Repositories/IEventRepository.cs) 将具有不同的实现。

### 云托管 <a href="#cloud-hosted" id="cloud-hosted"></a>

对于云托管的 Bitwarden 实例， `EventsController` 将通过实现 `IEventRepository` 的 [`Bit.Core.Repositories.TableStorage.EventRepository`](https://github.com/bitwarden/server/blob/master/src/Core/Repositories/TableStorage/EventRepository.cs) 类查询 Azure 表存储以查找事件日志。

### 自托管 <a href="#self-hosted" id="self-hosted"></a>

在自托管 Bitwarden 实例上， `EventsController` 将使用 `IEventRepository` 查询 `Events` 数据库表中的事件日志。
