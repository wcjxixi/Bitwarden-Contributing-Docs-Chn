# 0021 - Logging to Standard Output

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/standard-output-logging)
{% endhint %}

| ID：  | ADR-0021   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2023-07-13 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

随着服务器平台的成熟，各种日志扩展也在不断发展，以支持更多的用例和客户要求。[Serilog](https://serilog.net/) 通过共享「核心」逻辑为所有服务提供服务，并在启动时启动，随着时间的推移，还为特定用例添加了额外的「接收器」，以及所需的配置和下游依赖关系，从而增加了核心库的规模。

为了使接收器依赖项保持最新，维护需求不断增长，并且需要添加更多依赖项。目前可用的一些接收器使用率很低，而且/或者现在已经有了更好的替代品。关于如何以及何时使用某些类型的结构化日志记录的条件越来越多。特定于服务的配置、谓词和过滤器的存在，因此很难知道将记录什么内容以及何时记录。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

> Bitwarden 目前使用 Datadog 作为其监控工具，并希望全面提高工程师的使用率，以改进我们的交付。

* **维护当前的日志记录方案**-- 支持当前可用的日志记录方法，并希望运行平台的人员在平台之外配置他们所需的日志收集方法。
* **扩展平台以专门支持 Datadog** -- [存在](https://www.nuget.org/packages/serilog.sinks.datadog.logs) Serilog 接收器，平台可直接向 Datadog 发送日志。
* **整合日志记录提供商** -- 宣布针对与核心需求不符的接收器的弃用和迁移计划，并以日志的标准输出为中心。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**整合日志记录提供商**。

考虑到采用更灵活的共享（托管扩展）库的长期计划，该库可以作为项目（服务器整体）参考或 NuGet 包（以及作为参考架构）跨服务使用，使用 Serilog 作为扩展本机的方式日志记录功能是有益的。围绕如何实现 Serilog 及其高级输入和输出的详细信息可被提取到共享库中，并通过配置驱动消费应用程序。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 简化了跨组件的日志记录体验。
* 标准输出日志记录非常适合容器和编排工具。
* 消除了多个第三方依赖项及其维护，以及全局设置。
* 没有新的依赖项，这些依赖项仅仅与 Bitwarden 特定的云及其服务提供商保持一致。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 少数用户需要迁移到标准输出或类似的日志摄取。
* 管理门户日志浏览功能将退出（如果已配置），转而使用任何已配置的日志处理功能。

### 计划​ <a href="#plan" id="plan"></a>

根据标准支持策略，发行说明将提及三个 Serilog 接收器将被移除：

* CosmosDb
* Sentry
* Syslog

其余接收器（Serilog 的核心功能）将继续受支持：

* Console
* File

虽然 Serilog [console sink](https://www.nuget.org/packages/serilog.sinks.console) 当前与 ASP.NET Core 提供的内容存在隐式依赖关系，但它将被显式引用。

用户可以使用解决方案将已移除的接收器的日志处理转移到标准输出或文件，并保留其集成。管理门户用户同样可以继续使用 CosmosDb 来保留日志，但建议使用可用的应用程序监控，基本上在所有情况下都能接收和处理标准输出日志。

云安装（包括 Bitwarden 自己的云安装）将通过环境变量进行配置，或者利用结构化标准输出日志通过 [Serilog 配置](https://www.nuget.org/packages/Serilog.Settings.Configuration/)进行显式处理，例如：

```json
{
  "Serilog": {
    "Using": ["Serilog.Sinks.Console"],
    "MinimumLevel": "Verbose",
    "WriteTo": [{ "Name": "Console" }],
    "Enrich": ["FromLogContext"],
    "Properties": {
      "Project": "BitwardenProjectName"
    }
  }
}
```

这将允许更好地使用 `appsettings.json`，以及为开发人员带来更丰富的体验。如果需要，现有的内置 [.NET Core 日志记录](https://learn.microsoft.com/en-us/dotnet/core/extensions/logging)将继续可用，但建议迁移到 Serilog 配置。

将围绕 Serilog 的使用进行代码清理，例如：

* 消除过度使用的包含谓词，这些谓词会使有效日志输出复杂化（或有时会阻止日志输出），例如目前在每个消费应用程序中使用的 `AddSerilog` 。
* 在 Serilog 本身的[初始化](https://github.com/serilog/serilog-aspnetcore#two-stage-initialization)和使用方面，与 .NET Core 和 Serilog 最佳实践保持一致。
* 改进了记录初始化可靠性和处理配置问题的能力，以及在组件停止/结束时更具弹性地进行拆卸。
* 在支持窗口的最终版本中，移除上述过时的接收器。

日志记录功能将转移到一个新的共享库中（独立于核心项目），如上文提到的面向主机的实用程序。该库将作为 NuGet 包形式分发，以便本地 `server` 项目以及新的独立服务存储库都能从中受益。
