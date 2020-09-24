---
title: .NET 上 gRPC 中的日志记录和诊断
author: jamesnk
description: 了解如何从 .NET 上的 gRPC 应用收集诊断。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
no-loc:
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/diagnostics
ms.openlocfilehash: 7d2da20d04b93ebcd16fb58a4b74b5b67d37bd72
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722918"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="23854-103">.NET 上 gRPC 中的日志记录和诊断</span><span class="sxs-lookup"><span data-stu-id="23854-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="23854-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="23854-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="23854-105">本文提供从 gRPC 应用收集诊断以帮助解决问题的指南。</span><span class="sxs-lookup"><span data-stu-id="23854-105">This article provides guidance for gathering diagnostics from a gRPC app to help troubleshoot issues.</span></span> <span data-ttu-id="23854-106">涵盖的主题包括：</span><span class="sxs-lookup"><span data-stu-id="23854-106">Topics covered include:</span></span>

* <span data-ttu-id="23854-107">**日志记录** - 写入 [.NET Core 日志记录](xref:fundamentals/logging/index)的结​​构化日志。</span><span class="sxs-lookup"><span data-stu-id="23854-107">**Logging** - Structured logs written to [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="23854-108">应用框架使用 <xref:Microsoft.Extensions.Logging.ILogger> 来编写日志，用户使用它在应用中进行用户自己的日志记录。</span><span class="sxs-lookup"><span data-stu-id="23854-108"><xref:Microsoft.Extensions.Logging.ILogger> is used by app frameworks to write logs, and by users for their own logging in an app.</span></span>
* <span data-ttu-id="23854-109">**跟踪** - 与使用 `DiaganosticSource` 和 `Activity` 进行编写的操作相关的事件。</span><span class="sxs-lookup"><span data-stu-id="23854-109">**Tracing** - Events related to an operation written using `DiaganosticSource` and `Activity`.</span></span> <span data-ttu-id="23854-110">来自诊断源的跟踪通常用于通过库（如 [Application Insights](/azure/azure-monitor/app/asp-net-core) 和 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)）收集应用遥测数据。</span><span class="sxs-lookup"><span data-stu-id="23854-110">Traces from diagnostic source are commonly used to collect app telemetry by libraries such as [Application Insights](/azure/azure-monitor/app/asp-net-core) and [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).</span></span>
* <span data-ttu-id="23854-111">**指标** - 一段时间间隔内数据度量值的表示形式，例如每秒请求数。</span><span class="sxs-lookup"><span data-stu-id="23854-111">**Metrics** - Representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="23854-112">指标是使用 `EventCounter` 发出的，可以使用 [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) 命令行工具或 [Application Insights](/azure/azure-monitor/app/eventcounters) 进行观察。</span><span class="sxs-lookup"><span data-stu-id="23854-112">Metrics are emitted using `EventCounter` and can be observed using [dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) command line tool or with [Application Insights](/azure/azure-monitor/app/eventcounters).</span></span>

## <a name="logging"></a><span data-ttu-id="23854-113">日志记录</span><span class="sxs-lookup"><span data-stu-id="23854-113">Logging</span></span>

<span data-ttu-id="23854-114">gRPC 服务和 gRPC 客户端使用 [.NET Core 日志记录](xref:fundamentals/logging/index)编写日志。</span><span class="sxs-lookup"><span data-stu-id="23854-114">gRPC services and the gRPC client write logs using [.NET Core logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="23854-115">当你需要调试应用中的意外行为时，日志是一个不错的起点。</span><span class="sxs-lookup"><span data-stu-id="23854-115">Logs are a good place to start when you need to debug unexpected behavior in your apps.</span></span>

### <a name="grpc-services-logging"></a><span data-ttu-id="23854-116">gRPC 服务日志记录</span><span class="sxs-lookup"><span data-stu-id="23854-116">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="23854-117">服务器端日志可能包含来自应用的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="23854-117">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="23854-118">切勿将来自生产应用的原始日志发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="23854-118">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="23854-119">由于 gRPC 服务托管在 ASP.NET Core 上，因此它使用 ASP.NET Core 日志记录系统。</span><span class="sxs-lookup"><span data-stu-id="23854-119">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="23854-120">在默认配置中，gRPC 只记录很少的信息，但这可以进行配置。</span><span class="sxs-lookup"><span data-stu-id="23854-120">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="23854-121">有关配置 ASP.NET Core 日志记录的详细信息，请参阅 [ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)上的文档。</span><span class="sxs-lookup"><span data-stu-id="23854-121">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="23854-122">gRPC 在 `Grpc` 类别下添加日志。</span><span class="sxs-lookup"><span data-stu-id="23854-122">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="23854-123">若要启用来自 gRPC 的详细日志，请通过在 `Logging` 中的 `LogLevel` 子节中添加以下项目，将 `Grpc` 前缀配置为 appsettings.json 文件中的 `Debug` 级别：</span><span class="sxs-lookup"><span data-stu-id="23854-123">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/sample/logging-config.json?highlight=7)]

<span data-ttu-id="23854-124">你也可以在具有 `ConfigureLogging` 的 Startup.cs 中配置此项：</span><span class="sxs-lookup"><span data-stu-id="23854-124">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/sample/logging-config-code.cs?highlight=5)]

<span data-ttu-id="23854-125">如果不使用基于 JSON 的配置，请在配置系统中设置以下配置值：</span><span class="sxs-lookup"><span data-stu-id="23854-125">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="23854-126">查看配置系统的文档以确定如何指定嵌套的配置值。</span><span class="sxs-lookup"><span data-stu-id="23854-126">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="23854-127">例如，使用环境变量时，使用两个 `_` 而不是 `:` 字符（例如 `Logging__LogLevel__Grpc`）。</span><span class="sxs-lookup"><span data-stu-id="23854-127">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="23854-128">建议在为应用收集更详细的诊断时使用 `Debug` 级别。</span><span class="sxs-lookup"><span data-stu-id="23854-128">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="23854-129">`Trace` 级别产生的诊断级别很低，且很少需要它来诊断应用中的问题。</span><span class="sxs-lookup"><span data-stu-id="23854-129">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="23854-130">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="23854-130">Sample logging output</span></span>

<span data-ttu-id="23854-131">下面是 `Debug` 级别的 gRPC 服务控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="23854-131">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

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

### <a name="access-server-side-logs"></a><span data-ttu-id="23854-132">访问服务器端日志</span><span class="sxs-lookup"><span data-stu-id="23854-132">Access server-side logs</span></span>

<span data-ttu-id="23854-133">访问服务器端日志的方式取决于在其中运行的环境。</span><span class="sxs-lookup"><span data-stu-id="23854-133">How you access server-side logs depends on the environment in which you're running.</span></span>

#### <a name="as-a-console-app"></a><span data-ttu-id="23854-134">作为控制台应用</span><span class="sxs-lookup"><span data-stu-id="23854-134">As a console app</span></span>

<span data-ttu-id="23854-135">如果在控制台应用中运行，则默认情况下应启用[控制台记录器](xref:fundamentals/logging/index#console)。</span><span class="sxs-lookup"><span data-stu-id="23854-135">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console) should be enabled by default.</span></span> <span data-ttu-id="23854-136">gRPC 日志将在控制台中显示。</span><span class="sxs-lookup"><span data-stu-id="23854-136">gRPC logs will appear in the console.</span></span>

#### <a name="other-environments"></a><span data-ttu-id="23854-137">其他环境</span><span class="sxs-lookup"><span data-stu-id="23854-137">Other environments</span></span>

<span data-ttu-id="23854-138">如果将应用部署到另一个环境（例如 Docker、Kubernetes 或 Windows 服务），请参阅 <xref:fundamentals/logging/index>，了解有关如何配置适用于环境的日志记录提供程序的详细信息。</span><span class="sxs-lookup"><span data-stu-id="23854-138">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

### <a name="grpc-client-logging"></a><span data-ttu-id="23854-139">gRPC 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="23854-139">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="23854-140">客户端日志可能包含来自应用的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="23854-140">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="23854-141">切勿将来自生产应用的原始日志发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="23854-141">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="23854-142">若要从 .NET 客户端获取日志，可以在创建客户端通道时设置 `GrpcChannelOptions.LoggerFactory` 属性。</span><span class="sxs-lookup"><span data-stu-id="23854-142">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="23854-143">如果要从 ASP.NET Core 的应用调用 gRPC 服务，则可以通过依赖关系注入 (DI) 来解析记录器工厂：</span><span class="sxs-lookup"><span data-stu-id="23854-143">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/sample/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="23854-144">启用客户端日志记录的另一种方法是使用 [gRPC 客户端工厂](xref:grpc/clientfactory)创建客户端。</span><span class="sxs-lookup"><span data-stu-id="23854-144">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="23854-145">已向客户端工厂注册且解析自 DI 的 gRPC 客户端将自动使用应用的已配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="23854-145">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="23854-146">如果应用未使用 DI，则可以使用 [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*) 创建新的 `ILoggerFactory` 实例。</span><span class="sxs-lookup"><span data-stu-id="23854-146">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="23854-147">若要访问此方法，请将 [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) 包添加到应用。</span><span class="sxs-lookup"><span data-stu-id="23854-147">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/sample/net-client-loggerfactory-create.cs?highlight=1,8)]

#### <a name="grpc-client-log-scopes"></a><span data-ttu-id="23854-148">gRPC 客户端日志作用域</span><span class="sxs-lookup"><span data-stu-id="23854-148">gRPC client log scopes</span></span>

<span data-ttu-id="23854-149">gRPC 客户端可将[日志记录作用域](../fundamentals/logging/index.md#log-scopes)添加到在 gRPC 调用期间创建的日志。</span><span class="sxs-lookup"><span data-stu-id="23854-149">The gRPC client adds a [logging scope](../fundamentals/logging/index.md#log-scopes) to logs made during a gRPC call.</span></span> <span data-ttu-id="23854-150">作用域具有与 gRPC 调用相关的元数据：</span><span class="sxs-lookup"><span data-stu-id="23854-150">The scope has metadata related to the gRPC call:</span></span>

* <span data-ttu-id="23854-151">**GrpcMethodType** - gRPC 方法类型。</span><span class="sxs-lookup"><span data-stu-id="23854-151">**GrpcMethodType** - The gRPC method type.</span></span> <span data-ttu-id="23854-152">可能的值为来自 `Grpc.Core.MethodType` 枚举的名称，如一元</span><span class="sxs-lookup"><span data-stu-id="23854-152">Possible values are names from `Grpc.Core.MethodType` enum, e.g. Unary</span></span>
* <span data-ttu-id="23854-153">**GrpcUri** - gRPC 方法的相对 URI，例如 /greet.Greeter/SayHellos</span><span class="sxs-lookup"><span data-stu-id="23854-153">**GrpcUri** - The relative URI of the gRPC method, e.g. /greet.Greeter/SayHellos</span></span>

#### <a name="sample-logging-output"></a><span data-ttu-id="23854-154">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="23854-154">Sample logging output</span></span>

<span data-ttu-id="23854-155">下面是 `Debug` 级别的 gRPC 客户端控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="23854-155">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

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

## <a name="tracing"></a><span data-ttu-id="23854-156">跟踪</span><span class="sxs-lookup"><span data-stu-id="23854-156">Tracing</span></span>

<span data-ttu-id="23854-157">gRPC 服务和 gRPC 客户端使用 [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) 和 [Activity](/dotnet/api/system.diagnostics.activity) 提供有关 gRPC 调用的信息。</span><span class="sxs-lookup"><span data-stu-id="23854-157">gRPC services and the gRPC client provide information about gRPC calls using [DiagnosticSource](/dotnet/api/system.diagnostics.diagnosticsource) and [Activity](/dotnet/api/system.diagnostics.activity).</span></span>

* <span data-ttu-id="23854-158">.NET gRPC 使用活动来表示 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="23854-158">.NET gRPC uses an activity to represent a gRPC call.</span></span>
* <span data-ttu-id="23854-159">跟踪事件将在 gRPC 调用活动开始和结束时写入到诊断源。</span><span class="sxs-lookup"><span data-stu-id="23854-159">Tracing events are written to the diagnostic source at the start and stop of the gRPC call activity.</span></span>
* <span data-ttu-id="23854-160">跟踪不会捕获有关在 gRPC 流调用的生存期内消息何时发送的信息。</span><span class="sxs-lookup"><span data-stu-id="23854-160">Tracing doesn't capture information about when messages are sent over the lifetime of gRPC streaming calls.</span></span>

### <a name="grpc-service-tracing"></a><span data-ttu-id="23854-161">gRPC 服务跟踪</span><span class="sxs-lookup"><span data-stu-id="23854-161">gRPC service tracing</span></span>

<span data-ttu-id="23854-162">gRPC 服务托管在会报告有关传入 HTTP 请求事件的 ASP.NET Core 上。</span><span class="sxs-lookup"><span data-stu-id="23854-162">gRPC services are hosted on ASP.NET Core which reports events about incoming HTTP requests.</span></span> <span data-ttu-id="23854-163">特定于 gRPC 的元数据将添加到 ASP.NET Core 提供的现有 HTTP 请求诊断。</span><span class="sxs-lookup"><span data-stu-id="23854-163">gRPC specific metadata is added to the existing HTTP request diagnostics that ASP.NET Core provides.</span></span>

* <span data-ttu-id="23854-164">诊断源名称为 `Microsoft.AspNetCore`。</span><span class="sxs-lookup"><span data-stu-id="23854-164">Diagnostic source name is `Microsoft.AspNetCore`.</span></span>
* <span data-ttu-id="23854-165">活动名称为 `Microsoft.AspNetCore.Hosting.HttpRequestIn`。</span><span class="sxs-lookup"><span data-stu-id="23854-165">Activity name is `Microsoft.AspNetCore.Hosting.HttpRequestIn`.</span></span>
  * <span data-ttu-id="23854-166">gRPC 调用所调用的 gRPC 方法的名称将添加为标记，标记名称为 `grpc.method`。</span><span class="sxs-lookup"><span data-stu-id="23854-166">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="23854-167">gRPC 调用完成后，其状态代码将添加为标记，标记名称为 `grpc.status_code`。</span><span class="sxs-lookup"><span data-stu-id="23854-167">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="grpc-client-tracing"></a><span data-ttu-id="23854-168">gRPC 客户端跟踪</span><span class="sxs-lookup"><span data-stu-id="23854-168">gRPC client tracing</span></span>

<span data-ttu-id="23854-169">.NET gRPC 客户端使用 `HttpClient` 进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="23854-169">The .NET gRPC client uses `HttpClient` to make gRPC calls.</span></span> <span data-ttu-id="23854-170">尽管 `HttpClient` 可编写诊断事件，但 .NET gRPC 客户端提供自定义诊断源、活动和事件，以便可以收集有关 gRPC 调用的完整信息。</span><span class="sxs-lookup"><span data-stu-id="23854-170">Although `HttpClient` writes diagnostic events, the .NET gRPC client provides a custom diagnostic source, activity and events so that complete information about a gRPC call can be collected.</span></span>

* <span data-ttu-id="23854-171">诊断源名称为 `Grpc.Net.Client`。</span><span class="sxs-lookup"><span data-stu-id="23854-171">Diagnostic source name is `Grpc.Net.Client`.</span></span>
* <span data-ttu-id="23854-172">活动名称为 `Grpc.Net.Client.GrpcOut`。</span><span class="sxs-lookup"><span data-stu-id="23854-172">Activity name is `Grpc.Net.Client.GrpcOut`.</span></span>
  * <span data-ttu-id="23854-173">gRPC 调用所调用的 gRPC 方法的名称将添加为标记，标记名称为 `grpc.method`。</span><span class="sxs-lookup"><span data-stu-id="23854-173">Name of the gRPC method invoked by the gRPC call is added as a tag with the name `grpc.method`.</span></span>
  * <span data-ttu-id="23854-174">gRPC 调用完成后，其状态代码将添加为标记，标记名称为 `grpc.status_code`。</span><span class="sxs-lookup"><span data-stu-id="23854-174">Status code of the gRPC call when it is complete is added as a tag with the name `grpc.status_code`.</span></span>

### <a name="collecting-tracing"></a><span data-ttu-id="23854-175">收集跟踪</span><span class="sxs-lookup"><span data-stu-id="23854-175">Collecting tracing</span></span>

<span data-ttu-id="23854-176">使用 `DiagnosticSource` 的最简单方法是在应用中配置遥测库，如 [Application Insights](/azure/azure-monitor/app/asp-net-core) 或 [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet)。</span><span class="sxs-lookup"><span data-stu-id="23854-176">The easiest way to use `DiagnosticSource` is to configure a telemetry library such as [Application Insights](/azure/azure-monitor/app/asp-net-core) or [OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) in your app.</span></span> <span data-ttu-id="23854-177">该库将与其他应用遥测一起处理有关 gRPC 调用的信息。</span><span class="sxs-lookup"><span data-stu-id="23854-177">The library will process information about gRPC calls along-side other app telemetry.</span></span>

<span data-ttu-id="23854-178">可以在托管服务（如 Application Insights）中查看跟踪，也可以选择运行自己的分布式跟踪系统。</span><span class="sxs-lookup"><span data-stu-id="23854-178">Tracing can be viewed in a managed service like Application Insights, or you can choose to run your own distributed tracing system.</span></span> <span data-ttu-id="23854-179">OpenTelemetry 支持将跟踪数据导出到 [Jaeger](https://www.jaegertracing.io/) 和 [Zipkin](https://zipkin.io/)。</span><span class="sxs-lookup"><span data-stu-id="23854-179">OpenTelemetry supports exporting tracing data to [Jaeger](https://www.jaegertracing.io/) and [Zipkin](https://zipkin.io/).</span></span>

<span data-ttu-id="23854-180">`DiagnosticSource` 可以使用 `DiagnosticListener` 在代码中使用跟踪事件。</span><span class="sxs-lookup"><span data-stu-id="23854-180">`DiagnosticSource` can consume tracing events in code using `DiagnosticListener`.</span></span> <span data-ttu-id="23854-181">有关使用代码侦听诊断源的信息，请参阅 [DiagnosticSource 用户指南](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener)。</span><span class="sxs-lookup"><span data-stu-id="23854-181">For information about listening to a diagnostic source with code, see the [DiagnosticSource user's guide](https://github.com/dotnet/corefx/blob/d3942d4671919edb0cca6ddc1840190f524a809d/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#consuming-data-with-diagnosticlistener).</span></span>

> [!NOTE]
> <span data-ttu-id="23854-182">遥测库当前不捕获特定于 gRPC 的 `Grpc.Net.Client.GrpcOut` 遥测。</span><span class="sxs-lookup"><span data-stu-id="23854-182">Telemetry libraries do not capture gRPC specific `Grpc.Net.Client.GrpcOut` telemetry currently.</span></span> <span data-ttu-id="23854-183">改进捕获此跟踪的遥测库的工作正在进行。</span><span class="sxs-lookup"><span data-stu-id="23854-183">Work to improve telemetry libraries capturing this tracing is ongoing.</span></span>

## <a name="metrics"></a><span data-ttu-id="23854-184">指标</span><span class="sxs-lookup"><span data-stu-id="23854-184">Metrics</span></span>

<span data-ttu-id="23854-185">指标是一段时间间隔内数据度量值的表示形式，例如每秒请求数。</span><span class="sxs-lookup"><span data-stu-id="23854-185">Metrics is a representation of data measures over intervals of time, for example, requests per second.</span></span> <span data-ttu-id="23854-186">使用指标数据可以在高级别观察应用的状态。</span><span class="sxs-lookup"><span data-stu-id="23854-186">Metrics data allows observation of the state of an app at a high-level.</span></span> <span data-ttu-id="23854-187">.NET gRPC 指标是使用 `EventCounter` 发出的。</span><span class="sxs-lookup"><span data-stu-id="23854-187">.NET gRPC metrics are emitted using `EventCounter`.</span></span>

### <a name="grpc-service-metrics"></a><span data-ttu-id="23854-188">gRPC 服务指标</span><span class="sxs-lookup"><span data-stu-id="23854-188">gRPC service metrics</span></span>

<span data-ttu-id="23854-189">gRPC 服务器指标在 `Grpc.AspNetCore.Server` 事件源上报告。</span><span class="sxs-lookup"><span data-stu-id="23854-189">gRPC server metrics are reported on `Grpc.AspNetCore.Server` event source.</span></span>

| <span data-ttu-id="23854-190">“属性”</span><span class="sxs-lookup"><span data-stu-id="23854-190">Name</span></span>                      | <span data-ttu-id="23854-191">描述</span><span class="sxs-lookup"><span data-stu-id="23854-191">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="23854-192">总调用数</span><span class="sxs-lookup"><span data-stu-id="23854-192">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="23854-193">当前调用</span><span class="sxs-lookup"><span data-stu-id="23854-193">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="23854-194">失败调用总数</span><span class="sxs-lookup"><span data-stu-id="23854-194">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="23854-195">超出截止时间的调用总数</span><span class="sxs-lookup"><span data-stu-id="23854-195">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="23854-196">发送的邮件总数</span><span class="sxs-lookup"><span data-stu-id="23854-196">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="23854-197">收到的消息总数</span><span class="sxs-lookup"><span data-stu-id="23854-197">Total Messages Received</span></span>       |
| `calls-unimplemented`     | <span data-ttu-id="23854-198">总未实现调用数</span><span class="sxs-lookup"><span data-stu-id="23854-198">Total Calls Unimplemented</span></span>     |

<span data-ttu-id="23854-199">ASP.NET Core 还在 `Microsoft.AspNetCore.Hosting` 事件源上提供其自己的指标。</span><span class="sxs-lookup"><span data-stu-id="23854-199">ASP.NET Core also provides its own metrics on `Microsoft.AspNetCore.Hosting` event source.</span></span>

### <a name="grpc-client-metrics"></a><span data-ttu-id="23854-200">gRPC 客户端指标</span><span class="sxs-lookup"><span data-stu-id="23854-200">gRPC client metrics</span></span>

<span data-ttu-id="23854-201">gRPC 客户端指标在 `Grpc.Net.Client` 事件源上报告。</span><span class="sxs-lookup"><span data-stu-id="23854-201">gRPC client metrics are reported on `Grpc.Net.Client` event source.</span></span>

| <span data-ttu-id="23854-202">“属性”</span><span class="sxs-lookup"><span data-stu-id="23854-202">Name</span></span>                      | <span data-ttu-id="23854-203">描述</span><span class="sxs-lookup"><span data-stu-id="23854-203">Description</span></span>                   |
| --------------------------|-------------------------------|
| `total-calls`             | <span data-ttu-id="23854-204">总调用数</span><span class="sxs-lookup"><span data-stu-id="23854-204">Total Calls</span></span>                   |
| `current-calls`           | <span data-ttu-id="23854-205">当前调用</span><span class="sxs-lookup"><span data-stu-id="23854-205">Current Calls</span></span>                 |
| `calls-failed`            | <span data-ttu-id="23854-206">失败调用总数</span><span class="sxs-lookup"><span data-stu-id="23854-206">Total Calls Failed</span></span>            |
| `calls-deadline-exceeded` | <span data-ttu-id="23854-207">超出截止时间的调用总数</span><span class="sxs-lookup"><span data-stu-id="23854-207">Total Calls Deadline Exceeded</span></span> |
| `messages-sent`           | <span data-ttu-id="23854-208">发送的邮件总数</span><span class="sxs-lookup"><span data-stu-id="23854-208">Total Messages Sent</span></span>           |
| `messages-received`       | <span data-ttu-id="23854-209">收到的消息总数</span><span class="sxs-lookup"><span data-stu-id="23854-209">Total Messages Received</span></span>       |

### <a name="observe-metrics"></a><span data-ttu-id="23854-210">观察指标</span><span class="sxs-lookup"><span data-stu-id="23854-210">Observe metrics</span></span>

<span data-ttu-id="23854-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) 是一个性能监视工具，用于临时运行状况监视和初级性能调查。</span><span class="sxs-lookup"><span data-stu-id="23854-211">[dotnet-counters](/dotnet/core/diagnostics/dotnet-counters) is a performance monitoring tool for ad-hoc health monitoring and first-level performance investigation.</span></span> <span data-ttu-id="23854-212">使用 `Grpc.AspNetCore.Server` 或 `Grpc.Net.Client` 作为提供程序名称监视 .NET 应用。</span><span class="sxs-lookup"><span data-stu-id="23854-212">Monitor a .NET app with either `Grpc.AspNetCore.Server` or `Grpc.Net.Client` as the provider name.</span></span>

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

<span data-ttu-id="23854-213">观察 gRPC 指标的另一种方法是使用 Application Insights 的 [ 包](/azure/azure-monitor/app/eventcounters)捕获计数器数据。</span><span class="sxs-lookup"><span data-stu-id="23854-213">Another way to observe gRPC metrics is to capture counter data using Application Insights's [Microsoft.ApplicationInsights.EventCounterCollector package](/azure/azure-monitor/app/eventcounters).</span></span> <span data-ttu-id="23854-214">设置完成后，Application Insights 可在运行时收集常见的 .NET 计数器。</span><span class="sxs-lookup"><span data-stu-id="23854-214">Once setup, Application Insights collects common .NET counters at runtime.</span></span> <span data-ttu-id="23854-215">默认情况下，不收集 gRPC 的计数器，但可以[自定义 App Insights 以包括其他计数器](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected)。</span><span class="sxs-lookup"><span data-stu-id="23854-215">gRPC's counters are not collected by default, but App Insights can be [customized to include additional counters](/azure/azure-monitor/app/eventcounters#customizing-counters-to-be-collected).</span></span>

<span data-ttu-id="23854-216">为 Application Insight 指定 gRPC 计数器以在 Startup.cs 中收集：</span><span class="sxs-lookup"><span data-stu-id="23854-216">Specify the gRPC counters for Application Insight to collect in *Startup.cs*:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="23854-217">其他资源</span><span class="sxs-lookup"><span data-stu-id="23854-217">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>