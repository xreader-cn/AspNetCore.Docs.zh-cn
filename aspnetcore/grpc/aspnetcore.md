---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 了解 ASP.NET Core 编写 gRPC services 时的基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 08/07/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 38111c152c581c50767f9cd4e5fa257bd3fd804e
ms.sourcegitcommit: 476ea5ad86a680b7b017c6f32098acd3414c0f6c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/14/2019
ms.locfileid: "69022314"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="0da8a-103">使用 ASP.NET Core 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="0da8a-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="0da8a-104">本文档演示如何使用 ASP.NET Core 开始使用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="0da8a-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0da8a-105">系统必备</span><span class="sxs-lookup"><span data-stu-id="0da8a-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0da8a-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0da8a-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="0da8a-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0da8a-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="0da8a-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0da8a-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="0da8a-109">开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="0da8a-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="0da8a-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="0da8a-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="0da8a-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0da8a-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="0da8a-112">有关如何创建 gRPC 项目的详细说明, 请参阅[gRPC services 入门](xref:tutorials/grpc/grpc-start)。</span><span class="sxs-lookup"><span data-stu-id="0da8a-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="0da8a-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0da8a-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="0da8a-114">在命令行中运行 `dotnet new grpc -o GrpcGreeter`。</span><span class="sxs-lookup"><span data-stu-id="0da8a-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="0da8a-115">将 gRPC 服务添加到 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="0da8a-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="0da8a-116">gRPC 需要[gRPC](https://www.nuget.org/packages/Grpc.AspNetCore)包。</span><span class="sxs-lookup"><span data-stu-id="0da8a-116">gRPC requires the [Grpc.AspNetCore](https://www.nuget.org/packages/Grpc.AspNetCore) package.</span></span>

### <a name="configure-grpc"></a><span data-ttu-id="0da8a-117">配置 gRPC</span><span class="sxs-lookup"><span data-stu-id="0da8a-117">Configure gRPC</span></span>

<span data-ttu-id="0da8a-118">gRPC 是通过`AddGrpc`方法启用的:</span><span class="sxs-lookup"><span data-stu-id="0da8a-118">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="0da8a-119">每个 gRPC 服务通过`MapGrpcService`方法添加到路由管道:</span><span class="sxs-lookup"><span data-stu-id="0da8a-119">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-csharp[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="0da8a-120">ASP.NET Core 中间件和功能共享路由管道, 因此可以将应用配置为提供其他请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="0da8a-120">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="0da8a-121">其他请求处理程序 (如 MVC 控制器) 与已配置的 gRPC 服务并行工作。</span><span class="sxs-lookup"><span data-stu-id="0da8a-121">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="0da8a-122">与 ASP.NET Core Api 集成</span><span class="sxs-lookup"><span data-stu-id="0da8a-122">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="0da8a-123">gRPC 服务对 ASP.NET Core 功能 (如[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 和[日志记录](xref:fundamentals/logging/index)) 具有完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="0da8a-123">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="0da8a-124">例如, 服务实现可以通过构造函数从 DI 容器解析记录器服务:</span><span class="sxs-lookup"><span data-stu-id="0da8a-124">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="0da8a-125">默认情况下, gRPC 服务实现可以解析具有任意生存期 (单独、作用域或暂时性) 的其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="0da8a-125">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="0da8a-126">解析 gRPC 方法中的 HttpContext</span><span class="sxs-lookup"><span data-stu-id="0da8a-126">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="0da8a-127">GRPC API 提供对某些 HTTP/2 消息数据 (如方法、主机、标头和尾部) 的访问权限。</span><span class="sxs-lookup"><span data-stu-id="0da8a-127">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="0da8a-128">通过传递给每`ServerCallContext`个 gRPC 方法的参数访问:</span><span class="sxs-lookup"><span data-stu-id="0da8a-128">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="0da8a-129">`ServerCallContext`在所有 ASP.NET api 中都`HttpContext`不提供对的完全访问权限。</span><span class="sxs-lookup"><span data-stu-id="0da8a-129">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="0da8a-130">扩展方法提供对在 ASP.NET api 中`HttpContext`表示基础 HTTP/2 消息的完全访问权限: `GetHttpContext`</span><span class="sxs-lookup"><span data-stu-id="0da8a-130">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-csharp[](~/grpc/aspnetcore/sample/GrcpService/GreeterService2.cs?highlight=6-7&name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="0da8a-131">其他资源</span><span class="sxs-lookup"><span data-stu-id="0da8a-131">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
