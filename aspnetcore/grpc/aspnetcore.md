---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 了解 ASP.NET Core 编写 gRPC services 时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/28/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 128f5b36eac9112460c33693db5537134a077476
ms.sourcegitcommit: 23f79bd71d49c4efddb56377c1f553cc993d781b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/29/2019
ms.locfileid: "70130707"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="3cab7-103">使用 ASP.NET Core 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="3cab7-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="3cab7-104">本文档演示如何使用 ASP.NET Core 开始使用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="3cab7-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3cab7-105">系统必备</span><span class="sxs-lookup"><span data-stu-id="3cab7-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3cab7-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cab7-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="3cab7-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="3cab7-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="3cab7-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3cab7-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="3cab7-109">开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="3cab7-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="3cab7-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="3cab7-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="3cab7-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3cab7-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="3cab7-112">有关如何创建 gRPC 项目的详细说明, 请参阅[gRPC services 入门](xref:tutorials/grpc/grpc-start)。</span><span class="sxs-lookup"><span data-stu-id="3cab7-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="3cab7-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="3cab7-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="3cab7-114">在命令行中运行 `dotnet new grpc -o GrpcGreeter`。</span><span class="sxs-lookup"><span data-stu-id="3cab7-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="3cab7-115">将 gRPC 服务添加到 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="3cab7-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="3cab7-116">gRPC 需要[gRPC](https://www.nuget.org/packages/Grpc.AspNetCore)包。</span><span class="sxs-lookup"><span data-stu-id="3cab7-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="3cab7-117">配置 gRPC</span><span class="sxs-lookup"><span data-stu-id="3cab7-117">Configure gRPC</span></span>

<span data-ttu-id="3cab7-118">在 Startup.cs 中：</span><span class="sxs-lookup"><span data-stu-id="3cab7-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="3cab7-119">gRPC 是通过`AddGrpc`方法启用的。</span><span class="sxs-lookup"><span data-stu-id="3cab7-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="3cab7-120">每个 gRPC 服务通过`MapGrpcService`方法添加到路由管道。</span><span class="sxs-lookup"><span data-stu-id="3cab7-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="3cab7-121">ASP.NET Core 中间件和功能共享路由管道, 因此可以将应用配置为提供其他请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="3cab7-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="3cab7-122">其他请求处理程序 (如 MVC 控制器) 与已配置的 gRPC 服务并行工作。</span><span class="sxs-lookup"><span data-stu-id="3cab7-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="3cab7-123">配置 Kestrel</span><span class="sxs-lookup"><span data-stu-id="3cab7-123">Configure Kestrel</span></span>

<span data-ttu-id="3cab7-124">Kestrel gRPC 终结点:</span><span class="sxs-lookup"><span data-stu-id="3cab7-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="3cab7-125">需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="3cab7-125">Require HTTP/2.</span></span>
* <span data-ttu-id="3cab7-126">应通过 HTTPS 进行保护。</span><span class="sxs-lookup"><span data-stu-id="3cab7-126">Should be secured with HTTPS.</span></span>

#### <a name="http2"></a><span data-ttu-id="3cab7-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="3cab7-127">HTTP/2</span></span>

<span data-ttu-id="3cab7-128">在大多数现代操作系统上, Kestrel[支持 HTTP/2](xref:fundamentals/servers/kestrel#http2-support) 。</span><span class="sxs-lookup"><span data-stu-id="3cab7-128">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="3cab7-129">默认情况下, Kestrel 终结点配置为支持 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="3cab7-129">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

> [!NOTE]
> <span data-ttu-id="3cab7-130">macOS 不支持[传输层安全 (TLS)](https://tools.ietf.org/html/rfc5246)ASP.NET Core gRPC。</span><span class="sxs-lookup"><span data-stu-id="3cab7-130">macOS doesn't support ASP.NET Core gRPC with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span> <span data-ttu-id="3cab7-131">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="3cab7-131">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="3cab7-132">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="3cab7-132">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

#### <a name="https"></a><span data-ttu-id="3cab7-133">HTTPS</span><span class="sxs-lookup"><span data-stu-id="3cab7-133">HTTPS</span></span>

<span data-ttu-id="3cab7-134">用于 gRPC 的 Kestrel 终结点应使用 HTTPS 进行保护。</span><span class="sxs-lookup"><span data-stu-id="3cab7-134">Kestrel endpoints used for gRPC should be secured with HTTPS.</span></span> <span data-ttu-id="3cab7-135">在开发中, `https://localhost:5001`当存在 ASP.NET Core 开发证书时, 将自动创建 HTTPS 终结点。</span><span class="sxs-lookup"><span data-stu-id="3cab7-135">In development, an HTTPS endpoint is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="3cab7-136">不需要配置。</span><span class="sxs-lookup"><span data-stu-id="3cab7-136">No configuration is required.</span></span>

<span data-ttu-id="3cab7-137">在生产环境中，必须显式配置 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="3cab7-137">In production, HTTPS must be explicitly configured.</span></span> <span data-ttu-id="3cab7-138">在下面的*appsettings*示例中, 提供了一个使用 HTTPS 保护的 HTTP/2 终结点:</span><span class="sxs-lookup"><span data-stu-id="3cab7-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with HTTPS is provided:</span></span>

```json
{
  "Kestrel": {
    "Endpoints": {
      "HttpsDefaultCert": {
        "Url": "https://localhost:5001",
        "Protocols": "Http2"
      }
    },
    "Certificates": {
      "Default": {
        "Path": "<path to .pfx file>",
        "Password": "<certificate password>"
      }
    }
  }
}
```

<span data-ttu-id="3cab7-139">或者, 可以在*Program.cs*中配置 Kestrel endspoints:</span><span class="sxs-lookup"><span data-stu-id="3cab7-139">Alternatively, Kestrel endspoints can be configured in *Program.cs*:</span></span>

```csharp
public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        {
            webBuilder.ConfigureKestrel(options =>
            {
                // This endpoint will use HTTP/2 and HTTPS on port 5001.
                options.Listen(IPAddress.Any, 5001, listenOptions =>
                {
                    listenOptions.Protocols = HttpProtocols.Http2;
                    listenOptions.UseHttps("<path to .pfx file>", 
                        "<certificate password>");
                });
            });
            webBuilder.UseStartup<Startup>();
        });
```

<span data-ttu-id="3cab7-140">有关启用 HTTP/2 和 HTTPS with Kestrel 的详细信息, 请参阅[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="3cab7-140">For more information on enabling HTTP/2 and HTTPS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="3cab7-141">与 ASP.NET Core Api 集成</span><span class="sxs-lookup"><span data-stu-id="3cab7-141">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="3cab7-142">gRPC 服务对 ASP.NET Core 功能 (如[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 和[日志记录](xref:fundamentals/logging/index)) 具有完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="3cab7-142">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="3cab7-143">例如, 服务实现可以通过构造函数从 DI 容器解析记录器服务:</span><span class="sxs-lookup"><span data-stu-id="3cab7-143">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="3cab7-144">默认情况下, gRPC 服务实现可以解析具有任意生存期 (单独、作用域或暂时性) 的其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="3cab7-144">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="3cab7-145">解析 gRPC 方法中的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="3cab7-145">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="3cab7-146">GRPC API 提供对某些 HTTP/2 消息数据 (如方法、主机、标头和尾部) 的访问权限。</span><span class="sxs-lookup"><span data-stu-id="3cab7-146">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="3cab7-147">通过传递给每`ServerCallContext`个 gRPC 方法的参数访问:</span><span class="sxs-lookup"><span data-stu-id="3cab7-147">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="3cab7-148">`ServerCallContext`在所有 ASP.NET api 中都`HttpContext`不提供对的完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="3cab7-148">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="3cab7-149">扩展方法提供对在 ASP.NET api 中`HttpContext`表示基础 HTTP/2 消息的完全访问权限: `GetHttpContext`</span><span class="sxs-lookup"><span data-stu-id="3cab7-149">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="3cab7-150">其他资源</span><span class="sxs-lookup"><span data-stu-id="3cab7-150">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
