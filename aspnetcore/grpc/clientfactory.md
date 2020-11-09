---
title: .NET Core 中的 gRPC 客户端工厂集成
author: jamesnk
description: 了解如何使用客户端工厂创建 gRPC 客户端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/clientfactory
ms.openlocfilehash: c63bf495f558237ed801881d378953119791b8ce
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060945"
---
# <a name="grpc-client-factory-integration-in-net-core"></a><span data-ttu-id="d6f7c-103">.NET Core 中的 gRPC 客户端工厂集成</span><span class="sxs-lookup"><span data-stu-id="d6f7c-103">gRPC client factory integration in .NET Core</span></span>

<span data-ttu-id="d6f7c-104">gRPC 与 `HttpClientFactory` 的集成提供了一种创建 gRPC 客户端的集中方式。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-104">gRPC integration with `HttpClientFactory` offers a centralized way to create gRPC clients.</span></span> <span data-ttu-id="d6f7c-105">它可用作[配置独立 gRPC 客户端实例](xref:grpc/client)的替代方法。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-105">It can be used as an alternative to [configuring stand-alone gRPC client instances](xref:grpc/client).</span></span> <span data-ttu-id="d6f7c-106">[Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 包中提供了工厂集成。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-106">Factory integration is available in the [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="d6f7c-107">工厂具有以下优势：</span><span class="sxs-lookup"><span data-stu-id="d6f7c-107">The factory offers the following benefits:</span></span>

* <span data-ttu-id="d6f7c-108">提供了用于配置逻辑 gRPC 客户端实例的中心位置</span><span class="sxs-lookup"><span data-stu-id="d6f7c-108">Provides a central location for configuring logical gRPC client instances</span></span>
* <span data-ttu-id="d6f7c-109">可管理基础 `HttpClientMessageHandler` 的生存期</span><span class="sxs-lookup"><span data-stu-id="d6f7c-109">Manages the lifetime of the underlying `HttpClientMessageHandler`</span></span>
* <span data-ttu-id="d6f7c-110">在 ASP.NET Core gRPC 服务中自动传播截止时间和取消</span><span class="sxs-lookup"><span data-stu-id="d6f7c-110">Automatic propagation of deadline and cancellation in an ASP.NET Core gRPC service</span></span>

## <a name="register-grpc-clients"></a><span data-ttu-id="d6f7c-111">注册 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="d6f7c-111">Register gRPC clients</span></span>

<span data-ttu-id="d6f7c-112">若要注册 gRPC 客户端，可在 `Startup.ConfigureServices` 中使用通用的 `AddGrpcClient` 扩展方法，并指定 gRPC 类型化客户端类和服务地址：</span><span class="sxs-lookup"><span data-stu-id="d6f7c-112">To register a gRPC client, the generic `AddGrpcClient` extension method can be used within `Startup.ConfigureServices`, specifying the gRPC typed client class and service address:</span></span>

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

<span data-ttu-id="d6f7c-113">gRPC 客户端类型通过依赖项注入 (DI) 注册为暂时性。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-113">The gRPC client type is registered as transient with dependency injection (DI).</span></span> <span data-ttu-id="d6f7c-114">现在可以在由 DI 创建的类型中直接注入和使用客户端。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-114">The client can now be injected and consumed directly in types created by DI.</span></span> <span data-ttu-id="d6f7c-115">ASP.NET Core MVC 控制器、SignalR 中心和 gRPC 服务是可以自动注入 gRPC 客户端的位置：</span><span class="sxs-lookup"><span data-stu-id="d6f7c-115">ASP.NET Core MVC controllers, SignalR hubs and gRPC services are places where gRPC clients can automatically be injected:</span></span>

```csharp
public class AggregatorService : Aggregator.AggregatorBase
{
    private readonly Greeter.GreeterClient _client;

    public AggregatorService(Greeter.GreeterClient client)
    {
        _client = client;
    }

    public override async Task SayHellos(HelloRequest request,
        IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
    {
        // Forward the call on to the greeter service
        using (var call = _client.SayHellos(request))
        {
            await foreach (var response in call.ResponseStream.ReadAllAsync())
            {
                await responseStream.WriteAsync(response);
            }
        }
    }
}
```

## <a name="configure-httpclient"></a><span data-ttu-id="d6f7c-116">配置 HttpClient</span><span class="sxs-lookup"><span data-stu-id="d6f7c-116">Configure HttpClient</span></span>

<span data-ttu-id="d6f7c-117">`HttpClientFactory` 创建 gRPC 客户端使用的 `HttpClient`。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-117">`HttpClientFactory` creates the `HttpClient` used by the gRPC client.</span></span> <span data-ttu-id="d6f7c-118">标准 `HttpClientFactory` 方法可用于添加传出请求中间件或配置 `HttpClient` 的基础 `HttpClientHandler`：</span><span class="sxs-lookup"><span data-stu-id="d6f7c-118">Standard `HttpClientFactory` methods can be used to add outgoing request middleware or to configure the underlying `HttpClientHandler` of the `HttpClient`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .ConfigurePrimaryHttpMessageHandler(() =>
    {
        var handler = new HttpClientHandler();
        handler.ClientCertificates.Add(LoadCertificate());
        return handler;
    });
```

<span data-ttu-id="d6f7c-119">有关详细信息，请参阅[使用 IHttpClientFactory 发出 HTTP 请求](xref:fundamentals/http-requests)。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-119">For more information, see [Make HTTP requests using IHttpClientFactory](xref:fundamentals/http-requests).</span></span>

## <a name="configure-channel-and-interceptors"></a><span data-ttu-id="d6f7c-120">配置通道和侦听器</span><span class="sxs-lookup"><span data-stu-id="d6f7c-120">Configure Channel and Interceptors</span></span>

<span data-ttu-id="d6f7c-121">特定于 gRPC 的方法可用于：</span><span class="sxs-lookup"><span data-stu-id="d6f7c-121">gRPC-specific methods are available to:</span></span>

* <span data-ttu-id="d6f7c-122">配置 gRPC 客户端的基础通道。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-122">Configure a gRPC client's underlying channel.</span></span>
* <span data-ttu-id="d6f7c-123">添加客户端在进行 gRPC 调用时将使用的 `Interceptor` 实例。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-123">Add `Interceptor` instances that the client will use when making gRPC calls.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .AddInterceptor(() => new LoggingInterceptor())
    .ConfigureChannel(o =>
    {
        o.Credentials = new CustomCredentials();
    });
```

## <a name="deadline-and-cancellation-propagation"></a><span data-ttu-id="d6f7c-124">截止时间和取消传播</span><span class="sxs-lookup"><span data-stu-id="d6f7c-124">Deadline and cancellation propagation</span></span>

<span data-ttu-id="d6f7c-125">可以使用 `EnableCallContextPropagation()` 对 gRPC 服务中工厂所创建的 gRPC 客户端进行配置，以自动将截止时间和取消令牌传播到子调用。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-125">gRPC clients created by the factory in a gRPC service can be configured with `EnableCallContextPropagation()` to automatically propagate the deadline and cancellation token to child calls.</span></span> <span data-ttu-id="d6f7c-126">[Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 包中提供了 `EnableCallContextPropagation()` 扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-126">The `EnableCallContextPropagation()` extension method is available in the [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet package.</span></span>

<span data-ttu-id="d6f7c-127">调用上下文传播的工作方式是：从当前 gRPC 请求上下文中读取截止时间和取消令牌，并自动将其传播到 gRPC 客户端所发出的传出调用。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-127">Call context propagation works by reading the deadline and cancellation token from the current gRPC request context and automatically propagating them to outgoing calls made by the gRPC client.</span></span> <span data-ttu-id="d6f7c-128">调用上下文传播是确保复杂的嵌套 gRPC 场景始终传播截止时间和取消的一种极佳方式。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-128">Call context propagation is an excellent way of ensuring that complex, nested gRPC scenarios always propagate the deadline and cancellation.</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

<span data-ttu-id="d6f7c-129">默认情况下，如果客户端在 gRPC 调用的上下文之外使用，`EnableCallContextPropagation` 将引发错误。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-129">By default, `EnableCallContextPropagation` raises an error if the client is used outside the context of a gRPC call.</span></span> <span data-ttu-id="d6f7c-130">此错误旨在提醒你没有要传播的调用上下文。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-130">The error is designed to alert you that there isn't a call context to propagate.</span></span> <span data-ttu-id="d6f7c-131">如果要在调用上下文之外使用客户端，请使用 `SuppressContextNotFoundErrors` 在配置客户端时禁止显示该错误：</span><span class="sxs-lookup"><span data-stu-id="d6f7c-131">If you want to use the client outside of a call context, suppress the error when the client is configured with `SuppressContextNotFoundErrors`:</span></span>

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

<span data-ttu-id="d6f7c-132">有关截止时间和 RPC 取消的详细信息，请参阅 [RPC 生命周期](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)。</span><span class="sxs-lookup"><span data-stu-id="d6f7c-132">For more information about deadlines and RPC cancellation, see [RPC life cycle](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d6f7c-133">其他资源</span><span class="sxs-lookup"><span data-stu-id="d6f7c-133">Additional resources</span></span>

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
