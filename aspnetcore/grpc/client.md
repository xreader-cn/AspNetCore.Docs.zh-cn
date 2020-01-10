---
title: 通过 .NET 客户端调用 gRPC 服务
author: jamesnk
description: 了解如何通过 .NET gRPC 客户端调用 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 1e7887388a752fb35d00e65db210c3924c6ab192
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75829096"
---
# <a name="call-grpc-services-with-the-net-client"></a>通过 .NET 客户端调用 gRPC 服务

.NET gRPC 客户端库在[gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包中提供。 本文档介绍如何执行以下操作：

* 配置 gRPC 客户端以调用 gRPC 服务。
* 对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。

## <a name="configure-grpc-client"></a>配置 gRPC 客户端

gRPC 客户端是从 [\*.proto 文件生成的](xref:grpc/basics#generated-c-assets)具体客户端类型。 具体 gRPC 客户端具有转换为 \*.proto 文件中 gRPC 服务的方法。

从通道创建 gRPC 客户端。 开始使用 `GrpcChannel.ForAddress` 创建通道，然后使用通道创建 gRPC 客户端：

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

通道表示 gRPC 服务的长期连接。 创建通道后，会将其配置为与调用服务相关的选项。 例如，可在 `GrpcChannelOptions` 上指定 `HttpClient`，并在 `GrpcChannel.ForAddress`上指定日志记录的最大发送和接收消息大小和日志记录。 有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

通道和客户端性能和使用情况：

* 创建通道是一种开销高昂的操作。 为 gRPC 调用重用通道可带来性能优势。
* 将通过通道创建 gRPC 客户端。 gRPC 客户端是轻型对象，无需缓存或重复使用。
* 可以从通道（包括不同类型的客户端）创建多个 gRPC 客户端。
* 通道和从通道创建的客户端可以安全地由多个线程使用。
* 从通道创建的客户端可以同时进行多次调用。

`GrpcChannel.ForAddress` 不是创建 gRPC 客户端的唯一选项。 如果要从 ASP.NET Core 的应用程序中调用 gRPC 服务，请考虑[gRPC 客户端工厂集成](xref:grpc/clientfactory)。 gRPC 与 `HttpClientFactory` 的集成提供了创建 gRPC 客户端的集中式替代方法。

> [!NOTE]
> 需要额外的配置才能[通过 .net 客户端调用不安全的 gRPC 服务](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。

## <a name="make-grpc-calls"></a>进行 gRPC 调用

GRPC 调用通过在客户端上调用方法来启动。 GRPC 客户端将处理消息序列化，并将 gRPC 调用寻址到正确的服务。

gRPC 具有不同类型的方法。 使用客户端进行 gRPC 调用的方式取决于所调用的方法的类型。 GRPC 方法类型为：

* 一元
* 服务器流式处理
* 客户端流式处理
* 双向流式处理

### <a name="unary-call"></a>一元调用

一元调用从客户端发送请求消息开始。 服务完成后，将返回响应消息。

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

*\*proto*文件中的每个一元服务方法都将导致用于调用方法的具体 gRPC 客户端类型上有两个 .net 方法：异步方法和阻塞方法。 例如，在 `GreeterClient` 有两种方法可调用 `SayHello`：

* `GreeterClient.SayHelloAsync`-异步调用 `Greeter.SayHello` 服务。 可以等待。
* `GreeterClient.SayHello`-调用 `Greeter.SayHello` 服务，直到完成。 不要在异步代码中使用。

### <a name="server-streaming-call"></a>服务器流式处理调用

服务器流式处理调用会从客户端发送请求消息开始。 `ResponseStream.MoveNext()` 读取从服务传输的消息。 `ResponseStream.MoveNext()` 返回 `false`时，服务器流调用已完成。

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

如果使用C#的是8或更高版本，则可以使用 `await foreach` 语法来读取消息。 `IAsyncStreamReader<T>.ReadAllAsync()` 扩展方法读取响应流中的所有消息：

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is written multiple times
    }
}
```

### <a name="client-streaming-call"></a>客户端流式处理调用

客户端流式处理调用在客户端发送消息的*情况下*启动。 客户端可以选择通过 `RequestStream.WriteAsync`发送消息。 当客户端已经完成发送消息 `RequestStream.CompleteAsync` 应调用来通知服务。 当服务返回响应消息时，调用完成。

```csharp
var client = new Counter.CounterClient(channel);
using (var call = client.AccumulateCount())
{
    for (var i = 0; i < 3; i++)
    {
        await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
    }
    await call.RequestStream.CompleteAsync();

    var response = await call;
    Console.WriteLine($"Count: {response.Count}");
    // Count: 3
}
```

### <a name="bi-directional-streaming-call"></a>双向流式处理调用

双向流式处理调用在客户端发送消息的*情况下*启动。 客户端可以选择通过 `RequestStream.WriteAsync`发送消息。 通过 `ResponseStream.MoveNext()` 或 `ResponseStream.ReadAllAsync()`可以访问从服务流式处理的消息。 当 `ResponseStream` 没有更多消息时，双向流式处理调用完成。

```csharp
using (var call = client.Echo())
{
    Console.WriteLine("Starting background task to receive messages");
    var readTask = Task.Run(async () =>
    {
        await foreach (var response in call.ResponseStream.ReadAllAsync())
        {
            Console.WriteLine(response.Message);
            // Echo messages sent to the service
        }
    });

    Console.WriteLine("Starting to send messages");
    Console.WriteLine("Type a message to echo then press enter.");
    while (true)
    {
        var result = Console.ReadLine();
        if (string.IsNullOrEmpty(result))
        {
            break;
        }

        await call.RequestStream.WriteAsync(new EchoMessage { Message = result });
    }

    Console.WriteLine("Disconnecting");
    await call.RequestStream.CompleteAsync();
    await readTask;
}
```

在双向流式处理调用期间，客户端和服务可以在任何时间发送消息。 与双向调用交互的最佳客户端逻辑因服务逻辑而异。

## <a name="additional-resources"></a>其他资源

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
