# 0020 - Observability with OpenTelemetry

{% hint style="info" %}
对应的[官方页面地址](https://contributing.bitwarden.com/architecture/adr/observability-with-opentelemetry)
{% endhint %}

| ID：  | ADR-0020   |
| ---- | ---------- |
| 状态：  | 进行中        |
| 发表于： | 2023-07-13 |

## 背景和问题陈述​ <a href="#context-and-problem-statement" id="context-and-problem-statement"></a>

随着多年来代码库的成熟，平台上的用户数量也显著增长，需要更深入地了解服务如何在细粒度级别上执行。外部分析器当然可以连接到任何运行环境中，但平台本身需要提供内部指标，这不仅是为了支持运行产品的自托管客户，也是为了让工程师能够改进产品，并通过可靠的数据和证据来解决性能问题，从而确定应该改变什么以及为什么要改变。

## 考虑的方案​ <a href="#considered-options" id="considered-options"></a>

> Bitwarden 目前使用 [Datadog](https://www.datadoghq.com/) 作为其监控工具，并希望全面提高工程师的使用率，以改进以改进我们的交付。

* **保持当前的可观察性方案** -- 希望运行平台的人员能够在平台之外配置他们所需的日志收集和分析/监控功能。
* **扩展平台以专门支持 Datadog** -- [Datadog 跟踪](https://www.nuget.org/packages/Datadog.Trace.Bundle)以包形式存在，并且可以编码到应用程序启动中。 Datadog 特定的信号和指标可以通过代码收集并发送到平台。
* **实施本机插桩** -- 通过 [`System.Diagnostics`](https://learn.microsoft.com/en-us/dotnet/core/diagnostics/metrics-instrumentation) 中的可用功能添加逻辑，实现自定义插桩，并根据上述第一个方案配置分析。
* **使用开放式可观测性标准** -- 利用 [OpenTelemetry](https://opentelemetry.io/) 并在控制台上发出信号，并利用自身的事件处理方法获取插桩和指标数据。

## 决策结果​ <a href="#decision-outcome" id="decision-outcome"></a>

选择的方案：**使用开放式可观测性标准**。

一个很好的替代方案是仅使用本机插桩，而不是将平台与任何特定生态系统的实现相绑定 -- 即使是像 OpenTelemetry 这样的开放标准。.NET 密切支持 OpenTelemetry 指标收集集成，但所需的功能在于如何通过 OTLP 等输出机制使用这些数据。附加到正在运行的组件的分析器与通过其他方式（如代理收集）获得的指标无关。

通过配置获取的访问指标胜过设置和管理分析器。

### 积极的后果​ <a href="#positive-consequences" id="positive-consequences"></a>

* 如果需要使用指标的控制台日志记录，它非常适合容器和编排工具，而且上述环境可以安装代理来收集这些指标。
* 没有新的依赖关系，仅与 Bitwarden 专用云及其服务提供商保持一致。
* 可对组件进行更详细的监控，从而促进未来的改进。
* 使用 OpenTelemetry 等开放标准为未来的监控和可观察性创造了灵活性，使其随着生态系统的扩展而增长，例如 OTLP 导出与控制台日志记录。

### 消极的后果​ <a href="#negative-consequences" id="negative-consequences"></a>

* 在所有服务中添加 OpenTelemetry 依赖项。
* 专有的分析器实现可能会提供 OpenTelemetry 无法提供的信号信息，包括自动插桩。
* 有了在平台内捕获信号的能力，就需要维护不捕获敏感数据的明确策略。

### 计划​ <a href="#plan" id="plan"></a>

.NET Core 的 `System.Diagnostics` 库支持发出与 OpenTelemetry 兼容的指标，平台内的跟踪和指标将在控制台上并通过 OTLP 导出提供。将通过新的应用程序设置提供开启或关闭配置，例如：

```json
{
  "OpenTelemetry": {
    "UseTracingExporter": "Console",
    "UseMetricsExporter": "Console",
    "Otlp": {
      "Endpoint": "http://localhost:4318"
    }
  }
}
```

控制台和 OTLP 选项将可用于指标和跟踪导出，并且能够为 OTLP 指定 gRPC 或 HTTP 端点。将继续使用可配置的 `ProjectName` 对活动进行细分。

初始实现将提供来自 ASP.NET Core 和任何使用过的 HTTP 客户端的默认插桩详细信息。未来可能会在 Bitwarden 中探索自动插桩（分析器），但我们希望采用代码优先的解决方案，以便在安装过程中实现更多控制和更少的设置。预计本地进程将根据需要摄取日志/导出。

将对软件开发生命周期进行改进，以明确最佳实践，并审查日志记录或监控更改的要求。将[深度剖析](../deep-dives/)日志记录和监控，以展示在代码中添加信号收集的模式。开始时将只收集组件运行时信号；不会在信号中收集输入和输出数据等应用程序有效载荷。

随着时间的推移和需要，将采用应用逻辑来跟踪自定义 [signals](https://opentelemetry.io/docs/concepts/signals/)（活动和仪表），以获得更深入的见解，特别是在关键代码路径中。将在上述关于如何处理指标收集的深度剖析中，制定和记录标准，而无需收集敏感信息。将开发核心实用工具类，以建立 OpenTelemetry 使用的集中化，并使组件的使用变得更容易和通用。

可观察性功能将被转移到一个新的共享库中（与核心库分开），用于面向主机的实用工具。该库将作为 NuGet 包分发，以便本地 `server` 项目以及新的独立服务存储库都能从中受益。
