# 事件日志

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/getting-started/server/events)
{% endhint %}

## 要求 <a href="#requirements" id="requirements"></a>

* 完成了[服务器设置指南](guide.md)
* 一个[企业组织](https://help.ppgg.in/organizations/organizations#types-of-organizations)

## Azure Queue（云） <a href="#azure-queue-cloud" id="azure-queue-cloud"></a>

Bitwarden 云实例使用 Azure 队列和表存储来处理事件。以下是它的工作原理：

1. 由用户执行需要被记录的动作
2. 如果事件发生在客户端（例如查看了密码），客户端会将事件的详细信息发送到事件服务器项目，然后事件服务器项目调用 `EventService`。如果事件发生在服务器端，则相关项目调用 `EventService` 本身。
3. 事件暂时存储在 Azure Queue Storage（专为处理大量消息而设计）中
4. EventsProcessor 服务器项目运行常规批处理作业以从队列存储中检索事件并将它们保存到表存储中
5. 从表存储中检索事件以供查看

要在本地模拟：

1. 确保您已安装并设置 Azurite，如[服务器设置指南](guide.md#azurite)中所述
2. 确保未设置 `globalSettings:events:connectionString` 用户机密，或具有默认值 `UseDevelopmentStorage=true`
3. 使用 `dotnet run` 或您的 IDE 启动 Events 和 EventsProcessor 项目。（还要确保您的 Api、Identity 和你的 Web Vault 处于运行状态。）

您现在应该可以观察到您的企业组织正在记录事件（例如，当创建项目或邀请用户时）。这些将出现在组织密码库的事件日志部分。

[Azure Storage Exporer](https://learn.microsoft.com/zh-cn/azure/vs-azure-tools-storage-manage-with-storage-explorer) 允许您检查本地队列和表存储的内容，对调试非常有用。

## 数据库存储（自托管） <a href="#database-storage-self-hosted" id="database-storage-self-hosted"></a>

Bitwarden 的自托管实例使用替代的 `EventService` 实现将事件日志直接写入其数据库的 `Event` 表中。

要为事件使用数据库存储：

1. 在[自托管配置](self-hosted.md)中运行您的本地开发服务器（Api、Identity 和 Web 密码库）
2. 使用 `dotnet run` 或您的 IDE 启动 Events 项目（注意：自托管不需要 EventsProcessor）

## 分布式事件（可选）[​](https://contributing.bitwarden.com/getting-started/server/events#distributed-events-optional) <a href="#distributed-events-optional" id="distributed-events-optional"></a>

事件可以通过 AMQP 消息系统进行分发。该消息系统允许新的集成订阅事件。系统支持 RabbitMQ 或 Azure Service Bus。

### 监听器/处理器模式[​](https://contributing.bitwarden.com/getting-started/server/events#listener--handler-pattern) <a href="#listener--handler-pattern" id="listener--handler-pattern"></a>

将事件迁移到分布式的主要目的是构建额外的服务集成，以消费事件。为了方便支持多个 AMQP 服务（RabbitMQ 和 Azure Service Bus），监听事件流的行为与响应事件的行为是分离的。

#### 监听器 <a href="#listeners" id="listeners"></a>

* 每个通信平台（例如，一个用于 RabbitMQ，一个用于 Azure Service Bus）都有一个监听器。
* 可以配置多个实例独立运行，每个实例都有自己的处理程序和订阅/队列。
* 执行消息平台的设置/拆卸、订阅等所有方面，但不会直接处理任何事件。相反，它们将任务委托给已配置的处理程序。

#### 处理程序 <a href="#handlers" id="handlers"></a>

* 每个集成（例如 HTTP POST 或事件数据库存储库）一个处理程序。
* 完全独立于并不知道所使用的消息平台。这使它们可以在不同的通信平台中自由重用。
* 执行处理事件的所有方面。
* 由于它们与更复杂的消息传递相隔离和分离，因此具有很强的可测试性。

此组合允许在 `Startup.cs` 中进行配置，将当前运行的消息平台监听服务的实例与任何数量的处理器配对。它还允许快速开发新的处理器，因为它们只专注于处理特定事件的任务。

### RabbitMQ 实现[​](https://contributing.bitwarden.com/getting-started/server/events#rabbitmq-implementation) <a href="#rabbitmq-implementation" id="rabbitmq-implementation"></a>

RabbitMQ 实现增加了一个步骤，重构了在本地或自托管运行时处理事件的方式。每个事件都会广播到 RabbitMQ 交换，而不是直接通过 `EventsRepository` 写入 `Events` 表。使用 `EventRepositoryHandler` 配置的新 `RabbitMqEventListenerService` 实例会订阅 RabbitMQ 交换，并通过 `EventsRepository` 写入 `Events` 表。最终结果是相同的（事件存储在数据库中），但 RabbitMQ 交换的添加允许其他集成进行订阅。

为了说明广播事件的性能，一个配置了 `WebhookEventHandler` 的 `RabbitMqEventListenerService` 实例订阅了 RabbitMQ 事件交换机并将每个事件通过 `POST` 发送到可配置的 URL。这是一个简单具体的示例，说明了通过分布式事件如何启用多个集成。

#### 运行 RabbitMQ 容器[​](https://contributing.bitwarden.com/getting-started/server/events#running-the-rabbitmq-container) <a href="#running-the-rabbitmq-container" id="running-the-rabbitmq-container"></a>

1、验证您是否在 `.env` 文件中设置了用户名和密码（请参阅 `.env.example` 以获取示例）

2、使用 Docker Compose 以当前设置运行容器：

```bash
docker compose --profile rabbitmq up -d
```

* Compose 配置使用来自 `env` 文件的用户名和密码。
* 它配置为在 localhost 上以 RabbitMQ 的标准端口运行，但可以在 Docker 配置中进行自定义。

3、要验证其正在运行，请在浏览器中打开 `http://localhost:15672`，并使用您的 `.env` 文件中的用户名和密码登录。

#### 配置服务器以使用 RabbitMQ 处理事件[​](https://contributing.bitwarden.com/getting-started/server/events#configuring-the-server-to-use-rabbitmq-for-events) <a href="#configuring-the-server-to-use-rabbitmq-for-events" id="configuring-the-server-to-use-rabbitmq-for-events"></a>

1、将以下内容添加到您的 `secrets.json` 文件中，将默认值更改为与您的 `.env` 文件匹配：

```yaml
"eventLogging": {
  "rabbitMq": {
    "hostName": "localhost",
    "username": "bitwarden",
    "password": "SET_A_PASSWORD_HERE_123",
    "exchangeName": "events-exchange",
    "eventRepositoryQueueName": "events-write-queue",
    "webhookQueueName": "events-webhook-queue",
  }
  "webhookUrl": "<HTTP POST URL>",
}
```

2、（可选）上面指定的 `webhookQueueName` 和 `webhookUrl` 是可选的。如果它们被定义，则将添加一个 `WebhookEventHandler` 到一个 `RabbitMqEventListenerService` 实例中，该实例将 `POST` 事件到已配置的 URL。

{% hint style="success" %}
[RequestBin](http://requestbin.com/) 提供了一个易于设置的接收这些请求并允许您检查它们的简单服务器。
{% endhint %}

3、重新运行 PowerShell 脚本，将这些秘密添加到每个 Bitwarden 项目中：

```bash
pwsh setup_secrets.ps1
```

4、启动（或重新启动）所有项目以应用新设置

随着这些更改的实施，您应该会看到数据库事件按之前的方式写入，同时您也会在 RabbitMQ 管理界面中看到消息正在通过配置的交换/队列流动。

### Azure Service Bus 实现[​](https://contributing.bitwarden.com/getting-started/server/events#azure-service-bus-implementation) <a href="#azure-service-bus-implementation" id="azure-service-bus-implementation"></a>

Azure Service Bus 实现是 Azure Queue 的可配置替代。不是将事件写入队列等待提取，而是将事件发送到配置的 Service Bus 主题。然后使用 `AzureTableStorageEventHandler` 配置一个实例为 `AzureServiceBusEventListenerService` ，以订阅该主题并将事件写入 Azure 表存储。类似于上面的 RabbitMQ，最终结果相同（事件存储在 Azure 表存储中），但添加 Service Bus 主题允许其他集成进行订阅。

与上面的 RabbitMQ 实现一样，可以配置一个 `WebhookEventHandler` 来运行并通过单独的订阅将事件 POST 到 URL。

#### 运行 Azure Service Bus 模拟器[**​**](https://contributing.bitwarden.com/getting-started/server/events#running-the-azure-service-bus-emulator) <a href="#running-the-azure-service-bus-emulator" id="running-the-azure-service-bus-emulator"></a>

1. 确保您已在本地设置了 Azurite（如之前的用于将事件写入 Azure 表存储的[一般说明](guide.md#azurite)）。此外，这还假设您正在使用 `mssql` 默认配置文件并通过 `.env` 设置了 `${MSSQL_PASSWORD}`&#x20;
2. 使用 Docker Compose 添加/启动本地模拟器：

```bash
docker compose --profile servicebus up -d
```

{% hint style="success" %}
Service Bus 模拟器启动前会等待 15 秒。您可以在 Docker 桌面中查看控制台或运行 `docker logs service-bus` 来验证服务是否启动，然后再启动服务器。
{% endhint %}

#### 配置服务器以使用 Azure Service Bus 处理事件[**​**](https://contributing.bitwarden.com/getting-started/server/events#configuring-the-server-to-use-azure-service-bus-for-events) <a href="#configuring-the-server-to-use-azure-service-bus-for-events" id="configuring-the-server-to-use-azure-service-bus-for-events"></a>

1. 在 `dev` 中的 `secrets.json` 中添加以下内容以配置 Service Bus：

```yaml
	"eventLogging": {
	  "azureServiceBus": {
		"connectionString": "\"Endpoint=sb://localhost;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=SAS_KEY_VALUE;UseDevelopmentEmulator=true;\"",
		"topicName": "event-logging",
		"eventRepositorySubscriptionName": "events-write-subscription",
	  }
	},
```

2. 重新运行机密脚本以发布新的机密

```bash
pwsh setup_secrets.ps1 -clear
```

3. 启动或重新启动所有服务，包括 `EventsProcessor`。

### 配置 webhook（可选）[​](https://contributing.bitwarden.com/getting-started/server/events#configuring-the-webhook-optional) <a href="#configuring-the-webhook-optional" id="configuring-the-webhook-optional"></a>

1. 编辑 `servicebusemulator_config.json` 文件以添加对主 `event-logging` 主题的订阅 topic:

```yaml
{
  "Name": "events-webhook-subscription"
}
```

2. 重新启动 server-bus 容器以应用这些更改
3. 将 webhook 订阅名称和 URL 配置添加到 `secrets.json`

```yaml
  "eventLogging": {
    "azureServiceBus": {
    "connectionString": "\"Endpoint=sb://localhost;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=SAS_KEY_VALUE;UseDevelopmentEmulator=true;\"",
    "topicName": "event-logging",
    "eventRepositorySubscriptionName": "events-write-subscription",
    "webhookSubscriptionName": "events-webhook-subscription"
    },
    "webhookUrl": "<Optional URL here>"
  },
```

4.  发布新密钥到 App：

    ```bash
    pwsh setup_secrets.ps1 -clear
    ```
5. 重启所有服务，包括 `EventsProcessor`
