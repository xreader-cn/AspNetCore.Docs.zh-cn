---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 了解 ASP.NET Core 编写 gRPC services 时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 09/03/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 2507ce6df05403cb19e8bfa2565d410d6140b144
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925071"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="23a6d-103">使用 ASP.NET Core 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="23a6d-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="23a6d-104">本文档演示如何使用 ASP.NET Core 开始使用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="23a6d-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="23a6d-105">先决条件</span><span class="sxs-lookup"><span data-stu-id="23a6d-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="23a6d-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23a6d-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="23a6d-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="23a6d-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="23a6d-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="23a6d-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="23a6d-109">开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="23a6d-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="23a6d-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="23a6d-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="23a6d-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="23a6d-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="23a6d-112">有关如何创建 gRPC 项目的详细说明, 请参阅[gRPC services 入门](xref:tutorials/grpc/grpc-start)。</span><span class="sxs-lookup"><span data-stu-id="23a6d-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="23a6d-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="23a6d-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="23a6d-114">在命令行中运行 `dotnet new grpc -o GrpcGreeter`。</span><span class="sxs-lookup"><span data-stu-id="23a6d-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="23a6d-115">将 gRPC 服务添加到 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="23a6d-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="23a6d-116">gRPC 需要[gRPC](https://www.nuget.org/packages/Grpc.AspNetCore)包。</span><span class="sxs-lookup"><span data-stu-id="23a6d-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="23a6d-117">配置 gRPC</span><span class="sxs-lookup"><span data-stu-id="23a6d-117">Configure gRPC</span></span>

<span data-ttu-id="23a6d-118">在 Startup.cs 中：</span><span class="sxs-lookup"><span data-stu-id="23a6d-118">In *Startup.cs*:</span></span>

* <span data-ttu-id="23a6d-119">gRPC 是通过`AddGrpc`方法启用的。</span><span class="sxs-lookup"><span data-stu-id="23a6d-119">gRPC is enabled with the `AddGrpc` method.</span></span>
* <span data-ttu-id="23a6d-120">每个 gRPC 服务通过`MapGrpcService`方法添加到路由管道。</span><span class="sxs-lookup"><span data-stu-id="23a6d-120">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method.</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7,24)]

<span data-ttu-id="23a6d-121">ASP.NET Core 中间件和功能共享路由管道, 因此可以将应用配置为提供其他请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="23a6d-121">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="23a6d-122">其他请求处理程序 (如 MVC 控制器) 与已配置的 gRPC 服务并行工作。</span><span class="sxs-lookup"><span data-stu-id="23a6d-122">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

### <a name="configure-kestrel"></a><span data-ttu-id="23a6d-123">配置 Kestrel</span><span class="sxs-lookup"><span data-stu-id="23a6d-123">Configure Kestrel</span></span>

<span data-ttu-id="23a6d-124">Kestrel gRPC 终结点：</span><span class="sxs-lookup"><span data-stu-id="23a6d-124">Kestrel gRPC endpoints:</span></span>

* <span data-ttu-id="23a6d-125">需要 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="23a6d-125">Require HTTP/2.</span></span>
* <span data-ttu-id="23a6d-126">应通过[传输层安全性（TLS）](https://tools.ietf.org/html/rfc5246)来保护。</span><span class="sxs-lookup"><span data-stu-id="23a6d-126">Should be secured with [Transport Layer Security (TLS)](https://tools.ietf.org/html/rfc5246).</span></span>

#### <a name="http2"></a><span data-ttu-id="23a6d-127">HTTP/2</span><span class="sxs-lookup"><span data-stu-id="23a6d-127">HTTP/2</span></span>

<span data-ttu-id="23a6d-128">gRPC 要求 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="23a6d-128">gRPC requires HTTP/2.</span></span> <span data-ttu-id="23a6d-129">gRPC for ASP.NET Core 验证[HttpRequest](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*)为`HTTP/2`。</span><span class="sxs-lookup"><span data-stu-id="23a6d-129">gRPC for ASP.NET Core validates [HttpRequest.Protocol](xref:Microsoft.AspNetCore.Http.HttpRequest.Protocol*) is `HTTP/2`.</span></span>

<span data-ttu-id="23a6d-130">在大多数现代操作系统上，Kestrel[支持 HTTP/2](xref:fundamentals/servers/kestrel#http2-support) 。</span><span class="sxs-lookup"><span data-stu-id="23a6d-130">Kestrel [supports HTTP/2](xref:fundamentals/servers/kestrel#http2-support) on most modern operating systems.</span></span> <span data-ttu-id="23a6d-131">默认情况下，Kestrel 终结点配置为支持 HTTP/1.1 和 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="23a6d-131">Kestrel endpoints are configured to support HTTP/1.1 and HTTP/2 connections by default.</span></span>

#### <a name="tls"></a><span data-ttu-id="23a6d-132">TLS</span><span class="sxs-lookup"><span data-stu-id="23a6d-132">TLS</span></span>

<span data-ttu-id="23a6d-133">用于 gRPC 的 Kestrel 终结点应使用 TLS 进行保护。</span><span class="sxs-lookup"><span data-stu-id="23a6d-133">Kestrel endpoints used for gRPC should be secured with TLS.</span></span> <span data-ttu-id="23a6d-134">在开发中，将在存在 ASP.NET Core 开发证书`https://localhost:5001`时，自动创建一个使用 TLS 保护的终结点。</span><span class="sxs-lookup"><span data-stu-id="23a6d-134">In development, an endpoint secured with TLS is automatically created at `https://localhost:5001` when the ASP.NET Core development certificate is present.</span></span> <span data-ttu-id="23a6d-135">不需要配置。</span><span class="sxs-lookup"><span data-stu-id="23a6d-135">No configuration is required.</span></span> <span data-ttu-id="23a6d-136">`https`前缀验证 Kestrel 终结点是否正在使用 TLS。</span><span class="sxs-lookup"><span data-stu-id="23a6d-136">An `https` prefix verifies the Kestrel endpoint is using TLS.</span></span>

<span data-ttu-id="23a6d-137">在生产环境中，必须显式配置 TLS。</span><span class="sxs-lookup"><span data-stu-id="23a6d-137">In production, TLS must be explicitly configured.</span></span> <span data-ttu-id="23a6d-138">在下面的*appsettings*示例中，提供了使用 TLS 保护的 HTTP/2 终结点：</span><span class="sxs-lookup"><span data-stu-id="23a6d-138">In the following *appsettings.json* example, an HTTP/2 endpoint secured with TLS is provided:</span></span>

[!code-json[](~/grpc/aspnetcore/sample/appsettings.json?highlight=4)]

<span data-ttu-id="23a6d-139">或者，可以在*Program.cs*中配置 Kestrel 终结点：</span><span class="sxs-lookup"><span data-stu-id="23a6d-139">Alternatively, Kestrel endpoints can be configured in *Program.cs*:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/Program.cs?highlight=7&name=snippet)]

#### <a name="protocol-negotiation"></a><span data-ttu-id="23a6d-140">协议协商</span><span class="sxs-lookup"><span data-stu-id="23a6d-140">Protocol negotiation</span></span>

<span data-ttu-id="23a6d-141">TLS 用于保护通信的安全性。</span><span class="sxs-lookup"><span data-stu-id="23a6d-141">TLS is used for more than securing communication.</span></span> <span data-ttu-id="23a6d-142">当终结点支持多个协议时，TLS[应用层协议协商（ALPN）](https://tools.ietf.org/html/rfc7301#section-3)握手用于协商客户端与服务器之间的连接协议。</span><span class="sxs-lookup"><span data-stu-id="23a6d-142">The TLS [Application-Layer Protocol Negotiation (ALPN)](https://tools.ietf.org/html/rfc7301#section-3) handshake is used to negotiate the connection protocol between the client and the server when an endpoint supports multiple protocols.</span></span> <span data-ttu-id="23a6d-143">此协商确定连接是使用 HTTP/1.1 还是 HTTP/2。</span><span class="sxs-lookup"><span data-stu-id="23a6d-143">This negotiation determines whether the connection uses HTTP/1.1 or HTTP/2.</span></span>

<span data-ttu-id="23a6d-144">如果在不使用 TLS 的情况下配置了 HTTP/2 终结点，则终结点的 [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) 必须设置为 `HttpProtocols.Http2`。</span><span class="sxs-lookup"><span data-stu-id="23a6d-144">If an HTTP/2 endpoint is configured without TLS, the endpoint's [ListenOptions.Protocols](xref:fundamentals/servers/kestrel#listenoptionsprotocols) must be set to `HttpProtocols.Http2`.</span></span> <span data-ttu-id="23a6d-145">具有多个协议（例如）的终结`HttpProtocols.Http1AndHttp2`点不能使用 TLS，因为没有协商。</span><span class="sxs-lookup"><span data-stu-id="23a6d-145">An endpoint with multiple protocols (for example, `HttpProtocols.Http1AndHttp2`) can't be used without TLS because there is no negotiation.</span></span> <span data-ttu-id="23a6d-146">到不安全终结点的所有连接默认为 HTTP/1.1，并且 gRPC 调用失败。</span><span class="sxs-lookup"><span data-stu-id="23a6d-146">All connections to the unsecured endpoint default to HTTP/1.1, and gRPC calls fail.</span></span>

<span data-ttu-id="23a6d-147">有关通过 Kestrel 启用 HTTP/2 和 TLS 的详细信息，请参阅[Kestrel 终结点配置](xref:fundamentals/servers/kestrel#endpoint-configuration)。</span><span class="sxs-lookup"><span data-stu-id="23a6d-147">For more information on enabling HTTP/2 and TLS with Kestrel, see [Kestrel endpoint configuration](xref:fundamentals/servers/kestrel#endpoint-configuration).</span></span>

> [!NOTE]
> <span data-ttu-id="23a6d-148">macOS 不支持 ASP.NET Core gRPC 及 TLS。</span><span class="sxs-lookup"><span data-stu-id="23a6d-148">macOS doesn't support ASP.NET Core gRPC with TLS.</span></span> <span data-ttu-id="23a6d-149">在 macOS 上成功运行 gRPC 服务需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="23a6d-149">Additional configuration is required to successfully run gRPC services on macOS.</span></span> <span data-ttu-id="23a6d-150">有关详细信息，请参阅[无法在 macOS 上启用 ASP.NET Core gRPC 应用](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos)。</span><span class="sxs-lookup"><span data-stu-id="23a6d-150">For more information, see [Unable to start ASP.NET Core gRPC app on macOS](xref:grpc/troubleshoot#unable-to-start-aspnet-core-grpc-app-on-macos).</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="23a6d-151">与 ASP.NET Core Api 集成</span><span class="sxs-lookup"><span data-stu-id="23a6d-151">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="23a6d-152">gRPC 服务对 ASP.NET Core 功能 (如[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 和[日志记录](xref:fundamentals/logging/index)) 具有完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="23a6d-152">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="23a6d-153">例如, 服务实现可以通过构造函数从 DI 容器解析记录器服务:</span><span class="sxs-lookup"><span data-stu-id="23a6d-153">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="23a6d-154">默认情况下, gRPC 服务实现可以解析具有任意生存期 (单独、作用域或暂时性) 的其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="23a6d-154">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="23a6d-155">解析 gRPC 方法中的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="23a6d-155">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="23a6d-156">GRPC API 提供对某些 HTTP/2 消息数据 (如方法、主机、标头和尾部) 的访问权限。</span><span class="sxs-lookup"><span data-stu-id="23a6d-156">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="23a6d-157">通过传递给每`ServerCallContext`个 gRPC 方法的参数访问:</span><span class="sxs-lookup"><span data-stu-id="23a6d-157">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="23a6d-158">`ServerCallContext`在所有 ASP.NET api 中都`HttpContext`不提供对的完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="23a6d-158">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="23a6d-159">扩展方法提供对在 ASP.NET api 中`HttpContext`表示基础 HTTP/2 消息的完全访问权限: `GetHttpContext`</span><span class="sxs-lookup"><span data-stu-id="23a6d-159">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

[!INCLUDE[](~/includes/gRPCazure.md)]

## <a name="additional-resources"></a><span data-ttu-id="23a6d-160">其他资源</span><span class="sxs-lookup"><span data-stu-id="23a6d-160">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:fundamentals/servers/kestrel>
