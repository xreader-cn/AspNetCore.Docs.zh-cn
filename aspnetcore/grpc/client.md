---
title: 使用 .NET 客户端调用 gRPC 服务
author: jamesnk
description: 了解如何使用 .NET gRPC 客户端调用 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: grpc/client
ms.openlocfilehash: 5aca81da34e5ed51b2dc4f404c1ba4d7377a422f
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88016240"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="7118a-103">使用 .NET 客户端调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="7118a-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="7118a-104">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包提供了 .NET gRPC 客户端库。</span><span class="sxs-lookup"><span data-stu-id="7118a-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="7118a-105">本文档介绍如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7118a-105">This document explains how to:</span></span>

* <span data-ttu-id="7118a-106">配置 gRPC 客户端以调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="7118a-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="7118a-107">对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="7118a-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="7118a-108">配置 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="7118a-108">Configure gRPC client</span></span>

<span data-ttu-id="7118a-109">gRPC 客户端是从 [\*.proto  文件生成的](xref:grpc/basics#generated-c-assets)具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="7118a-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="7118a-110">具体 gRPC 客户端具有转换为 \*.proto  文件中 gRPC 服务的方法。</span><span class="sxs-lookup"><span data-stu-id="7118a-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span>

<span data-ttu-id="7118a-111">gRPC 客户端是通过通道创建的。</span><span class="sxs-lookup"><span data-stu-id="7118a-111">A gRPC client is created from a channel.</span></span> <span data-ttu-id="7118a-112">首先使用 `GrpcChannel.ForAddress` 创建一个通道，然后使用该通道创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="7118a-112">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="7118a-113">通道表示与 gRPC 服务的长期连接。</span><span class="sxs-lookup"><span data-stu-id="7118a-113">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="7118a-114">创建通道后，进行配置，使其具有与调用服务相关的选项。</span><span class="sxs-lookup"><span data-stu-id="7118a-114">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="7118a-115">例如，可在 `GrpcChannelOptions` 上指定用于调用的 `HttpClient`、发收和接收消息的最大大小以及记录日志，并将其与 `GrpcChannel.ForAddress` 一起使用。</span><span class="sxs-lookup"><span data-stu-id="7118a-115">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="7118a-116">有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="7118a-116">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="7118a-117">配置 TLS</span><span class="sxs-lookup"><span data-stu-id="7118a-117">Configure TLS</span></span>

<span data-ttu-id="7118a-118">gRPC 客户端必须使用与被调用服务相同的连接级别安全性。</span><span class="sxs-lookup"><span data-stu-id="7118a-118">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="7118a-119">gRPC 客户端传输层安全性 (TLS) 是在创建 gRPC 通道时配置的。</span><span class="sxs-lookup"><span data-stu-id="7118a-119">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="7118a-120">如果在调用服务时通道和服务的连接级别安全性不一致，gRPC 客户端就会抛出错误。</span><span class="sxs-lookup"><span data-stu-id="7118a-120">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="7118a-121">若要将 gRPC 通道配置为使用 TLS，请确保服务器地址以 `https` 开头。</span><span class="sxs-lookup"><span data-stu-id="7118a-121">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="7118a-122">例如，`GrpcChannel.ForAddress("https://localhost:5001")` 使用 HTTPS 协议。</span><span class="sxs-lookup"><span data-stu-id="7118a-122">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="7118a-123">gRPC 通道自动协商由 TLS 保护的连接，并使用安全连接进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="7118a-123">The gRPC channel automatically negotates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="7118a-124">gRPC 支持通过 TLS 进行客户端证书身份验证。</span><span class="sxs-lookup"><span data-stu-id="7118a-124">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="7118a-125">若要了解如何为 gRPC 通道配置客户端证书，请参阅 <xref:grpc/authn-and-authz#client-certificate-authentication>。</span><span class="sxs-lookup"><span data-stu-id="7118a-125">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="7118a-126">若要调用不安全的 gRPC 服务，请确保服务器地址以 `http` 开头。</span><span class="sxs-lookup"><span data-stu-id="7118a-126">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="7118a-127">例如，`GrpcChannel.ForAddress("http://localhost:5000")` 使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="7118a-127">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="7118a-128">在 .NET Core 3.1 或更高版本中，必须进行其他配置，才能[使用 .NET 客户端调用不安全的 gRPC 服务](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="7118a-128">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="7118a-129">客户端性能</span><span class="sxs-lookup"><span data-stu-id="7118a-129">Client performance</span></span>

<span data-ttu-id="7118a-130">通道及客户端性能和使用情况：</span><span class="sxs-lookup"><span data-stu-id="7118a-130">Channel and client performance and usage:</span></span>

* <span data-ttu-id="7118a-131">创建通道成本高昂。</span><span class="sxs-lookup"><span data-stu-id="7118a-131">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="7118a-132">重用 gRPC 调用的通道可提高性能。</span><span class="sxs-lookup"><span data-stu-id="7118a-132">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="7118a-133">gRPC 客户端是使用通道创建的。</span><span class="sxs-lookup"><span data-stu-id="7118a-133">gRPC clients are created with channels.</span></span> <span data-ttu-id="7118a-134">gRPC 客户端是轻型对象，无需缓存或重用。</span><span class="sxs-lookup"><span data-stu-id="7118a-134">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="7118a-135">可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。</span><span class="sxs-lookup"><span data-stu-id="7118a-135">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="7118a-136">通道和从该通道创建的客户端可由多个线程安全使用。</span><span class="sxs-lookup"><span data-stu-id="7118a-136">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="7118a-137">从通道创建的客户端可同时进行多个调用。</span><span class="sxs-lookup"><span data-stu-id="7118a-137">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="7118a-138">`GrpcChannel.ForAddress` 不是创建 gRPC 客户端的唯一选项。</span><span class="sxs-lookup"><span data-stu-id="7118a-138">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="7118a-139">如果要从 ASP.NET Core 应用调用 gRPC 服务，请考虑 [gRPC 客户端工厂集成](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="7118a-139">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="7118a-140">gRPC 与 `HttpClientFactory` 集成是创建 gRPC 客户端的集中式操作备选方案。</span><span class="sxs-lookup"><span data-stu-id="7118a-140">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="7118a-141">Xamarin 当前不支持通过 HTTP/2 使用 `Grpc.Net.Client` 调用 gRPC。</span><span class="sxs-lookup"><span data-stu-id="7118a-141">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="7118a-142">我们正在改进 Xamarin 未来版本中的 HTTP/2 支持。</span><span class="sxs-lookup"><span data-stu-id="7118a-142">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="7118a-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 和 [gRPC-Web](xref:grpc/browser) 是立即生效的可行备选方案。</span><span class="sxs-lookup"><span data-stu-id="7118a-143">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="7118a-144">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="7118a-144">Make gRPC calls</span></span>

<span data-ttu-id="7118a-145">在客户端上调用方法会启动 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="7118a-145">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="7118a-146">gRPC 客户端将处理消息序列化，并为 gRPC 调用寻址到正确服务。</span><span class="sxs-lookup"><span data-stu-id="7118a-146">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="7118a-147">gRPC 具有不同类型的方法。</span><span class="sxs-lookup"><span data-stu-id="7118a-147">gRPC has different types of methods.</span></span> <span data-ttu-id="7118a-148">如何使用客户端来进行 gRPC 调用取决于所调用的方法类型。</span><span class="sxs-lookup"><span data-stu-id="7118a-148">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="7118a-149">gRPC 方法类型如下：</span><span class="sxs-lookup"><span data-stu-id="7118a-149">The gRPC method types are:</span></span>

* <span data-ttu-id="7118a-150">一元</span><span class="sxs-lookup"><span data-stu-id="7118a-150">Unary</span></span>
* <span data-ttu-id="7118a-151">服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="7118a-151">Server streaming</span></span>
* <span data-ttu-id="7118a-152">客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="7118a-152">Client streaming</span></span>
* <span data-ttu-id="7118a-153">双向流式处理</span><span class="sxs-lookup"><span data-stu-id="7118a-153">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="7118a-154">一元调用</span><span class="sxs-lookup"><span data-stu-id="7118a-154">Unary call</span></span>

<span data-ttu-id="7118a-155">一元调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="7118a-155">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="7118a-156">服务结束后，返回响应消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-156">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="7118a-157">\*.proto 文件中的每个一元服务方法将在用于调用方法的具体 gRPC 客户端类型上产生两个 .NET 方法：异步方法和阻塞方法  。</span><span class="sxs-lookup"><span data-stu-id="7118a-157">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="7118a-158">例如，`GreeterClient` 具有两种调用 `SayHello` 的方法：</span><span class="sxs-lookup"><span data-stu-id="7118a-158">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="7118a-159">`GreeterClient.SayHelloAsync` - 以异步方式调用 `Greeter.SayHello` 服务。</span><span class="sxs-lookup"><span data-stu-id="7118a-159">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="7118a-160">敬请期待。</span><span class="sxs-lookup"><span data-stu-id="7118a-160">Can be awaited.</span></span>
* <span data-ttu-id="7118a-161">`GreeterClient.SayHello` - 调用 `Greeter.SayHello` 服务并阻塞，直至结束。</span><span class="sxs-lookup"><span data-stu-id="7118a-161">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="7118a-162">不要在异步代码中使用。</span><span class="sxs-lookup"><span data-stu-id="7118a-162">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="7118a-163">服务器流式处理调用</span><span class="sxs-lookup"><span data-stu-id="7118a-163">Server streaming call</span></span>

<span data-ttu-id="7118a-164">服务器流式处理调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="7118a-164">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="7118a-165">`ResponseStream.MoveNext()` 读取从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-165">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="7118a-166">`ResponseStream.MoveNext()` 返回 `false` 时，服务器流式处理调用已完成。</span><span class="sxs-lookup"><span data-stu-id="7118a-166">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="7118a-167">如果使用 C# 8 或更高版本，则可使用 `await foreach` 语法来读取消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-167">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="7118a-168">`IAsyncStreamReader<T>.ReadAllAsync()` 扩展方法读取响应数据流中的所有消息：</span><span class="sxs-lookup"><span data-stu-id="7118a-168">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="7118a-169">客户端流式处理调用</span><span class="sxs-lookup"><span data-stu-id="7118a-169">Client streaming call</span></span>

<span data-ttu-id="7118a-170">客户端无需发送消息即可开始客户端流式处理调用  。</span><span class="sxs-lookup"><span data-stu-id="7118a-170">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="7118a-171">客户端可选择使用 `RequestStream.WriteAsync` 发送消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-171">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="7118a-172">客户端发送完消息后，应调用 `RequestStream.CompleteAsync` 来通知服务。</span><span class="sxs-lookup"><span data-stu-id="7118a-172">When the client has finished sending messages, `RequestStream.CompleteAsync` should be called to notify the service.</span></span> <span data-ttu-id="7118a-173">服务返回响应消息时，调用完成。</span><span class="sxs-lookup"><span data-stu-id="7118a-173">The call is finished when the service returns a response message.</span></span>

```csharp
var client = new Counter.CounterClient(channel);
using var call = client.AccumulateCount();

for (var i = 0; i < 3; i++)
{
    await call.RequestStream.WriteAsync(new CounterRequest { Count = 1 });
}
await call.RequestStream.CompleteAsync();

var response = await call;
Console.WriteLine($"Count: {response.Count}");
// Count: 3
```

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="7118a-174">双向流式处理调用</span><span class="sxs-lookup"><span data-stu-id="7118a-174">Bi-directional streaming call</span></span>

<span data-ttu-id="7118a-175">客户端无需发送消息即可开始双向流式处理调用  。</span><span class="sxs-lookup"><span data-stu-id="7118a-175">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="7118a-176">客户端可选择使用 `RequestStream.WriteAsync` 发送消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-176">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="7118a-177">使用 `ResponseStream.MoveNext()` 或 `ResponseStream.ReadAllAsync()` 可访问从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-177">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="7118a-178">`ResponseStream` 没有更多消息时，双向流式处理调用完成。</span><span class="sxs-lookup"><span data-stu-id="7118a-178">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

```csharp
var client = new Echo.EchoClient(channel);
using var call = client.Echo();

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
```

<span data-ttu-id="7118a-179">双向流式处理调用期间，客户端和服务可在任何时间互相发送消息。</span><span class="sxs-lookup"><span data-stu-id="7118a-179">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="7118a-180">与双向调用交互的最佳客户端逻辑因服务逻辑而异。</span><span class="sxs-lookup"><span data-stu-id="7118a-180">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="7118a-181">访问 gRPC 尾部</span><span class="sxs-lookup"><span data-stu-id="7118a-181">Access gRPC trailers</span></span>

<span data-ttu-id="7118a-182">gRPC 调用可能会返回 gRPC 尾部。</span><span class="sxs-lookup"><span data-stu-id="7118a-182">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="7118a-183">gRPC 尾部用于提供有关调用的名称/值元数据。</span><span class="sxs-lookup"><span data-stu-id="7118a-183">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="7118a-184">尾部提供与 HTTP 头相似的功能，但在调用结尾获得。</span><span class="sxs-lookup"><span data-stu-id="7118a-184">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="7118a-185">gRPC 尾部可通过 `GetTrailers()` 进行访问，它会返回元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="7118a-185">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="7118a-186">尾部是在响应完成后返回的，因此你必须等待收到所有响应消息，然后才能访问尾部。</span><span class="sxs-lookup"><span data-stu-id="7118a-186">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="7118a-187">一元和客户端流式调用必须等待出现 `ResponseAsync` 后才能调用 `GetTrailers()`：</span><span class="sxs-lookup"><span data-stu-id="7118a-187">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="7118a-188">服务器和双向流式调用必须等到出现响应流，然后才能调用 `GetTrailers()`：</span><span class="sxs-lookup"><span data-stu-id="7118a-188">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="7118a-189">gRPC 尾部也可通过 `RpcException` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="7118a-189">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="7118a-190">服务可能会同时返回尾部和“异常”gRPC 状态。</span><span class="sxs-lookup"><span data-stu-id="7118a-190">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="7118a-191">在这种情况下，尾部是从 gRPC 客户端引起的异常中检索得到的：</span><span class="sxs-lookup"><span data-stu-id="7118a-191">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
string myValue = null;

try
{
    using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
    var response = await call.ResponseAsync;

    Console.WriteLine("Greeting: " + response.Message);
    // Greeting: Hello World

    var trailers = call.GetTrailers();
    myValue = trailers.GetValue("my-trailer-name");
}
catch (RpcException ex)
{
    var trailers = ex.Trailers;
    myValue = trailers.GetValue("my-trailer-name");
}
```

## <a name="additional-resources"></a><span data-ttu-id="7118a-192">其他资源</span><span class="sxs-lookup"><span data-stu-id="7118a-192">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/basics>
