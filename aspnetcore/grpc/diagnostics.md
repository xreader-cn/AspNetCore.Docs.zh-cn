---
title: .NET 上的 gRPC 中的日志记录和诊断
author: jamesnk
description: 了解如何从 .NET 上的 gRPC 应用程序收集诊断信息。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 09/23/2019
uid: grpc/diagnostics
ms.openlocfilehash: ce6ad96d9e26c9cd3844093536745f8f9bea4a76
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71204323"
---
# <a name="logging-and-diagnostics-in-grpc-on-net"></a><span data-ttu-id="edc39-103">.NET 上的 gRPC 中的日志记录和诊断</span><span class="sxs-lookup"><span data-stu-id="edc39-103">Logging and diagnostics in gRPC on .NET</span></span>

<span data-ttu-id="edc39-104">按[James 牛顿-k](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="edc39-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="edc39-105">本文介绍如何从 gRPC 应用程序收集诊断信息，以帮助解决问题。</span><span class="sxs-lookup"><span data-stu-id="edc39-105">This article provides guidance for gathering diagnostics from your gRPC app to help troubleshoot issues.</span></span>

## <a name="grpc-services-logging"></a><span data-ttu-id="edc39-106">gRPC services 日志记录</span><span class="sxs-lookup"><span data-stu-id="edc39-106">gRPC services logging</span></span>

> [!WARNING]
> <span data-ttu-id="edc39-107">服务器端日志可能包含应用中的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="edc39-107">Server-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="edc39-108">**请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="edc39-108">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="edc39-109">由于 gRPC 服务托管在 ASP.NET Core 上，因此它使用 ASP.NET Core 日志记录系统。</span><span class="sxs-lookup"><span data-stu-id="edc39-109">Since gRPC services are hosted on ASP.NET Core, it uses the ASP.NET Core logging system.</span></span> <span data-ttu-id="edc39-110">在默认配置中，gRPC 记录的信息非常小，但这可以进行配置。</span><span class="sxs-lookup"><span data-stu-id="edc39-110">In the default configuration, gRPC logs very little information, but this can configured.</span></span> <span data-ttu-id="edc39-111">有关配置 ASP.NET Core 日志记录的详细信息，请参阅有关[ASP.NET Core 日志记录](xref:fundamentals/logging/index#configuration)的文档。</span><span class="sxs-lookup"><span data-stu-id="edc39-111">See the documentation on [ASP.NET Core logging](xref:fundamentals/logging/index#configuration) for details on configuring ASP.NET Core logging.</span></span>

<span data-ttu-id="edc39-112">gRPC 会将日志添加`Grpc`到类别下。</span><span class="sxs-lookup"><span data-stu-id="edc39-112">gRPC adds logs under the `Grpc` category.</span></span> <span data-ttu-id="edc39-113">若要从 gRPC 启用详细日志，请`Grpc`通过将以下`Debug`项添加到中`LogLevel` `Logging`的子部分，将前缀配置为 appsettings 文件中的级别：</span><span class="sxs-lookup"><span data-stu-id="edc39-113">To enable detailed logs from gRPC, configure the `Grpc` prefixes to the `Debug` level in your *appsettings.json* file by adding the following items to the `LogLevel` sub-section in `Logging`:</span></span>

[!code-json[](diagnostics/logging-config.json?highlight=7)]

<span data-ttu-id="edc39-114">你还可以在*Startup.cs*中通过以下`ConfigureLogging`方式配置此项：</span><span class="sxs-lookup"><span data-stu-id="edc39-114">You can also configure this in *Startup.cs* with `ConfigureLogging`:</span></span>

[!code-csharp[](diagnostics/logging-config-code.cs?highlight=5)]

<span data-ttu-id="edc39-115">如果不使用基于 JSON 的配置，请在配置系统中设置以下配置值：</span><span class="sxs-lookup"><span data-stu-id="edc39-115">If you aren't using JSON-based configuration, set the following configuration value in your configuration system:</span></span>

* `Logging:LogLevel:Grpc` = `Debug`

<span data-ttu-id="edc39-116">查看配置系统的文档以确定如何指定嵌套配置值。</span><span class="sxs-lookup"><span data-stu-id="edc39-116">Check the documentation for your configuration system to determine how to specify nested configuration values.</span></span> <span data-ttu-id="edc39-117">例如，使用环境变量时，将使用`_`两个字符，而不`:`是（例如`Logging__LogLevel__Grpc`）。</span><span class="sxs-lookup"><span data-stu-id="edc39-117">For example, when using environment variables, two `_` characters are used instead of the `:` (for example, `Logging__LogLevel__Grpc`).</span></span>

<span data-ttu-id="edc39-118">建议为应用收集`Debug`更详细的诊断时使用级别。</span><span class="sxs-lookup"><span data-stu-id="edc39-118">We recommend using the `Debug` level when gathering more detailed diagnostics for your app.</span></span> <span data-ttu-id="edc39-119">此`Trace`级别生成非常低级别的诊断，很少需要诊断应用中的问题。</span><span class="sxs-lookup"><span data-stu-id="edc39-119">The `Trace` level produces very low-level diagnostics and is rarely needed to diagnose issues in your app.</span></span>

### <a name="sample-logging-output"></a><span data-ttu-id="edc39-120">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="edc39-120">Sample logging output</span></span>

<span data-ttu-id="edc39-121">下面是 gRPC 服务`Debug`级别的控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="edc39-121">Here is an example of console output at the `Debug` level of a gRPC service:</span></span>

```
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

## <a name="access-server-side-logs"></a><span data-ttu-id="edc39-122">访问服务器端日志</span><span class="sxs-lookup"><span data-stu-id="edc39-122">Access server-side logs</span></span>

<span data-ttu-id="edc39-123">访问服务器端日志的方式取决于运行的环境。</span><span class="sxs-lookup"><span data-stu-id="edc39-123">How you access server-side logs depends on the environment in which you're running.</span></span>

### <a name="as-a-console-app"></a><span data-ttu-id="edc39-124">作为控制台应用</span><span class="sxs-lookup"><span data-stu-id="edc39-124">As a console app</span></span>

<span data-ttu-id="edc39-125">如果在控制台应用中运行，则默认情况下应启用[控制台记录器](xref:fundamentals/logging/index#console-provider)。</span><span class="sxs-lookup"><span data-stu-id="edc39-125">If you're running in a console app, the [Console logger](xref:fundamentals/logging/index#console-provider) should be enabled by default.</span></span> <span data-ttu-id="edc39-126">gRPC 日志将显示在控制台中。</span><span class="sxs-lookup"><span data-stu-id="edc39-126">gRPC logs will appear in the console.</span></span>

### <a name="other-environments"></a><span data-ttu-id="edc39-127">其他环境</span><span class="sxs-lookup"><span data-stu-id="edc39-127">Other environments</span></span>

<span data-ttu-id="edc39-128">如果将应用部署到另一个环境（例如 Docker、Kubernetes 或 Windows 服务），请参阅<xref:fundamentals/logging/index> ，以了解有关如何配置适用于环境的日志记录提供程序的详细信息。</span><span class="sxs-lookup"><span data-stu-id="edc39-128">If the app is deployed to another environment (for example, Docker, Kubernetes, or Windows Service), see <xref:fundamentals/logging/index> for more information on how to configure logging providers suitable for the environment.</span></span>

## <a name="grpc-client-logging"></a><span data-ttu-id="edc39-129">gRPC 客户端日志记录</span><span class="sxs-lookup"><span data-stu-id="edc39-129">gRPC client logging</span></span>

> [!WARNING]
> <span data-ttu-id="edc39-130">客户端日志可能包含应用中的敏感信息。</span><span class="sxs-lookup"><span data-stu-id="edc39-130">Client-side logs may contain sensitive information from your app.</span></span> <span data-ttu-id="edc39-131">**请勿**将原始日志从生产应用发布到 GitHub 等公共论坛。</span><span class="sxs-lookup"><span data-stu-id="edc39-131">**Never** post raw logs from production apps to public forums like GitHub.</span></span>

<span data-ttu-id="edc39-132">若要从 .net 客户端获取日志，可以在创建`GrpcChannelOptions.LoggerFactory`客户端通道时设置属性。</span><span class="sxs-lookup"><span data-stu-id="edc39-132">To get logs from the .NET client, you can set the `GrpcChannelOptions.LoggerFactory` property when the client's channel is created.</span></span> <span data-ttu-id="edc39-133">如果要从 ASP.NET Core 的应用程序调用 gRPC 服务，则可以通过依赖关系注入（DI）来解析记录器工厂：</span><span class="sxs-lookup"><span data-stu-id="edc39-133">If you are calling a gRPC service from an ASP.NET Core app then the logger factory can be resolved from dependency injection (DI):</span></span>

[!code-csharp[](diagnostics/net-client-dependency-injection.cs?highlight=7,16)]

<span data-ttu-id="edc39-134">启用客户端日志记录的另一种方法是使用[gRPC 客户端工厂](xref:grpc/clientfactory)来创建客户端。</span><span class="sxs-lookup"><span data-stu-id="edc39-134">An alternative way to enable client logging is to use the [gRPC client factory](xref:grpc/clientfactory) to create the client.</span></span> <span data-ttu-id="edc39-135">向客户端工厂注册并从 DI 解析的 gRPC 客户端将自动使用应用的配置日志记录。</span><span class="sxs-lookup"><span data-stu-id="edc39-135">A gRPC client registered with the client factory and resolved from DI will automatically use the app's configured logging.</span></span>

<span data-ttu-id="edc39-136">如果你的应用未使用 DI，则可以使用`ILoggerFactory` [server.loggerfactory](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*)创建新的实例。</span><span class="sxs-lookup"><span data-stu-id="edc39-136">If your app isn't using DI then you can create a new `ILoggerFactory` instance with [LoggerFactory.Create](xref:Microsoft.Extensions.Logging.LoggerFactory.Create*).</span></span> <span data-ttu-id="edc39-137">若要访问此方法，请向应用程序中[添加 ""。](https://www.nuget.org/packages/microsoft.extensions.logging/)</span><span class="sxs-lookup"><span data-stu-id="edc39-137">To access this method add the [Microsoft.Extensions.Logging](https://www.nuget.org/packages/microsoft.extensions.logging/) package to your app.</span></span>

[!code-csharp[](diagnostics/net-client-loggerfactory-create.cs?highlight=1,8)]

### <a name="sample-logging-output"></a><span data-ttu-id="edc39-138">日志记录输出示例</span><span class="sxs-lookup"><span data-stu-id="edc39-138">Sample logging output</span></span>

<span data-ttu-id="edc39-139">下面是 gRPC 客户端`Debug`级别的控制台输出示例：</span><span class="sxs-lookup"><span data-stu-id="edc39-139">Here is an example of console output at the `Debug` level of a gRPC client:</span></span>

```
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Starting gRPC call. Method type: 'Unary', URI: 'https://localhost:5001/Greet.Greeter/SayHello'.
dbug: Grpc.Net.Client.Internal.GrpcCall[6]
      Sending message.
dbug: Grpc.Net.Client.Internal.GrpcCall[1]
      Reading message.
dbug: Grpc.Net.Client.Internal.GrpcCall[4]
      Finished gRPC call.
```

## <a name="additional-resources"></a><span data-ttu-id="edc39-140">其他资源</span><span class="sxs-lookup"><span data-stu-id="edc39-140">Additional resources</span></span>

* <xref:fundamentals/logging/index>
* <xref:grpc/configuration>
* <xref:grpc/clientfactory>
