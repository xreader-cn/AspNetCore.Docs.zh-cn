---
title: 通过 .NET 客户端调用 gRPC 服务
author: jamesnk
description: 了解如何通过 .NET gRPC 客户端调用 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 5518a221c4641ba0d1da051a14750e3165944455
ms.sourcegitcommit: 8b36f75b8931ae3f656e2a8e63572080adc78513
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/05/2019
ms.locfileid: "70310618"
---
# <a name="call-grpc-services-with-the-net-client"></a>通过 .NET 客户端调用 gRPC 服务

.NET gRPC 客户端库在[gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包中提供。 本文档介绍如何执行以下操作：

* 配置 gRPC 客户端以调用 gRPC 服务。
* 对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。

## <a name="configure-grpc-client"></a>配置 gRPC 客户端

gRPC 客户端是[从 *\*proto*文件生成](xref:grpc/basics#generated-c-assets)的具体客户端类型。 混凝土 gRPC 客户端具有转换为 *\*proto*文件中 gRPC 服务的方法。

从通道创建 gRPC 客户端。 首先使用`GrpcChannel.ForAddress`创建通道，然后使用通道创建 gRPC 客户端：

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

通道表示 gRPC 服务的长期连接。 创建通道时，会使用与调用服务相关的选项来配置该通道。 例如，可在`HttpClient`上`GrpcChannelOptions`指定并与`GrpcChannel.ForAddress`一起使用的、最大发送和接收消息大小以及日志记录。 有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。

创建通道是一种开销高昂的操作，使用 gRPC 调用的通道可带来性能优势。 可以从通道（包括不同类型的客户端）创建多个具体的 gRPC 客户端。 具体 gRPC 客户端类型为轻型对象，并可在需要时创建。

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

`GrpcChannel.ForAddress`不是创建 gRPC 客户端的唯一选项。 如果从 ASP.NET Core 应用程序调用 gRPC 服务，请考虑[gRPC 客户端工厂集成](xref:grpc/clientfactory)。 与的`HttpClientFactory` gRPC 集成提供了一种用于创建 gRPC 客户端的集中替代方法。

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

*\*Proto*文件中的每个一元服务方法将导致用于调用方法的具体 gRPC 客户端类型上有两个 .net 方法：一个异步方法和一个阻止方法。 例如， `GreeterClient`可以通过两种方法调用`SayHello`：

* `GreeterClient.SayHelloAsync`-以`Greeter.SayHello`异步方式调用服务。 可以等待。
* `GreeterClient.SayHello`-调用`Greeter.SayHello`服务和阻止到完成。 不要在异步代码中使用。

### <a name="server-streaming-call"></a>服务器流式处理调用

服务器流式处理调用会从客户端发送请求消息开始。 `ResponseStream.MoveNext()`读取从服务传输的消息。 `ResponseStream.MoveNext()` 返回`false`时，服务器流调用已完成。

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    while (await call.ResponseStream.MoveNext())
    {
        Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
        // Greeting: Hello World" is streamed multiple times
    }
}
```

如果使用C#的`await foreach`是8或更高版本，则可以使用语法来读取消息。 `IAsyncStreamReader<T>.ReadAllAsync()`扩展方法从响应流中读取所有消息：

```csharp
var client = new Greet.GreeterClient(channel);
using (var call = client.SayHellos(new HelloRequest { Name = "World" }))
{
    await foreach (var response in call.ResponseStream.ReadAllAsync())
    {
        Console.WriteLine("Greeting: " + response.Message);
        // "Greeting: Hello World" is streamed multiple times
    }
}
```

### <a name="client-streaming-call"></a>客户端流式处理调用

客户端流式处理调用在客户端发送消息的*情况下*启动。 客户端可以选择发送发送消息`RequestStream.WriteAsync`。 当客户端已经完成发送消息`RequestStream.CompleteAsync`时，应调用来通知服务。 当服务返回响应消息时，调用完成。

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

双向流式处理调用在客户端发送消息的*情况下*启动。 客户端可以选择通过`RequestStream.WriteAsync`发送消息。 可以通过或`ResponseStream.ReadAllAsync()`访问从服务流式处理`ResponseStream.MoveNext()`的消息。 当没有更多消息时`ResponseStream` ，双向流式处理调用完成。

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
