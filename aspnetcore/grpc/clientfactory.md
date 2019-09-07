---
title: .NET Core 中的 gRPC 客户端工厂集成
author: jamesnk
description: 了解如何使用客户端工厂创建 gRPC 客户端。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/clientfactory
ms.openlocfilehash: 5d719893e96ae017e2de0ee1744003d2d67a49c9
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773666"
---
# <a name="grpc-client-factory-integration-in-net-core"></a>.NET Core 中的 gRPC 客户端工厂集成

与的`HttpClientFactory` gRPC 集成提供了一种集中式方式来创建 gRPC 客户端。 它可用作[配置独立 gRPC 客户端实例](xref:grpc/client)的替代方法。 工厂集成在[Grpc ClientFactory](https://www.nuget.org/packages/Grpc.Net.ClientFactory) NuGet 包中提供。

工厂具有以下优势：

* 提供用于配置逻辑 gRPC 客户端实例的中心位置
* 管理基础的生存期`HttpClientMessageHandler`
* 在 ASP.NET Core gRPC 服务中自动传播截止时间和取消

## <a name="register-grpc-clients"></a>注册 gRPC 客户端

若要注册 gRPC 客户端，可以`AddGrpcClient`在中`Startup.ConfigureServices`使用泛型扩展方法，并指定 gRPC 类型化客户端类和服务地址：

```csharp
services.AddGrpcClient<Greeter.GreeterClient>(o =>
{
    o.Address = new Uri("https://localhost:5001");
});
```

GRPC 客户端类型被注册为具有依赖关系注入（DI）的暂时性。 现在可以在 DI 创建的类型中直接注入和使用客户端。 ASP.NET Core MVC 控制器，SignalR 中心和 gRPC 服务是可自动注入 gRPC 客户端的位置：

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

`HttpClientFactory`创建 gRPC `HttpClient`客户端使用的。 标准`HttpClientFactory`方法可用于添加传出请求中间件或配置的`HttpClient`基础`HttpClientHandler` ：

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
* 添加`Interceptor`客户端在进行 gRPC 调用时将使用的实例。

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

可以使用`EnableCallContextPropagation()`将 gRPC 服务中的工厂创建的 gRPC 客户端配置为自动将截止时间和取消标记传播到子调用。 [Grpc. AspNetCore ClientFactory](https://www.nuget.org/packages/Grpc.AspNetCore.Server.ClientFactory) NuGet 包中提供了扩展方法。`EnableCallContextPropagation()`

调用上下文传播的工作方式是从当前 gRPC 请求上下文中读取截止时间和取消标记，并自动将其传播到 gRPC 客户端发出的传出调用。 调用上下文传播是确保复杂的嵌套 gRPC 方案始终传播截止时间和取消的极佳方式。

```csharp
services
    .AddGrpcClient<Greeter.GreeterClient>(o =>
    {
        o.Address = new Uri("https://localhost:5001");
    })
    .EnableCallContextPropagation();
```

有关截止时间和 RPC 取消的详细信息，请参阅[rpc 生命周期](https://www.grpc.io/docs/guides/concepts/#rpc-life-cycle)。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/client>
* <xref:fundamentals/http-requests>
