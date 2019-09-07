---
title: 通过 .NET 客户端调用 gRPC 服务
author: jamesnk
description: 了解如何通过 .NET gRPC 客户端调用 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 27e4b3e7307ae49bacb01a46fbc1b55b6967c7a0
ms.sourcegitcommit: f65d8765e4b7c894481db9b37aa6969abc625a48
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/06/2019
ms.locfileid: "70773697"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="49ac0-103">通过 .NET 客户端调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="49ac0-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="49ac0-104">.NET gRPC 客户端库在[gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包中提供。</span><span class="sxs-lookup"><span data-stu-id="49ac0-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="49ac0-105">本文档介绍如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="49ac0-105">This document explains how to:</span></span>

* <span data-ttu-id="49ac0-106">配置 gRPC 客户端以调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="49ac0-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="49ac0-107">对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="49ac0-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="49ac0-108">配置 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="49ac0-108">Configure gRPC client</span></span>

<span data-ttu-id="49ac0-109">gRPC 客户端是[从 *\*proto*文件生成](xref:grpc/basics#generated-c-assets)的具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="49ac0-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="49ac0-110">混凝土 gRPC 客户端具有转换为 *\*proto*文件中 gRPC 服务的方法。</span><span class="sxs-lookup"><span data-stu-id="49ac0-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="49ac0-111">从通道创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="49ac0-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="49ac0-112">首先使用`GrpcChannel.ForAddress`创建通道，然后使用通道创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="49ac0-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="49ac0-113">通道表示 gRPC 服务的长期连接。</span><span class="sxs-lookup"><span data-stu-id="49ac0-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="49ac0-114">创建通道时，会使用与调用服务相关的选项来配置该通道。</span><span class="sxs-lookup"><span data-stu-id="49ac0-114">When a channel is created it is configured with options related to calling a service.</span></span> <span data-ttu-id="49ac0-115">例如，可在`HttpClient`上`GrpcChannelOptions`指定并与`GrpcChannel.ForAddress`一起使用的、最大发送和接收消息大小以及日志记录。</span><span class="sxs-lookup"><span data-stu-id="49ac0-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="49ac0-116">有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="49ac0-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

<span data-ttu-id="49ac0-117">创建通道是一种开销高昂的操作，使用 gRPC 调用的通道可带来性能优势。</span><span class="sxs-lookup"><span data-stu-id="49ac0-117">Creating a channel can be an expensive operation and reusing a channel for gRPC calls offers performance benefits.</span></span> <span data-ttu-id="49ac0-118">可以从通道（包括不同类型的客户端）创建多个具体的 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="49ac0-118">Multiple concrete gRPC clients can be created from a channel, including different types of clients.</span></span> <span data-ttu-id="49ac0-119">具体 gRPC 客户端类型为轻型对象，并可在需要时创建。</span><span class="sxs-lookup"><span data-stu-id="49ac0-119">Concrete gRPC client types are lightweight objects and can be created when needed.</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="49ac0-120">`GrpcChannel.ForAddress`不是创建 gRPC 客户端的唯一选项。</span><span class="sxs-lookup"><span data-stu-id="49ac0-120">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="49ac0-121">如果从 ASP.NET Core 应用程序调用 gRPC 服务，请考虑[gRPC 客户端工厂集成](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="49ac0-121">If you are calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="49ac0-122">与的`HttpClientFactory` gRPC 集成提供了一种用于创建 gRPC 客户端的集中替代方法。</span><span class="sxs-lookup"><span data-stu-id="49ac0-122">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="49ac0-123">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="49ac0-123">Make gRPC calls</span></span>

<span data-ttu-id="49ac0-124">GRPC 调用通过在客户端上调用方法来启动。</span><span class="sxs-lookup"><span data-stu-id="49ac0-124">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="49ac0-125">GRPC 客户端将处理消息序列化，并将 gRPC 调用寻址到正确的服务。</span><span class="sxs-lookup"><span data-stu-id="49ac0-125">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="49ac0-126">gRPC 具有不同类型的方法。</span><span class="sxs-lookup"><span data-stu-id="49ac0-126">gRPC has different types of methods.</span></span> <span data-ttu-id="49ac0-127">使用客户端进行 gRPC 调用的方式取决于所调用的方法的类型。</span><span class="sxs-lookup"><span data-stu-id="49ac0-127">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="49ac0-128">GRPC 方法类型为：</span><span class="sxs-lookup"><span data-stu-id="49ac0-128">The gRPC method types are:</span></span>

* <span data-ttu-id="49ac0-129">一元</span><span class="sxs-lookup"><span data-stu-id="49ac0-129">Unary</span></span>
* <span data-ttu-id="49ac0-130">服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="49ac0-130">Server streaming</span></span>
* <span data-ttu-id="49ac0-131">客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="49ac0-131">Client streaming</span></span>
* <span data-ttu-id="49ac0-132">双向流式处理</span><span class="sxs-lookup"><span data-stu-id="49ac0-132">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="49ac0-133">一元调用</span><span class="sxs-lookup"><span data-stu-id="49ac0-133">Unary call</span></span>

<span data-ttu-id="49ac0-134">一元调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="49ac0-134">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="49ac0-135">服务完成后，将返回响应消息。</span><span class="sxs-lookup"><span data-stu-id="49ac0-135">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="49ac0-136">*\*Proto*文件中的每个一元服务方法将导致用于调用方法的具体 gRPC 客户端类型上有两个 .net 方法：一个异步方法和一个阻止方法。</span><span class="sxs-lookup"><span data-stu-id="49ac0-136">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="49ac0-137">例如， `GreeterClient`可以通过两种方法调用`SayHello`：</span><span class="sxs-lookup"><span data-stu-id="49ac0-137">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="49ac0-138">`GreeterClient.SayHelloAsync`-以`Greeter.SayHello`异步方式调用服务。</span><span class="sxs-lookup"><span data-stu-id="49ac0-138">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="49ac0-139">可以等待。</span><span class="sxs-lookup"><span data-stu-id="49ac0-139">Can be awaited.</span></span>
* <span data-ttu-id="49ac0-140">`GreeterClient.SayHello`-调用`Greeter.SayHello`服务和阻止到完成。</span><span class="sxs-lookup"><span data-stu-id="49ac0-140">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="49ac0-141">不要在异步代码中使用。</span><span class="sxs-lookup"><span data-stu-id="49ac0-141">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="49ac0-142">服务器流式处理调用</span><span class="sxs-lookup"><span data-stu-id="49ac0-142">Server streaming call</span></span>

<span data-ttu-id="49ac0-143">服务器流式处理调用会从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="49ac0-143">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="49ac0-144">`ResponseStream.MoveNext()`读取从服务传输的消息。</span><span class="sxs-lookup"><span data-stu-id="49ac0-144">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="49ac0-145">`ResponseStream.MoveNext()` 返回`false`时，服务器流调用已完成。</span><span class="sxs-lookup"><span data-stu-id="49ac0-145">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="49ac0-146">如果使用C#的`await foreach`是8或更高版本，则可以使用语法来读取消息。</span><span class="sxs-lookup"><span data-stu-id="49ac0-146">If you are using C# 8 or later then the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="49ac0-147">`IAsyncStreamReader<T>.ReadAllAsync()`扩展方法从响应流中读取所有消息：</span><span class="sxs-lookup"><span data-stu-id="49ac0-147">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="49ac0-148">客户端流式处理调用</span><span class="sxs-lookup"><span data-stu-id="49ac0-148">Client streaming call</span></span>

<span data-ttu-id="49ac0-149">客户端流式处理调用在客户端发送消息的*情况下*启动。</span><span class="sxs-lookup"><span data-stu-id="49ac0-149">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="49ac0-150">客户端可以选择发送发送消息`RequestStream.WriteAsync`。</span><span class="sxs-lookup"><span data-stu-id="49ac0-150">The client can choose to send sends messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="49ac0-151">当客户端已经完成发送消息`RequestStream.CompleteAsync`时，应调用来通知服务。</span><span class="sxs-lookup"><span data-stu-id="49ac0-151">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="49ac0-152">当服务返回响应消息时，调用完成。</span><span class="sxs-lookup"><span data-stu-id="49ac0-152">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="49ac0-153">双向流式处理调用</span><span class="sxs-lookup"><span data-stu-id="49ac0-153">Bi-directional streaming call</span></span>

<span data-ttu-id="49ac0-154">双向流式处理调用在客户端发送消息的*情况下*启动。</span><span class="sxs-lookup"><span data-stu-id="49ac0-154">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="49ac0-155">客户端可以选择通过`RequestStream.WriteAsync`发送消息。</span><span class="sxs-lookup"><span data-stu-id="49ac0-155">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="49ac0-156">可以通过或`ResponseStream.ReadAllAsync()`访问从服务流式处理`ResponseStream.MoveNext()`的消息。</span><span class="sxs-lookup"><span data-stu-id="49ac0-156">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="49ac0-157">当没有更多消息时`ResponseStream` ，双向流式处理调用完成。</span><span class="sxs-lookup"><span data-stu-id="49ac0-157">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="49ac0-158">在双向流式处理调用期间，客户端和服务可以在任何时间发送消息。</span><span class="sxs-lookup"><span data-stu-id="49ac0-158">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="49ac0-159">与双向调用交互的最佳客户端逻辑因服务逻辑而异。</span><span class="sxs-lookup"><span data-stu-id="49ac0-159">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="49ac0-160">其他资源</span><span class="sxs-lookup"><span data-stu-id="49ac0-160">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
