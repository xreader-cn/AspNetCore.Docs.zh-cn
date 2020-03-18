---
title: 使用 .NET 客户端调用 gRPC 服务
author: jamesnk
description: 了解如何使用 .NET gRPC 客户端调用 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/21/2019
uid: grpc/client
ms.openlocfilehash: 6a6a649f7194354b16f3d67160be02428cc01170
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78650802"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="a73fa-103">使用 .NET 客户端调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="a73fa-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="a73fa-104">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包提供了 .NET gRPC 客户端库。</span><span class="sxs-lookup"><span data-stu-id="a73fa-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="a73fa-105">本文档介绍如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="a73fa-105">This document explains how to:</span></span>

* <span data-ttu-id="a73fa-106">配置 gRPC 客户端以调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="a73fa-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="a73fa-107">对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="a73fa-108">配置 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="a73fa-108">Configure gRPC client</span></span>

<span data-ttu-id="a73fa-109">gRPC 客户端是从 [\*.proto  文件生成的](xref:grpc/basics#generated-c-assets)具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="a73fa-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="a73fa-110">具体 gRPC 客户端具有转换为 \*.proto  文件中 gRPC 服务的方法。</span><span class="sxs-lookup"><span data-stu-id="a73fa-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="a73fa-111">gRPC 客户端是通过通道创建的。</span><span class="sxs-lookup"><span data-stu-id="a73fa-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="a73fa-112">首先使用 `GrpcChannel.ForAddress` 创建一个通道，然后使用该通道创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="a73fa-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="a73fa-113">通道表示与 gRPC 服务的长期连接。</span><span class="sxs-lookup"><span data-stu-id="a73fa-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="a73fa-114">创建通道后，进行配置，使其具有与调用服务相关的选项。</span><span class="sxs-lookup"><span data-stu-id="a73fa-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="a73fa-115">例如，可在 `GrpcChannelOptions` 上指定用于调用的 `HttpClient`、发收和接收消息的最大大小以及记录日志，并将其与 `GrpcChannel.ForAddress` 一起使用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="a73fa-116">有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="a73fa-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

<span data-ttu-id="a73fa-117">通道及客户端性能和使用情况：</span><span class="sxs-lookup"><span data-stu-id="a73fa-117">Channel and client performance and usage:</span></span>

* <span data-ttu-id="a73fa-118">创建通道成本高昂。</span><span class="sxs-lookup"><span data-stu-id="a73fa-118">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="a73fa-119">重用 gRPC 调用的通道可提高性能。</span><span class="sxs-lookup"><span data-stu-id="a73fa-119">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="a73fa-120">gRPC 客户端是使用通道创建的。</span><span class="sxs-lookup"><span data-stu-id="a73fa-120">gRPC clients are created with channels.</span></span> <span data-ttu-id="a73fa-121">gRPC 客户端是轻型对象，无需缓存或重用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-121">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="a73fa-122">可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。</span><span class="sxs-lookup"><span data-stu-id="a73fa-122">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="a73fa-123">通道和从该通道创建的客户端可由多个线程安全使用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-123">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="a73fa-124">从通道创建的客户端可同时进行多个调用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-124">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="a73fa-125">`GrpcChannel.ForAddress` 不是创建 gRPC 客户端的唯一选项。</span><span class="sxs-lookup"><span data-stu-id="a73fa-125">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="a73fa-126">如果要从 ASP.NET Core 应用调用 gRPC 服务，请考虑 [gRPC 客户端工厂集成](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="a73fa-126">If you're calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="a73fa-127">gRPC 与 `HttpClientFactory` 集成是创建 gRPC 客户端的集中式操作备选方案。</span><span class="sxs-lookup"><span data-stu-id="a73fa-127">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="a73fa-128">要[使用 .NET 客户端调用不安全的 gRPC 服务](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)，需要其他配置。</span><span class="sxs-lookup"><span data-stu-id="a73fa-128">Additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

> [!NOTE]
> <span data-ttu-id="a73fa-129">Xamarin 当前不支持通过 HTTP/2 使用 `Grpc.Net.Client` 调用 gRPC。</span><span class="sxs-lookup"><span data-stu-id="a73fa-129">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="a73fa-130">我们正在改进 Xamarin 未来版本中的 HTTP/2 支持。</span><span class="sxs-lookup"><span data-stu-id="a73fa-130">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="a73fa-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 和 [gRPC-Web](xref:grpc/browser) 是立即生效的可行备选方案。</span><span class="sxs-lookup"><span data-stu-id="a73fa-131">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="a73fa-132">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="a73fa-132">Make gRPC calls</span></span>

<span data-ttu-id="a73fa-133">在客户端上调用方法会启动 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-133">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="a73fa-134">gRPC 客户端将处理消息序列化，并为 gRPC 调用寻址到正确服务。</span><span class="sxs-lookup"><span data-stu-id="a73fa-134">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="a73fa-135">gRPC 具有不同类型的方法。</span><span class="sxs-lookup"><span data-stu-id="a73fa-135">gRPC has different types of methods.</span></span> <span data-ttu-id="a73fa-136">使用客户端进行 gRPC 调用的方式取决于所调用的方法类型。</span><span class="sxs-lookup"><span data-stu-id="a73fa-136">How you use the client to make a gRPC call depends on the type of method you are calling.</span></span> <span data-ttu-id="a73fa-137">gRPC 方法类型如下：</span><span class="sxs-lookup"><span data-stu-id="a73fa-137">The gRPC method types are:</span></span>

* <span data-ttu-id="a73fa-138">一元</span><span class="sxs-lookup"><span data-stu-id="a73fa-138">Unary</span></span>
* <span data-ttu-id="a73fa-139">服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="a73fa-139">Server streaming</span></span>
* <span data-ttu-id="a73fa-140">客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="a73fa-140">Client streaming</span></span>
* <span data-ttu-id="a73fa-141">双向流式处理</span><span class="sxs-lookup"><span data-stu-id="a73fa-141">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="a73fa-142">一元调用</span><span class="sxs-lookup"><span data-stu-id="a73fa-142">Unary call</span></span>

<span data-ttu-id="a73fa-143">一元调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="a73fa-143">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="a73fa-144">服务结束后，返回响应消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-144">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="a73fa-145">\*.proto 文件中的每个一元服务方法将在用于调用方法的具体 gRPC 客户端类型上产生两个 .NET 方法：异步方法和阻塞方法  。</span><span class="sxs-lookup"><span data-stu-id="a73fa-145">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="a73fa-146">例如，`GreeterClient` 具有两种调用 `SayHello` 的方法：</span><span class="sxs-lookup"><span data-stu-id="a73fa-146">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="a73fa-147">`GreeterClient.SayHelloAsync` - 以异步方式调用 `Greeter.SayHello` 服务。</span><span class="sxs-lookup"><span data-stu-id="a73fa-147">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="a73fa-148">敬请期待。</span><span class="sxs-lookup"><span data-stu-id="a73fa-148">Can be awaited.</span></span>
* <span data-ttu-id="a73fa-149">`GreeterClient.SayHello` - 调用 `Greeter.SayHello` 服务并阻塞，直至结束。</span><span class="sxs-lookup"><span data-stu-id="a73fa-149">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="a73fa-150">不要在异步代码中使用。</span><span class="sxs-lookup"><span data-stu-id="a73fa-150">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="a73fa-151">服务器流式处理调用</span><span class="sxs-lookup"><span data-stu-id="a73fa-151">Server streaming call</span></span>

<span data-ttu-id="a73fa-152">服务器流式处理调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="a73fa-152">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="a73fa-153">`ResponseStream.MoveNext()` 读取从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-153">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="a73fa-154">`ResponseStream.MoveNext()` 返回 `false` 时，服务器流式处理调用已完成。</span><span class="sxs-lookup"><span data-stu-id="a73fa-154">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

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

<span data-ttu-id="a73fa-155">如果使用的是 C# 8 或更高版本，则可使用 `await foreach` 语法读取消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-155">If you are using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="a73fa-156">`IAsyncStreamReader<T>.ReadAllAsync()` 扩展方法读取响应数据流中的所有消息：</span><span class="sxs-lookup"><span data-stu-id="a73fa-156">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

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

### <a name="client-streaming-call"></a><span data-ttu-id="a73fa-157">客户端流式处理调用</span><span class="sxs-lookup"><span data-stu-id="a73fa-157">Client streaming call</span></span>

<span data-ttu-id="a73fa-158">客户端无需发送消息即可开始客户端流式处理调用  。</span><span class="sxs-lookup"><span data-stu-id="a73fa-158">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a73fa-159">客户端可选择使用 `RequestStream.WriteAsync` 发送消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-159">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a73fa-160">客户端发送完消息后，应调用 `RequestStream.CompleteAsync` 来通知服务。</span><span class="sxs-lookup"><span data-stu-id="a73fa-160">When the client has finished sending messages `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="a73fa-161">服务返回响应消息时，调用完成。</span><span class="sxs-lookup"><span data-stu-id="a73fa-161">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="a73fa-162">双向流式处理调用</span><span class="sxs-lookup"><span data-stu-id="a73fa-162">Bi-directional streaming call</span></span>

<span data-ttu-id="a73fa-163">客户端无需发送消息即可开始双向流式处理调用  。</span><span class="sxs-lookup"><span data-stu-id="a73fa-163">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="a73fa-164">客户端可选择使用 `RequestStream.WriteAsync` 发送消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-164">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="a73fa-165">使用 `ResponseStream.MoveNext()` 或 `ResponseStream.ReadAllAsync()` 可访问从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-165">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="a73fa-166">`ResponseStream` 没有更多消息时，双向流式处理调用完成。</span><span class="sxs-lookup"><span data-stu-id="a73fa-166">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="a73fa-167">双向流式处理调用期间，客户端和服务可在任何时间互相发送消息。</span><span class="sxs-lookup"><span data-stu-id="a73fa-167">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="a73fa-168">与双向调用交互的最佳客户端逻辑因服务逻辑而异。</span><span class="sxs-lookup"><span data-stu-id="a73fa-168">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a73fa-169">其他资源</span><span class="sxs-lookup"><span data-stu-id="a73fa-169">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
