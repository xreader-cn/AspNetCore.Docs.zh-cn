---
title: 使用 ASP.NET Core 的 gRPC 服务
author: juntaoluo
description: 使用 ASP.NET Core 中编写 gRPC 服务时，请了解基本概念。
monikerRange: '>= aspnetcore-3.0'
ms.author: johluo
ms.date: 03/08/2019
uid: grpc/aspnetcore
ms.openlocfilehash: 5937ca9f2a783c4dabe324dae828b97953782938
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555869"
---
# <a name="grpc-services-with-aspnet-core"></a><span data-ttu-id="1c603-103">使用 ASP.NET Core 的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="1c603-103">gRPC services with ASP.NET Core</span></span>

<span data-ttu-id="1c603-104">本文档演示如何使用 ASP.NET Core gRPC 服务入门。</span><span class="sxs-lookup"><span data-stu-id="1c603-104">This document shows how to get started with gRPC services using ASP.NET Core.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="1c603-105">系统必备</span><span class="sxs-lookup"><span data-stu-id="1c603-105">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c603-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c603-106">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1c603-107">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1c603-107">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1c603-108">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c603-108">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="get-started-with-grpc-service-in-aspnet-core"></a><span data-ttu-id="1c603-109">开始使用 ASP.NET Core 中的 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="1c603-109">Get started with gRPC service in ASP.NET Core</span></span>

<span data-ttu-id="1c603-110">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="1c603-110">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/grpc/grpc-start/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1c603-111">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1c603-111">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="1c603-112">请参阅[gRPC 服务入门](xref:tutorials/grpc/grpc-start)有关如何创建 gRPC 项目的详细说明。</span><span class="sxs-lookup"><span data-stu-id="1c603-112">See [Get started with gRPC services](xref:tutorials/grpc/grpc-start) for detailed instructions on how to create a gRPC project.</span></span>

# <a name="visual-studio-code--visual-studio-for-mactabvisual-studio-codevisual-studio-mac"></a>[<span data-ttu-id="1c603-113">Visual Studio Code / Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1c603-113">Visual Studio Code / Visual Studio for Mac</span></span>](#tab/visual-studio-code+visual-studio-mac)

<span data-ttu-id="1c603-114">在命令行中运行 `dotnet new grpc -o GrpcGreeter`。</span><span class="sxs-lookup"><span data-stu-id="1c603-114">Run `dotnet new grpc -o GrpcGreeter` from the command line.</span></span>

---

## <a name="add-grpc-services-to-an-aspnet-core-app"></a><span data-ttu-id="1c603-115">将 gRPC 服务添加到 ASP.NET Core 应用</span><span class="sxs-lookup"><span data-stu-id="1c603-115">Add gRPC services to an ASP.NET Core app</span></span>

<span data-ttu-id="1c603-116">gRPC 需要以下程序包：</span><span class="sxs-lookup"><span data-stu-id="1c603-116">gRPC requires the following packages:</span></span>

* [<span data-ttu-id="1c603-117">Grpc.AspNetCore.Server</span><span class="sxs-lookup"><span data-stu-id="1c603-117">Grpc.AspNetCore.Server</span></span>](https://www.nuget.org/packages/Grpc.AspNetCore.Server)
* <span data-ttu-id="1c603-118">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/)为 protobuf 消息 Api。</span><span class="sxs-lookup"><span data-stu-id="1c603-118">[Google.Protobuf](https://www.nuget.org/packages/Google.Protobuf/) for protobuf message APIs.</span></span>
* [<span data-ttu-id="1c603-119">Grpc.Tools</span><span class="sxs-lookup"><span data-stu-id="1c603-119">Grpc.Tools</span></span>](https://www.nuget.org/packages/Grpc.Tools/)

### <a name="configure-grpc"></a><span data-ttu-id="1c603-120">配置 gRPC</span><span class="sxs-lookup"><span data-stu-id="1c603-120">Configure gRPC</span></span>

<span data-ttu-id="1c603-121">使用启用 gRPC`AddGrpc`方法：</span><span class="sxs-lookup"><span data-stu-id="1c603-121">gRPC is enabled with the `AddGrpc` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=7)]

<span data-ttu-id="1c603-122">每个 gRPC 服务添加到路由通过管道`MapGrpcService`方法：</span><span class="sxs-lookup"><span data-stu-id="1c603-122">Each gRPC service is added to the routing pipeline through the `MapGrpcService` method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Startup.cs?name=snippet&highlight=24)]

<span data-ttu-id="1c603-123">ASP.NET Core 中间件和功能共享路由管道，因此配置应用以提供额外的请求处理程序。</span><span class="sxs-lookup"><span data-stu-id="1c603-123">ASP.NET Core middlewares and features share the routing pipeline, therefore an app can be configured to serve additional request handlers.</span></span> <span data-ttu-id="1c603-124">与配置的 gRPC 服务同时工作的其他请求处理程序，如 MVC 控制器。</span><span class="sxs-lookup"><span data-stu-id="1c603-124">The additional request handlers, such as MVC controllers, work in parallel with the configured gRPC services.</span></span>

## <a name="integration-with-aspnet-core-apis"></a><span data-ttu-id="1c603-125">与 ASP.NET Core Api 集成</span><span class="sxs-lookup"><span data-stu-id="1c603-125">Integration with ASP.NET Core APIs</span></span>

<span data-ttu-id="1c603-126">gRPC 服务具有完全访问权限的 ASP.NET Core 功能，如[依赖关系注入](xref:fundamentals/dependency-injection)(DI) 和[日志记录](xref:fundamentals/logging/index)。</span><span class="sxs-lookup"><span data-stu-id="1c603-126">gRPC services have full access to the ASP.NET Core features such as [Dependency Injection](xref:fundamentals/dependency-injection) (DI) and [Logging](xref:fundamentals/logging/index).</span></span> <span data-ttu-id="1c603-127">例如，服务实现可能会解决从通过构造函数的 DI 容器的记录器服务：</span><span class="sxs-lookup"><span data-stu-id="1c603-127">For example, the service implementation can resolve a logger service from the DI container via the constructor:</span></span>

```csharp
public class GreeterService : Greeter.GreeterBase
{
    public GreeterService(ILogger<GreeterService> logger)
    {
    }
}
```

<span data-ttu-id="1c603-128">默认情况下，gRPC 服务实现可以解析任何生存期 （单一实例、 作用域内或暂时） 与其他 DI 服务。</span><span class="sxs-lookup"><span data-stu-id="1c603-128">By default, the gRPC service implementation can resolve other DI services with any lifetime (Singleton, Scoped, or Transient).</span></span>

### <a name="resolve-httpcontext-in-grpc-methods"></a><span data-ttu-id="1c603-129">GRPC 方法中解决 HttpContext</span><span class="sxs-lookup"><span data-stu-id="1c603-129">Resolve HttpContext in gRPC methods</span></span>

<span data-ttu-id="1c603-130">GRPC API 提供了访问某些 HTTP/2 消息数据，如方法、 主机、 标头和尾部。</span><span class="sxs-lookup"><span data-stu-id="1c603-130">The gRPC API provides access to some HTTP/2 message data, such as the method, host, header, and trailers.</span></span> <span data-ttu-id="1c603-131">访问是通过`ServerCallContext`自变量传递给每个 gRPC 方法：</span><span class="sxs-lookup"><span data-stu-id="1c603-131">Access is through the `ServerCallContext` argument passed to each gRPC method:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?highlight=3-4&name=snippet)]

<span data-ttu-id="1c603-132">`ServerCallContext` 不提供完全访问权限`HttpContext`所有 ASP.NET Api 中。</span><span class="sxs-lookup"><span data-stu-id="1c603-132">`ServerCallContext` does not provide full access to `HttpContext` in all ASP.NET APIs.</span></span> <span data-ttu-id="1c603-133">`GetHttpContext`扩展方法提供了全面的`HttpContext`表示 ASP.NET Api 中的基础 HTTP/2 消息：</span><span class="sxs-lookup"><span data-stu-id="1c603-133">The `GetHttpContext` extension method provides full access to the `HttpContext` representing the underlying HTTP/2 message in ASP.NET APIs:</span></span>

[!code-cs[](~/tutorials/grpc/grpc-start/sample/GrpcGreeter/Services/GreeterService.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="1c603-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="1c603-134">Additional resources</span></span>

* <xref:tutorials/grpc/grpc-start>
* <xref:grpc/index>
* <xref:grpc/basics>
* <xref:grpc/migration>
