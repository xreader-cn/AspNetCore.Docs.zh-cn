---
title: .NET 上的 gRPC 中的日志记录和诊断
author: jamesnk
description: 了解如何从 .NET 上的 gRPC 应用程序收集诊断信息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: 17607b734e6d777de9516aa14e81c97f87b61023
ms.sourcegitcommit: 80286715afb93c4d13c931b008016d6086c0312b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/07/2020
ms.locfileid: "77074518"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a>.NET 上的 gRPC 中的日志记录和诊断

按[James 牛顿-k](https://twitter.com/jamesnk)

本文介绍如何从 gRPC 应用程序收集诊断信息，以帮助解决问题。 涵盖的主题包括：

* **日志记录**-写入到[.net Core 日志记录](xref:fundamentals/logging/index)的结构化日志。 应用程序框架使用 <xref:Microsoft.Extensions.Logging.ILogger> 来编写日志，并由用户在应用中使用其自己的日志记录。
* **跟踪**-与使用 `DiaganosticSource` 和 `Activity`编写的操作相关的事件。 来自诊断源的跟踪通常用于通过库（如[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)和[OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)）收集应用遥测数据。
* **指标**-以时间间隔表示的数据度量值，例如每秒的请求数。 使用 `EventCounter` 发出指标，可以使用[dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)命令行工具或[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)来观察指标。

## <a name="logging"></a>日志记录

gRPC services 和 gRPC 客户端使用[.Net Core 日志记录](xref:fundamentals/logging/index)编写日志。 当你需要在应用中调试意外行为时，可以开始使用日志。

### <a name="grpc-services-logging"></a>gRPC services 日志记录

> [!WARNING]
> 服务器端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

由于 gRPC 服务托管在 ASP.NET Core 上，因此它使用 ASP.NET Core 日志记录系统。 在默认配置中，gRPC 记录的信息非常小，但这可以进行配置。 有关配置 ASP.NET Core 日志记录的详细信息，请参阅有关[ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)的文档。

gRPC 在 `Grpc` 类别下添加日志。 若要从 gRPC 启用详细日志，请通过将以下项添加到 `Logging`中的 `LogLevel` 子节，将 `Grpc` 前缀配置为*appsettings*文件中的 `Debug` 级别：

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

你还可以在*Startup.cs*中对此进行配置 `ConfigureLogging`：

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

如果不使用基于 JSON 的配置，请在配置系统中设置以下配置值：

* `Logging:LogLevel:Grpc` = `Debug`

查看配置系统的文档以确定如何指定嵌套配置值。 例如，使用环境变量时，将使用两个 `_` 字符，而不是 `:` （例如 `Logging__LogLevel__Grpc`）。

建议在为应用收集更详细的诊断时使用 `Debug` 级别。 `Trace` 级别产生非常低级别的诊断，很少需要诊断应用程序中的问题。

#### <a name="sample-logging-output"></a>日志记录输出示例

下面是 gRPC 服务 `Debug` 级别的控制台输出示例：

```console
info: Microsoft.AspNetCore.Hosting.Diagnostics[1]
      Request starting HTTP/2 POST https://localhost:5001/Greet.Greeter/SayHello application/grpc
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[0]
      Executing endpoint 'gRPC - /Greet.Greeter/SayHello'
dbug: Grpc.AspNetCore.Server.ServerCallHandler[1]
      Reading message.
info: GrpcService.GreeterService[0]
      Hello World
dbug: Grpc.AspNetCore.Server.ServerCallHandler[6]
      Sending message.
info: Microsoft.AspNetCore.Routing.EndpointMiddleware[1]
      Executed endpoint 'gRPC - /Greet.Greeter/SayHello'
info: Microsoft.AspNetCore.Hosting.Diagnostics[2]
      Request finished in 1.4113ms 200 application/grpc
```

### <a name="access-server-side-logs"></a>访问服务器端日志

访问服务器端日志的方式取决于运行的环境。

#### <a name="as-a-console-app"></a>作为控制台应用

如果在控制台应用中运行，则默认情况下应启用[控制台记录器](xref:fundamentals/logging/index#console-provider)。 gRPC 日志将显示在控制台中。

#### <a name="other-environments"></a>其他环境

如果将应用部署到另一个环境（例如 Docker、Kubernetes 或 Windows 服务），请参阅 <xref:fundamentals/logging/index>，了解有关如何配置适用于环境的日志记录提供程序的详细信息。

### <a name="grpc-client-logging"></a>gRPC 客户端日志记录

> [!WARNING]
> 客户端日志可能包含应用中的敏感信息。 **请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。

若要从 .NET 客户端获取日志，可以在创建客户端通道时设置 `GrpcChannelOptions.LoggerFactory` 属性。 如果要从 ASP.NET Core 的应用程序调用 gRPC 服务，则可以通过依赖关系注入（DI）来解析记录器工厂：

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

启用客户端日志记录的另一种方法是使用[gRPC 客户端工厂](xref:grpc/clientfactory)来创建客户端。 向客户端工厂注册并从 DI 解析的 gRPC 客户端将自动使用应用的配置日志记录。

如果你的应用未使用 DI，则可以使用[server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)创建新的 `ILoggerFactory` 实例。 若要访问此方法，请向应用程序中[添加 ""。](https://www.nuget.org/packages/microsoft.extensions.logging/)

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a>gRPC 客户端日志范围

GRPC 客户端向 gRPC 调用期间创建的日志添加[日志记录范围](https://docs.microsoft.com/aspnet/core/fundamentals/logginglog-scopes)。 作用域具有与 gRPC 调用相关的元数据：

* **GrpcMethodType** -gRPC 方法类型。 可能的值是来自 `Grpc.Core.MethodType` 枚举的名称，如一元
* **GrpcUri** -gRPC 方法的相对 URI，例如/greet。Greeter/SayHellos

#### <a name="sample-logging-output"></a>日志记录输出示例

下面是 gRPC 客户端 `Debug` 级别的控制台输出示例：

```console
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="tracing"></a>跟踪

gRPC services 和 gRPC 客户端使用[DiagnosticSource](https://docs.microsoft.com/dotnet/api/system.diagnostics.diagnosticsource)和[活动](https://docs.microsoft.com/dotnet/api/system.diagnostics.activity)提供有关 gRPC 调用的信息。

* .NET gRPC 使用活动表示 gRPC 调用。
* 跟踪事件将写入到 gRPC call 活动开始和结束时的诊断源。
* 跟踪不会捕获有关消息在 gRPC 流调用的生存期内发送的信息。

### <a name="grpc-service-tracing"></a>gRPC 服务跟踪

gRPC services 在 ASP.NET Core 上托管，它报告有关传入 HTTP 请求的事件。 gRPC 特定的元数据添加到 ASP.NET Core 提供的现有 HTTP 请求诊断。

* 诊断源名称为 `Microsoft.AspNetCore`。
* 活动名称为 `Microsoft.AspNetCore.Hosting.HttpRequestIn`。
  * GRPC 调用调用的 gRPC 方法的名称将添加为 `grpc.method`名称的标记。
  * 完成后，gRPC 调用的状态代码将添加为名称为 `grpc.status_code`的标记。

### <a name="grpc-client-tracing"></a>gRPC 客户端跟踪

.NET gRPC 客户端使用 `HttpClient` 进行 gRPC 调用。 尽管 `HttpClient` 会写入诊断事件，但 .NET gRPC 客户端提供自定义诊断源、活动和事件，以便可以收集有关 gRPC 调用的完整信息。

* 诊断源名称为 `Grpc.Net.Client`。
* 活动名称为 `Grpc.Net.Client.GrpcOut`。
  * GRPC 调用调用的 gRPC 方法的名称将添加为 `grpc.method`名称的标记。
  * 完成后，gRPC 调用的状态代码将添加为名称为 `grpc.status_code`的标记。

### <a name="collecting-tracing"></a>正在收集跟踪

使用 `DiagnosticSource` 的最简单方法是在应用程序中配置遥测库，如[Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/asp-net-core)或[OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) 。 库将处理有关 gRPC 调用的信息，以及其他应用遥测。

可以在托管服务（如 Application Insights）中查看跟踪，也可以选择运行自己的分布式跟踪系统。 OpenTelemetry 支持将跟踪数据导出到[Jaeger](https://www.jaegertracing.io/)和[Zipkin](https://zipkin.io/)。

`DiagnosticSource` 可以使用 `DiagnosticListener`使用代码中的跟踪事件。 有关使用代码侦听诊断源的信息，请参阅[DiagnosticSource 用户指南](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)。

> [!NOTE]
> 遥测库不捕获当前特定 `Grpc.Net.Client.GrpcOut` 遥测。 改善了捕获此跟踪的遥测库的工作正在进行中。

## <a name="metrics"></a>度量值

度量值是一种基于时间间隔的数据度量值，例如每秒的请求数。 使用度量值数据可以在高级别观察应用的状态。 使用 `EventCounter`发出 .NET gRPC 指标。

### <a name="grpc-service-metrics"></a>gRPC 服务指标

`Grpc.AspNetCore.Server` 事件源上报告 gRPC 服务器指标。

| 名称                      | 说明                   |
| --------------------------|-------------------------------|
| `total-calls`             | 总调用数                   |
| `current-calls`           | 当前调用                 |
| `calls-failed`            | 失败的调用总数            |
| `calls-deadline-exceeded` | 超过总调用截止时间 |
| `messages-sent`           | 发送的邮件总数           |
| `messages-received`       | 收到的消息总数       |
| `calls-unimplemented`     | 未实现的总调用数     |

ASP.NET Core 还在 `Microsoft.AspNetCore.Hosting` 事件源上提供其自己的度量值。

### <a name="grpc-client-metrics"></a>gRPC 客户端指标

`Grpc.Net.Client` 事件源上报告 gRPC 客户端指标。

| 名称                      | 说明                   |
| --------------------------|-------------------------------|
| `total-calls`             | 总调用数                   |
| `current-calls`           | 当前调用                 |
| `calls-failed`            | 失败的调用总数            |
| `calls-deadline-exceeded` | 超过总调用截止时间 |
| `messages-sent`           | 发送的邮件总数           |
| `messages-received`       | 收到的消息总数       |

### <a name="observe-metrics"></a>观察指标

[dotnet](https://docs.microsoft.com/dotnet/core/diagnostics/dotnet-counters)是一种性能监视工具，用于即席运行状况监视和一级性能调查。 使用 `Grpc.AspNetCore.Server` 或 `Grpc.Net.Client` 作为提供程序名称监视 .NET 应用程序。

```console
> dotnet-counters monitor --process-id 1902 Grpc.AspNetCore.Server

Press p to pause, r to resume, q to quit.
    Status: Running
[Grpc.AspNetCore.Server]
    Total Calls                                 300
    Current Calls                               5
    Total Calls Failed                          0
    Total Calls Deadline Exceeded               0
    Total Messages Sent                         295
    Total Messages Received                     300
    Total Calls Unimplemented                   0
```

观察 gRPC 度量值的另一种方法是使用 Application Insights 的[EventCounterCollector 包](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters)捕获计数器数据。 设置后，Application Insights 在运行时收集常见的 .NET 计数器。 默认情况下，不收集 gRPC 的计数器，但可以[自定义 App Insights 以包括其他计数器](https://docs.microsoft.com/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)。

指定 gRPC 计数器，以便在*Startup.cs*中收集 Application insights：

```csharp
    using Microsoft.ApplicationInsights.Extensibility.EventCounterCollector;

    public void ConfigureServices(IServiceCollection services)
    {
        //... other code...

        services.ConfigureTelemetryModule<EventCounterCollectionModule>(
            (module, o) =>
            {
                // Configure App Insights to collect gRPC counters gRPC services hosted in an ASP.NET Core app
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "current-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "total-calls"));
                module.Counters.Add(new EventCounterCollectionRequest("Grpc.AspNetCore.Server", "calls-failed"));
            }
        );
    }
```

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
