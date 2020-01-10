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
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="b9b43-103">通过 .NET 客户端调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="b9b43-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="b9b43-104">.NET gRPC 客户端库在[gRPC .net. client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包中提供。</span><span class="sxs-lookup"><span data-stu-id="b9b43-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="b9b43-105">本文档介绍如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="b9b43-105">This document explains how to:</span></span>

* <span data-ttu-id="b9b43-106">配置 gRPC 客户端以调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="b9b43-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="b9b43-107">对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="b9b43-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="b9b43-108">配置 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="b9b43-108">Configure gRPC client</span></span>

<span data-ttu-id="b9b43-109">gRPC 客户端是从 [\*.proto 文件生成的](xref:grpc/basics#generated-c-assets)具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="b9b43-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="b9b43-110">具体 gRPC 客户端具有转换为 \*.proto 文件中 gRPC 服务的方法。</span><span class="sxs-lookup"><span data-stu-id="b9b43-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="b9b43-111">从通道创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="b9b43-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="b9b43-112">开始使用 `GrpcChannel.ForAddress` 创建通道，然后使用通道创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="b9b43-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="b9b43-113">通道表示 gRPC 服务的长期连接。</span><span class="sxs-lookup"><span data-stu-id="b9b43-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="b9b43-114">创建通道后，会将其配置为与调用服务相关的选项。</span><span class="sxs-lookup"><span data-stu-id="b9b43-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="b9b43-115">例如，可在 `GrpcChannelOptions` 上指定 `HttpClient`，并在 `GrpcChannel.ForAddress`上指定日志记录的最大发送和接收消息大小和日志记录。</span><span class="sxs-lookup"><span data-stu-id="b9b43-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="b9b43-116">有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="b9b43-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="b9b43-117">通道和客户端性能和使用情况：</span><span class="sxs-lookup"><span data-stu-id="b9b43-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="b9b43-118">创建通道是一种开销高昂的操作。</span><span class="sxs-lookup"><span data-stu-id="b9b43-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="b9b43-119">为 gRPC 调用重用通道可带来性能优势。</span><span class="sxs-lookup"><span data-stu-id="b9b43-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="b9b43-120">将通过通道创建 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="b9b43-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="b9b43-121">gRPC 客户端是轻型对象，无需缓存或重复使用。</span><span class="sxs-lookup"><span data-stu-id="b9b43-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="b9b43-122">可以从通道（包括不同类型的客户端）创建多个 gRPC 客户端。</span><span class="sxs-lookup"><span data-stu-id="b9b43-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="b9b43-123">通道和从通道创建的客户端可以安全地由多个线程使用。</span><span class="sxs-lookup"><span data-stu-id="b9b43-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="b9b43-124">从通道创建的客户端可以同时进行多次调用。</span><span class="sxs-lookup"><span data-stu-id="b9b43-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="b9b43-125">`GrpcChannel.ForAddress` 不是创建 gRPC 客户端的唯一选项。</span><span class="sxs-lookup"><span data-stu-id="b9b43-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="b9b43-126">如果要从 ASP.NET Core 的应用程序中调用 gRPC 服务，请考虑[gRPC 客户端工厂集成](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="b9b43-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="b9b43-127">gRPC 与 `HttpClientFactory` 的集成提供了创建 gRPC 客户端的集中式替代方法。</span><span class="sxs-lookup"><span data-stu-id="b9b43-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="b9b43-128">需要额外的配置才能[通过 .net 客户端调用不安全的 gRPC 服务](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="b9b43-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="b9b43-129">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="b9b43-129">Make gRPC calls</span></span>

<span data-ttu-id="b9b43-130">GRPC 调用通过在客户端上调用方法来启动。</span><span class="sxs-lookup"><span data-stu-id="b9b43-130">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="b9b43-131">GRPC 客户端将处理消息序列化，并将 gRPC 调用寻址到正确的服务。</span><span class="sxs-lookup"><span data-stu-id="b9b43-131">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="b9b43-132">gRPC 具有不同类型的方法。</span><span class="sxs-lookup"><span data-stu-id="b9b43-132">gRPC has different types of methods.</span></span> <span data-ttu-id="b9b43-133">使用客户端进行 gRPC 调用的方式取决于所调用的方法的类型。</span><span class="sxs-lookup"><span data-stu-id="b9b43-133">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="b9b43-134">GRPC 方法类型为：</span><span class="sxs-lookup"><span data-stu-id="b9b43-134">The gRPC method types are:</span></span>

* <span data-ttu-id="b9b43-135">一元</span><span class="sxs-lookup"><span data-stu-id="b9b43-135">Unary</span></span>
* <span data-ttu-id="b9b43-136">服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="b9b43-136">Server streaming</span></span>
* <span data-ttu-id="b9b43-137">客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="b9b43-137">Client streaming</span></span>
* <span data-ttu-id="b9b43-138">双向流式处理</span><span class="sxs-lookup"><span data-stu-id="b9b43-138">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="b9b43-139">一元调用</span><span class="sxs-lookup"><span data-stu-id="b9b43-139">Unary call</span></span>

<span data-ttu-id="b9b43-140">一元调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="b9b43-140">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="b9b43-141">服务完成后，将返回响应消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-141">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="b9b43-142">*\*proto*文件中的每个一元服务方法都将导致用于调用方法的具体 gRPC 客户端类型上有两个 .net 方法：异步方法和阻塞方法。</span><span class="sxs-lookup"><span data-stu-id="b9b43-142">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="b9b43-143">例如，在 `GreeterClient` 有两种方法可调用 `SayHello`：</span><span class="sxs-lookup"><span data-stu-id="b9b43-143">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="b9b43-144">`GreeterClient.SayHelloAsync`-异步调用 `Greeter.SayHello` 服务。</span><span class="sxs-lookup"><span data-stu-id="b9b43-144">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="b9b43-145">可以等待。</span><span class="sxs-lookup"><span data-stu-id="b9b43-145">Can be awaited.</span></span>
* <span data-ttu-id="b9b43-146">`GreeterClient.SayHello`-调用 `Greeter.SayHello` 服务，直到完成。</span><span class="sxs-lookup"><span data-stu-id="b9b43-146">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="b9b43-147">不要在异步代码中使用。</span><span class="sxs-lookup"><span data-stu-id="b9b43-147">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="b9b43-148">服务器流式处理调用</span><span class="sxs-lookup"><span data-stu-id="b9b43-148">Server streaming call</span></span>

<span data-ttu-id="b9b43-149">服务器流式处理调用会从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="b9b43-149">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="b9b43-150">`ResponseStream.MoveNext()` 读取从服务传输的消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-150">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="b9b43-151">`ResponseStream.MoveNext()` 返回 `false`时，服务器流调用已完成。</span><span class="sxs-lookup"><span data-stu-id="b9b43-151">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="b9b43-152">如果使用C#的是8或更高版本，则可以使用 `await foreach` 语法来读取消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-152">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="b9b43-153">`IAsyncStreamReader<T>.ReadAllAsync()` 扩展方法读取响应流中的所有消息：</span><span class="sxs-lookup"><span data-stu-id="b9b43-153">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="b9b43-154">客户端流式处理调用</span><span class="sxs-lookup"><span data-stu-id="b9b43-154">Client streaming call</span></span>

<span data-ttu-id="b9b43-155">客户端流式处理调用在客户端发送消息的*情况下*启动。</span><span class="sxs-lookup"><span data-stu-id="b9b43-155">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="b9b43-156">客户端可以选择通过 `RequestStream.WriteAsync`发送消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-156">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="b9b43-157">当客户端已经完成发送消息 `RequestStream.CompleteAsync` 应调用来通知服务。</span><span class="sxs-lookup"><span data-stu-id="b9b43-157">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="b9b43-158">当服务返回响应消息时，调用完成。</span><span class="sxs-lookup"><span data-stu-id="b9b43-158">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="b9b43-159">双向流式处理调用</span><span class="sxs-lookup"><span data-stu-id="b9b43-159">Bi-directional streaming call</span></span>

<span data-ttu-id="b9b43-160">双向流式处理调用在客户端发送消息的*情况下*启动。</span><span class="sxs-lookup"><span data-stu-id="b9b43-160">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="b9b43-161">客户端可以选择通过 `RequestStream.WriteAsync`发送消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-161">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="b9b43-162">通过 `ResponseStream.MoveNext()` 或 `ResponseStream.ReadAllAsync()`可以访问从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-162">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="b9b43-163">当 `ResponseStream` 没有更多消息时，双向流式处理调用完成。</span><span class="sxs-lookup"><span data-stu-id="b9b43-163">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="b9b43-164">在双向流式处理调用期间，客户端和服务可以在任何时间发送消息。</span><span class="sxs-lookup"><span data-stu-id="b9b43-164">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="b9b43-165">与双向调用交互的最佳客户端逻辑因服务逻辑而异。</span><span class="sxs-lookup"><span data-stu-id="b9b43-165">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b9b43-166">其他资源</span><span class="sxs-lookup"><span data-stu-id="b9b43-166">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
