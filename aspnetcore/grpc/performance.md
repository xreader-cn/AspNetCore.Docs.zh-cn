---
title: 适用于 ASP.NET Core 的 gRPC 中的性能最佳做法
author: jamesnk
description: 了解生成高性能 gRPC 服务的最佳做法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
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
uid: grpc/performance
ms.openlocfilehash: f9cefa89ec6e533920b33223b34333f6ebe38428
ms.sourcegitcommit: 4df148cbbfae9ec8d377283ee71394944a284051
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/26/2020
ms.locfileid: "88876719"
---
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a>适用于 ASP.NET Core 的 gRPC 中的性能最佳做法

作者：[James Newton-King](https://twitter.com/jamesnk)

gRPC 专用于高性能服务。 本文档介绍如何从 gRPC 获得最佳性能。

## <a name="reuse-channel"></a>重用通道

进行 gRPC 调用时，应重新使用 gRPC 通道。 重用通道后通过现有的 HTTP/2 连接对调用进行多路复用。

如果为每个 gRPC 调用创建一个新通道，则完成此操作所需的时间可能会显著增加。 每次调用都需要在客户端和服务器之间进行多个网络往返，以创建 HTTP/2 连接：

1. 打开套接字
2. 建立 TCP 连接
3. 协商 TLS
4. 启动 HTTP/2 连接
5. 进行 gRPC 调用

在 gRPC 调用之间可以安全地共享和重用通道：

* gRPC 客户端是使用通道创建的。 gRPC 客户端是轻型对象，无需缓存或重用。
* 可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。
* 通道和从该通道创建的客户端可由多个线程安全使用。
* 从通道创建的客户端可同时进行多个调用。

## <a name="connection-concurrency"></a>连接并发

HTTP/2 连接通常会限制一个连接上同时存在的[最大并发流（活动 HTTP 请求）数](https://http2.github.io/http2-spec/#rfc.section.5.1.2)。 默认情况下，大多数服务器将此限制设置为 100 个并发流。

gRPC 通道使用单个 HTTP/2 连接，并且并发调用在该连接上多路复用。 当活动调用数达到连接流限制时，其他调用会在客户端中排队。 排队调用等待活动调用完成后再发送。 由于此限制，具有高负载或长时间运行的流式处理 gRPC 调用的应用程序可能会因调用排队而出现性能问题。

::: moniker range=">= aspnetcore-5.0"

.NET 5 引入 `SocketsHttpHandler.EnableMultipleHttp2Connections` 属性。 如果设置为 `true`，则当达到并发流限制时，通道会创建额外的 HTTP/2 连接。 创建 `GrpcChannel` 时，会自动将其内部 `SocketsHttpHandler` 配置为创建额外的 HTTP/2 连接。 如果应用配置其自己的处理程序，请考虑将 `EnableMultipleHttp2Connections` 设置为 `true`：

```csharp
var channel = GrpcChannel.ForAddress("https://localhost", new GrpcChannelOptions
{
    HttpHandler = new SocketsHttpHandler
    {
        EnableMultipleHttp2Connections = true,

        // ...configure other handler settings
    }
});
```

::: moniker-end

.NET Core 3.1 应用有几种解决方法：

* 为具有高负载的应用的区域创建单独的 gRPC 通道。 例如，`Logger` gRPC 服务可能具有高负载。 使用单独的通道在应用中创建 `LoggerClient`。
* 使用 gRPC 通道池，例如创建 gRPC 通道列表。 每次需要 gRPC 通道时，使用 `Random` 从列表中选取一个通道。 使用 `Random` 在多个连接上随机分配调用。

> [!IMPORTANT]
> 提升服务器上的最大并发流限制是解决此问题的另一种方法。 在 Kestrel 中，这是用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 配置的。
>
> 不建议提升最大并发流限制。 单个 HTTP/2 连接上的流过多会带来新的性能问题：
>
> * 尝试写入连接的流之间发生线程争用。
> * 连接数据包丢失导致在 TCP 层阻止所有调用。

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a>保持活动 ping

保持活动 ping 可用于在非活动期间使 HTTP/2 连接保持为活动状态。 如果在应用恢复活动时已准备好现有 HTTP/2 连接，则可以快速进行初始 gRPC 调用，而不会因重新建立连接而导致延迟。

在 <xref:System.Net.Http.SocketsHttpHandler> 上配置保持活动 ping：

```csharp
var handler = new SocketsHttpHandler
{
    PooledConnectionIdleTimeout = Timeout.InfiniteTimeSpan,
    KeepAlivePingDelay = TimeSpan.FromSeconds(60),
    KeepAlivePingTimeout = TimeSpan.FromSeconds(30),
    EnableMultipleHttp2Connections = true
};

var channel = GrpcChannel.ForAddress("https://localhost:5001", new GrpcChannelOptions
{
    HttpHandler = handler
});
```

前面的代码配置了一个通道，该通道在非活动期间每 60 秒向服务器发送一次保持活动 ping。 ping 确保服务器和使用中的任何代理不会由于不活动而关闭连接。

::: moniker-end

## <a name="streaming"></a>流式处理

在高性能方案中，可使用 gRPC 双向流式处理取代一元 gRPC 调用。 双向流启动后，来回流式处理消息比使用多个一元 gRPC 调用发送消息更快。 流式处理消息作为现有 HTTP/2 请求上的数据发送，节省了为每个一元调用创建新的 HTTP/2 请求的开销。

示例服务：

```csharp
public override async Task SayHello(IAsyncStreamReader<HelloRequest> requestStream,
    IServerStreamWriter<HelloReply> responseStream, ServerCallContext context)
{
    await foreach (var request in requestStream.ReadAllAsync())
    {
        var helloReply = new HelloReply { Message = "Hello " + request.Name };

        await responseStream.WriteAsync(helloReply);
    }
}
```

示例客户端：

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHello();

Console.WriteLine("Type a name then press enter.");
while (true)
{
    var text = Console.ReadLine();

    // Send and receive messages over the stream
    await call.RequestStream.WriteAsync(new HelloRequest { Name = text });
    await call.ResponseStream.MoveNext();

    Console.WriteLine($"Greeting: {call.ResponseStream.Current.Message}");
}
```

将一元调用替换为双向流式处理是一种高级技术，由于性能原因，这在许多情况下并不适用。

有以下情况时，使用流式处理调用是一个不错的选择：

1. 需要高吞吐量或低延迟。
2. gRPC 和 HTTP/2 被标识为性能瓶颈。
3. 客户端的辅助程序使用 gRPC 服务发送或接收常规消息。

请注意使用流式处理调用而不是一元调用的其他复杂性和限制：

1. 流可能会因服务或连接错误而中断。 需要在出现错误时重启流的逻辑。
2. 对于多线程处理，`RequestStream.WriteAsync` 并不安全。 一次只能将一条消息写入流中。 通过单个流从多个线程发送消息需要制造者/使用者队列（如 <xref:System.Threading.Channels.Channel%601>）来整理消息。
3. gRPC 流式处理方法仅限于接收一种类型的消息并发送一种类型的消息。 例如，`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 并发送 `ResponseMessage`。 Protobuf 对使用 `Any` 和 `oneof` 支持未知消息或条件消息，可以解决此限制。
