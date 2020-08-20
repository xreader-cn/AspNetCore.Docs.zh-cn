---
title: .NET Core 中的 gRPC 客户端工厂集成
author: jamesnk
description: 了解如何使用客户端工厂创建 gRPC 客户端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 05/26/2020
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
uid: grpc/clientfactory
ms.openlocfilehash: dfdcb8d73017c0c1ccb4cf2aaffdbe7c49030179
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88633729"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core 中的 gRPC 客户端工厂集成

gRPC 与 `HttpClientFactory` 的集成提供了一种创建 gRPC 客户端的集中方式。 它可用作[配置独立 gRPC 客户端实例](xref:grpc/client)的替代方法。 [Grpc.Net.ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 包中提供了工厂集成。

工厂具有以下优势：

* 提供了用于配置逻辑 gRPC 客户端实例的中心位置
* 可管理基础 `HttpClientMessageHandler` 的生存期
* 在 ASP.NET Core gRPC 服务中自动传播截止时间和取消

## <a name="register-grpc-clients"></a>注册 gRPC 客户端

若要注册 gRPC 客户端，可在 `Startup.ConfigureServices` 中使用通用的 `AddGrpcClient` 扩展方法，并指定 gRPC 类型化客户端类和服务地址：

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

gRPC 客户端类型通过依赖项注入 (DI) 注册为暂时性。 现在可以在由 DI 创建的类型中直接注入和使用客户端。 ASP.NET Core MVC 控制器、SignalR 中心和 gRPC 服务是可以自动注入 gRPC 客户端的位置：

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

## <a name="configure-httpclient"></a>配置 HttpClient

`HttpClientFactory` 创建 gRPC 客户端使用的 `HttpClient`。 标准 `HttpClientFactory` 方法可用于添加传出请求中间件或配置 `HttpClient` 的基础 `HttpClientHandler`：

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

有关详细信息，请参阅[使用 IHttpClientFactory 发出 HTTP 请求](xref:fundamentals/http-requests)。

## <a name="configure-channel-and-interceptors"></a>配置通道和侦听器

特定于 gRPC 的方法可用于：

* 配置 gRPC 客户端的基础通道。
* 添加客户端在进行 gRPC 调用时将使用的 `Interceptor` 实例。

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

## <a name="deadline-and-cancellation-propagation"></a>截止时间和取消传播

可以使用 `EnableCallContextPropagation()` 对 gRPC 服务中工厂所创建的 gRPC 客户端进行配置，以自动将截止时间和取消令牌传播到子调用。 [Grpc.AspNetCore.Server.ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 包中提供了 `EnableCallContextPropagation()` 扩展方法。

调用上下文传播的工作方式是：从当前 gRPC 请求上下文中读取截止时间和取消令牌，并自动将其传播到 gRPC 客户端所发出的传出调用。 调用上下文传播是确保复杂的嵌套 gRPC 场景始终传播截止时间和取消的一种极佳方式。

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

默认情况下，如果客户端在 gRPC 调用的上下文之外使用，`EnableCallContextPropagation` 将引发错误。 此错误旨在提醒你没有要传播的调用上下文。 如果要在调用上下文之外使用客户端，请使用 `SuppressContextNotFoundErrors` 在配置客户端时禁止显示该错误：

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation(o => o.SuppressContextNotFoundErrors = true);
```

有关截止时间和 RPC 取消的详细信息，请参阅 [RPC 生命周期](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
