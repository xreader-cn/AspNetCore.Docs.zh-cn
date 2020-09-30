---
title: 使用 .NET 客户端调用 gRPC 服务
author: jamesnk
description: 了解如何使用 .NET gRPC 客户端调用 gRPC 服务。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 07/27/2020
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
uid: grpc/client
ms.openlocfilehash: 6515e87845cc5aa101532c18711d175a73581bee
ms.sourcegitcommit: 24106b7ffffc9fff410a679863e28aeb2bbe5b7e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/17/2020
ms.locfileid: "90722704"
---
# <a name="call-grpc-services-with-the-net-client"></a><span data-ttu-id="d4032-103">使用 .NET 客户端调用 gRPC 服务</span><span class="sxs-lookup"><span data-stu-id="d4032-103">Call gRPC services with the .NET client</span></span>

<span data-ttu-id="d4032-104">[Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet 包提供了 .NET gRPC 客户端库。</span><span class="sxs-lookup"><span data-stu-id="d4032-104">A .NET gRPC client library is available in the [Grpc.Net.Client](https://www.nuget.org/packages/Grpc.Net.Client) NuGet package.</span></span> <span data-ttu-id="d4032-105">本文档介绍如何执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d4032-105">This document explains how to:</span></span>

* <span data-ttu-id="d4032-106">配置 gRPC 客户端以调用 gRPC 服务。</span><span class="sxs-lookup"><span data-stu-id="d4032-106">Configure a gRPC client to call gRPC services.</span></span>
* <span data-ttu-id="d4032-107">对一元、服务器流式处理、客户端流式处理和双向流式处理方法进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-107">Make gRPC calls to unary, server streaming, client streaming, and bi-directional streaming methods.</span></span>

## <a name="configure-grpc-client"></a><span data-ttu-id="d4032-108">配置 gRPC 客户端</span><span class="sxs-lookup"><span data-stu-id="d4032-108">Configure gRPC client</span></span>

<span data-ttu-id="d4032-109">gRPC 客户端是从 [\*.proto  文件生成的](xref:grpc/basics#generated-c-assets)具体客户端类型。</span><span class="sxs-lookup"><span data-stu-id="d4032-109">gRPC clients are concrete client types that are [generated from *\*.proto* files](xref:grpc/basics#generated-c-assets).</span></span> <span data-ttu-id="d4032-110">具体 gRPC 客户端具有转换为 \*.proto  文件中 gRPC 服务的方法。</span><span class="sxs-lookup"><span data-stu-id="d4032-110">The concrete gRPC client has methods that translate to the gRPC service in the *\*.proto* file.</span></span> <span data-ttu-id="d4032-111">例如，名为 `Greeter` 的服务生成 `GreeterClient` 类型（包含调用服务的方法）。</span><span class="sxs-lookup"><span data-stu-id="d4032-111">For example, a service called `Greeter` generates a `GreeterClient` type with methods to call the service.</span></span>

<span data-ttu-id="d4032-112">gRPC 客户端是通过通道创建的。</span><span class="sxs-lookup"><span data-stu-id="d4032-112">A gRPC client is created from a channel.</span></span> <span data-ttu-id="d4032-113">首先使用 `GrpcChannel.ForAddress` 创建一个通道，然后使用该通道创建 gRPC 客户端：</span><span class="sxs-lookup"><span data-stu-id="d4032-113">Start by using `GrpcChannel.ForAddress` to create a channel, and then use the channel to create a gRPC client:</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");
var client = new Greet.GreeterClient(channel);
```

<span data-ttu-id="d4032-114">通道表示与 gRPC 服务的长期连接。</span><span class="sxs-lookup"><span data-stu-id="d4032-114">A channel represents a long-lived connection to a gRPC service.</span></span> <span data-ttu-id="d4032-115">创建通道后，进行配置，使其具有与调用服务相关的选项。</span><span class="sxs-lookup"><span data-stu-id="d4032-115">When a channel is created, it is configured with options related to calling a service.</span></span> <span data-ttu-id="d4032-116">例如，可在 `GrpcChannelOptions` 上指定用于调用的 `HttpClient`、发收和接收消息的最大大小以及记录日志，并将其与 `GrpcChannel.ForAddress` 一起使用。</span><span class="sxs-lookup"><span data-stu-id="d4032-116">For example, the `HttpClient` used to make calls, the maximum send and receive message size, and logging can be specified on `GrpcChannelOptions` and used with `GrpcChannel.ForAddress`.</span></span> <span data-ttu-id="d4032-117">有关选项的完整列表，请参阅[客户端配置选项](xref:grpc/configuration#configure-client-options)。</span><span class="sxs-lookup"><span data-stu-id="d4032-117">For a complete list of options, see [client configuration options](xref:grpc/configuration#configure-client-options).</span></span>

```csharp
var channel = GrpcChannel.ForAddress("https://localhost:5001");

var greeterClient = new Greet.GreeterClient(channel);
var counterClient = new Count.CounterClient(channel);

// Use clients to call gRPC services
```

### <a name="configure-tls"></a><span data-ttu-id="d4032-118">配置 TLS</span><span class="sxs-lookup"><span data-stu-id="d4032-118">Configure TLS</span></span>

<span data-ttu-id="d4032-119">gRPC 客户端必须使用与被调用服务相同的连接级别安全性。</span><span class="sxs-lookup"><span data-stu-id="d4032-119">A gRPC client must use the same connection-level security as the called service.</span></span> <span data-ttu-id="d4032-120">gRPC 客户端传输层安全性 (TLS) 是在创建 gRPC 通道时配置的。</span><span class="sxs-lookup"><span data-stu-id="d4032-120">gRPC client Transport Layer Security (TLS) is configured when the gRPC channel is created.</span></span> <span data-ttu-id="d4032-121">如果在调用服务时通道和服务的连接级别安全性不一致，gRPC 客户端就会抛出错误。</span><span class="sxs-lookup"><span data-stu-id="d4032-121">A gRPC client throws an error when it calls a service and the connection-level security of the channel and service don't match.</span></span>

<span data-ttu-id="d4032-122">若要将 gRPC 通道配置为使用 TLS，请确保服务器地址以 `https` 开头。</span><span class="sxs-lookup"><span data-stu-id="d4032-122">To configure a gRPC channel to use TLS, ensure the server address starts with `https`.</span></span> <span data-ttu-id="d4032-123">例如，`GrpcChannel.ForAddress("https://localhost:5001")` 使用 HTTPS 协议。</span><span class="sxs-lookup"><span data-stu-id="d4032-123">For example, `GrpcChannel.ForAddress("https://localhost:5001")` uses HTTPS protocol.</span></span> <span data-ttu-id="d4032-124">gRPC 通道自动协商由 TLS 保护的连接，并使用安全连接进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-124">The gRPC channel automatically negotiates a connection secured by TLS and uses a secure connection to make gRPC calls.</span></span>

> [!TIP]
> <span data-ttu-id="d4032-125">gRPC 支持通过 TLS 进行客户端证书身份验证。</span><span class="sxs-lookup"><span data-stu-id="d4032-125">gRPC supports client certificate authentication over TLS.</span></span> <span data-ttu-id="d4032-126">若要了解如何为 gRPC 通道配置客户端证书，请参阅 <xref:grpc/authn-and-authz#client-certificate-authentication>。</span><span class="sxs-lookup"><span data-stu-id="d4032-126">For information on configuring client certificates with a gRPC channel, see <xref:grpc/authn-and-authz#client-certificate-authentication>.</span></span>

<span data-ttu-id="d4032-127">若要调用不安全的 gRPC 服务，请确保服务器地址以 `http` 开头。</span><span class="sxs-lookup"><span data-stu-id="d4032-127">To call unsecured gRPC services, ensure the server address starts with `http`.</span></span> <span data-ttu-id="d4032-128">例如，`GrpcChannel.ForAddress("http://localhost:5000")` 使用 HTTP 协议。</span><span class="sxs-lookup"><span data-stu-id="d4032-128">For example, `GrpcChannel.ForAddress("http://localhost:5000")` uses HTTP protocol.</span></span> <span data-ttu-id="d4032-129">在 .NET Core 3.1 或更高版本中，必须进行其他配置，才能[使用 .NET 客户端调用不安全的 gRPC 服务](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client)。</span><span class="sxs-lookup"><span data-stu-id="d4032-129">In .NET Core 3.1 or later, additional configuration is required to [call insecure gRPC services with the .NET client](xref:grpc/troubleshoot#call-insecure-grpc-services-with-net-core-client).</span></span>

### <a name="client-performance"></a><span data-ttu-id="d4032-130">客户端性能</span><span class="sxs-lookup"><span data-stu-id="d4032-130">Client performance</span></span>

<span data-ttu-id="d4032-131">通道及客户端性能和使用情况：</span><span class="sxs-lookup"><span data-stu-id="d4032-131">Channel and client performance and usage:</span></span>

* <span data-ttu-id="d4032-132">创建通道成本高昂。</span><span class="sxs-lookup"><span data-stu-id="d4032-132">Creating a channel can be an expensive operation.</span></span> <span data-ttu-id="d4032-133">重用 gRPC 调用的通道可提高性能。</span><span class="sxs-lookup"><span data-stu-id="d4032-133">Reusing a channel for gRPC calls provides performance benefits.</span></span>
* <span data-ttu-id="d4032-134">gRPC 客户端是使用通道创建的。</span><span class="sxs-lookup"><span data-stu-id="d4032-134">gRPC clients are created with channels.</span></span> <span data-ttu-id="d4032-135">gRPC 客户端是轻型对象，无需缓存或重用。</span><span class="sxs-lookup"><span data-stu-id="d4032-135">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="d4032-136">可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。</span><span class="sxs-lookup"><span data-stu-id="d4032-136">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="d4032-137">通道和从该通道创建的客户端可由多个线程安全使用。</span><span class="sxs-lookup"><span data-stu-id="d4032-137">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="d4032-138">从通道创建的客户端可同时进行多个调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-138">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="d4032-139">`GrpcChannel.ForAddress` 不是创建 gRPC 客户端的唯一选项。</span><span class="sxs-lookup"><span data-stu-id="d4032-139">`GrpcChannel.ForAddress` isn't the only option for creating a gRPC client.</span></span> <span data-ttu-id="d4032-140">如果要从 ASP.NET Core 应用调用 gRPC 服务，请考虑 [gRPC 客户端工厂集成](xref:grpc/clientfactory)。</span><span class="sxs-lookup"><span data-stu-id="d4032-140">If calling gRPC services from an ASP.NET Core app, consider [gRPC client factory integration](xref:grpc/clientfactory).</span></span> <span data-ttu-id="d4032-141">gRPC 与 `HttpClientFactory` 集成是创建 gRPC 客户端的集中式操作备选方案。</span><span class="sxs-lookup"><span data-stu-id="d4032-141">gRPC integration with `HttpClientFactory` offers a centralized alternative to creating gRPC clients.</span></span>

> [!NOTE]
> <span data-ttu-id="d4032-142">Xamarin 当前不支持通过 HTTP/2 使用 `Grpc.Net.Client` 调用 gRPC。</span><span class="sxs-lookup"><span data-stu-id="d4032-142">Calling gRPC over HTTP/2 with `Grpc.Net.Client` is currently not supported on Xamarin.</span></span> <span data-ttu-id="d4032-143">我们正在改进 Xamarin 未来版本中的 HTTP/2 支持。</span><span class="sxs-lookup"><span data-stu-id="d4032-143">We are working to improve HTTP/2 support in a future Xamarin release.</span></span> <span data-ttu-id="d4032-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 和 [gRPC-Web](xref:grpc/browser) 是立即生效的可行备选方案。</span><span class="sxs-lookup"><span data-stu-id="d4032-144">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) and [gRPC-Web](xref:grpc/browser) are viable alternatives that work today.</span></span>

## <a name="make-grpc-calls"></a><span data-ttu-id="d4032-145">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="d4032-145">Make gRPC calls</span></span>

<span data-ttu-id="d4032-146">在客户端上调用方法会启动 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-146">A gRPC call is initiated by calling a method on the client.</span></span> <span data-ttu-id="d4032-147">gRPC 客户端将处理消息序列化，并为 gRPC 调用寻址到正确服务。</span><span class="sxs-lookup"><span data-stu-id="d4032-147">The gRPC client will handle message serialization and addressing the gRPC call to the correct service.</span></span>

<span data-ttu-id="d4032-148">gRPC 具有不同类型的方法。</span><span class="sxs-lookup"><span data-stu-id="d4032-148">gRPC has different types of methods.</span></span> <span data-ttu-id="d4032-149">如何使用客户端来进行 gRPC 调用取决于所调用的方法类型。</span><span class="sxs-lookup"><span data-stu-id="d4032-149">How the client is used to make a gRPC call depends on the type of method called.</span></span> <span data-ttu-id="d4032-150">gRPC 方法类型如下：</span><span class="sxs-lookup"><span data-stu-id="d4032-150">The gRPC method types are:</span></span>

* <span data-ttu-id="d4032-151">一元</span><span class="sxs-lookup"><span data-stu-id="d4032-151">Unary</span></span>
* <span data-ttu-id="d4032-152">服务器流式处理</span><span class="sxs-lookup"><span data-stu-id="d4032-152">Server streaming</span></span>
* <span data-ttu-id="d4032-153">客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="d4032-153">Client streaming</span></span>
* <span data-ttu-id="d4032-154">双向流式处理</span><span class="sxs-lookup"><span data-stu-id="d4032-154">Bi-directional streaming</span></span>

### <a name="unary-call"></a><span data-ttu-id="d4032-155">一元调用</span><span class="sxs-lookup"><span data-stu-id="d4032-155">Unary call</span></span>

<span data-ttu-id="d4032-156">一元调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="d4032-156">A unary call starts with the client sending a request message.</span></span> <span data-ttu-id="d4032-157">服务结束后，返回响应消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-157">A response message is returned when the service finishes.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
var response = await client.SayHelloAsync(new HelloRequest { Name = "World" });

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World
```

<span data-ttu-id="d4032-158">\*.proto 文件中的每个一元服务方法将在用于调用方法的具体 gRPC 客户端类型上产生两个 .NET 方法：异步方法和阻塞方法  。</span><span class="sxs-lookup"><span data-stu-id="d4032-158">Each unary service method in the *\*.proto* file will result in two .NET methods on the concrete gRPC client type for calling the method: an asynchronous method and a blocking method.</span></span> <span data-ttu-id="d4032-159">例如，`GreeterClient` 具有两种调用 `SayHello` 的方法：</span><span class="sxs-lookup"><span data-stu-id="d4032-159">For example, on `GreeterClient` there are two ways of calling `SayHello`:</span></span>

* <span data-ttu-id="d4032-160">`GreeterClient.SayHelloAsync` - 以异步方式调用 `Greeter.SayHello` 服务。</span><span class="sxs-lookup"><span data-stu-id="d4032-160">`GreeterClient.SayHelloAsync` - calls `Greeter.SayHello` service asynchronously.</span></span> <span data-ttu-id="d4032-161">敬请期待。</span><span class="sxs-lookup"><span data-stu-id="d4032-161">Can be awaited.</span></span>
* <span data-ttu-id="d4032-162">`GreeterClient.SayHello` - 调用 `Greeter.SayHello` 服务并阻塞，直至结束。</span><span class="sxs-lookup"><span data-stu-id="d4032-162">`GreeterClient.SayHello` - calls `Greeter.SayHello` service and blocks until complete.</span></span> <span data-ttu-id="d4032-163">不要在异步代码中使用。</span><span class="sxs-lookup"><span data-stu-id="d4032-163">Don't use in asynchronous code.</span></span>

### <a name="server-streaming-call"></a><span data-ttu-id="d4032-164">服务器流式处理调用</span><span class="sxs-lookup"><span data-stu-id="d4032-164">Server streaming call</span></span>

<span data-ttu-id="d4032-165">服务器流式处理调用从客户端发送请求消息开始。</span><span class="sxs-lookup"><span data-stu-id="d4032-165">A server streaming call starts with the client sending a request message.</span></span> <span data-ttu-id="d4032-166">`ResponseStream.MoveNext()` 读取从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-166">`ResponseStream.MoveNext()` reads messages streamed from the service.</span></span> <span data-ttu-id="d4032-167">`ResponseStream.MoveNext()` 返回 `false` 时，服务器流式处理调用已完成。</span><span class="sxs-lookup"><span data-stu-id="d4032-167">The server streaming call is complete when `ResponseStream.MoveNext()` returns `false`.</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

while (await call.ResponseStream.MoveNext())
{
    Console.WriteLine("Greeting: " + call.ResponseStream.Current.Message);
    // "Greeting: Hello World" is written multiple times
}
```

<span data-ttu-id="d4032-168">如果使用 C# 8 或更高版本，则可使用 `await foreach` 语法来读取消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-168">When using C# 8 or later, the `await foreach` syntax can be used to read messages.</span></span> <span data-ttu-id="d4032-169">`IAsyncStreamReader<T>.ReadAllAsync()` 扩展方法读取响应数据流中的所有消息：</span><span class="sxs-lookup"><span data-stu-id="d4032-169">The `IAsyncStreamReader<T>.ReadAllAsync()` extension method reads all messages from the response stream:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHellos(new HelloRequest { Name = "World" });

await foreach (var response in call.ResponseStream.ReadAllAsync())
{
    Console.WriteLine("Greeting: " + response.Message);
    // "Greeting: Hello World" is written multiple times
}
```

### <a name="client-streaming-call"></a><span data-ttu-id="d4032-170">客户端流式处理调用</span><span class="sxs-lookup"><span data-stu-id="d4032-170">Client streaming call</span></span>

<span data-ttu-id="d4032-171">客户端无需发送消息即可开始客户端流式处理调用  。</span><span class="sxs-lookup"><span data-stu-id="d4032-171">A client streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="d4032-172">客户端可选择使用 `RequestStream.WriteAsync` 发送消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-172">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="d4032-173">客户端发送完消息后，应调用 `RequestStream.CompleteAsync()` 来通知服务。</span><span class="sxs-lookup"><span data-stu-id="d4032-173">When the client has finished sending messages, `RequestStream.CompleteAsync()` should be called to notify the service.</span></span> <span data-ttu-id="d4032-174">服务返回响应消息时，调用完成。</span><span class="sxs-lookup"><span data-stu-id="d4032-174">The call is finished when the service returns a response message.</span></span>

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

### <a name="bi-directional-streaming-call"></a><span data-ttu-id="d4032-175">双向流式处理调用</span><span class="sxs-lookup"><span data-stu-id="d4032-175">Bi-directional streaming call</span></span>

<span data-ttu-id="d4032-176">客户端无需发送消息即可开始双向流式处理调用  。</span><span class="sxs-lookup"><span data-stu-id="d4032-176">A bi-directional streaming call starts *without* the client sending a message.</span></span> <span data-ttu-id="d4032-177">客户端可选择使用 `RequestStream.WriteAsync` 发送消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-177">The client can choose to send messages with `RequestStream.WriteAsync`.</span></span> <span data-ttu-id="d4032-178">使用 `ResponseStream.MoveNext()` 或 `ResponseStream.ReadAllAsync()` 可访问从服务流式处理的消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-178">Messages streamed from the service are accessible with `ResponseStream.MoveNext()` or `ResponseStream.ReadAllAsync()`.</span></span> <span data-ttu-id="d4032-179">`ResponseStream` 没有更多消息时，双向流式处理调用完成。</span><span class="sxs-lookup"><span data-stu-id="d4032-179">The bi-directional streaming call is complete when the `ResponseStream` has no more messages.</span></span>

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

<span data-ttu-id="d4032-180">为获得最佳性能并避免客户端和服务中出现不必要的错误，请尝试正常完成双向流式调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-180">For best performance, and to avoid unnecessary errors in the client and service, try to complete bi-directional streaming calls gracefully.</span></span> <span data-ttu-id="d4032-181">当服务器已读取请求流且客户端已读取响应流时，双向调用正常完成。</span><span class="sxs-lookup"><span data-stu-id="d4032-181">A bi-directional call completes gracefully when the server has finished reading the request stream and the client has finished reading the response stream.</span></span> <span data-ttu-id="d4032-182">前面的示例调用就是一个正常结束的双向调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-182">The preceding sample call is one example of a bi-directional call that ends gracefully.</span></span> <span data-ttu-id="d4032-183">在调用中，客户端：</span><span class="sxs-lookup"><span data-stu-id="d4032-183">In the call, the client:</span></span>

1. <span data-ttu-id="d4032-184">通过调用 `EchoClient.Echo` 启动新的双向流式调用。</span><span class="sxs-lookup"><span data-stu-id="d4032-184">Starts a new bi-directional streaming call by calling `EchoClient.Echo`.</span></span>
2. <span data-ttu-id="d4032-185">使用 `ResponseStream.ReadAllAsync()` 创建用于从服务中读取消息的后台任务。</span><span class="sxs-lookup"><span data-stu-id="d4032-185">Creates a background task to read messages from the service using `ResponseStream.ReadAllAsync()`.</span></span>
3. <span data-ttu-id="d4032-186">使用 `RequestStream.WriteAsync` 将消息发送到服务器。</span><span class="sxs-lookup"><span data-stu-id="d4032-186">Sends messages to the server with `RequestStream.WriteAsync`.</span></span>
4. <span data-ttu-id="d4032-187">使用 `RequestStream.CompleteAsync()` 通知服务器它已发送消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-187">Notifies the server it has finished sending messages with `RequestStream.CompleteAsync()`.</span></span>
5. <span data-ttu-id="d4032-188">等待直到后台任务已读取所有传入消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-188">Waits until the background task has read all incoming messages.</span></span>

<span data-ttu-id="d4032-189">双向流式处理调用期间，客户端和服务可在任何时间互相发送消息。</span><span class="sxs-lookup"><span data-stu-id="d4032-189">During a bi-directional streaming call, the client and service can send messages to each other at any time.</span></span> <span data-ttu-id="d4032-190">与双向调用交互的最佳客户端逻辑因服务逻辑而异。</span><span class="sxs-lookup"><span data-stu-id="d4032-190">The best client logic for interacting with a bi-directional call varies depending upon the service logic.</span></span>

## <a name="access-grpc-trailers"></a><span data-ttu-id="d4032-191">访问 gRPC 尾部</span><span class="sxs-lookup"><span data-stu-id="d4032-191">Access gRPC trailers</span></span>

<span data-ttu-id="d4032-192">gRPC 调用可能会返回 gRPC 尾部。</span><span class="sxs-lookup"><span data-stu-id="d4032-192">gRPC calls may return gRPC trailers.</span></span> <span data-ttu-id="d4032-193">gRPC 尾部用于提供有关调用的名称/值元数据。</span><span class="sxs-lookup"><span data-stu-id="d4032-193">gRPC trailers are used to provide name/value metadata about a call.</span></span> <span data-ttu-id="d4032-194">尾部提供与 HTTP 头相似的功能，但在调用结尾获得。</span><span class="sxs-lookup"><span data-stu-id="d4032-194">Trailers provide similar functionality to HTTP headers, but are received at the end of the call.</span></span>

<span data-ttu-id="d4032-195">gRPC 尾部可通过 `GetTrailers()` 进行访问，它会返回元数据的集合。</span><span class="sxs-lookup"><span data-stu-id="d4032-195">gRPC trailers are accessible using `GetTrailers()`, which returns a collection of metadata.</span></span> <span data-ttu-id="d4032-196">尾部是在响应完成后返回的，因此你必须等待收到所有响应消息，然后才能访问尾部。</span><span class="sxs-lookup"><span data-stu-id="d4032-196">Trailers are returned after the response is complete, therefore, you must await all response messages before accessing the trailers.</span></span>

<span data-ttu-id="d4032-197">一元和客户端流式调用必须等待出现 `ResponseAsync` 后才能调用 `GetTrailers()`：</span><span class="sxs-lookup"><span data-stu-id="d4032-197">Unary and client streaming calls must await `ResponseAsync` before calling `GetTrailers()`:</span></span>

```csharp
var client = new Greet.GreeterClient(channel);
using var call = client.SayHelloAsync(new HelloRequest { Name = "World" });
var response = await call.ResponseAsync;

Console.WriteLine("Greeting: " + response.Message);
// Greeting: Hello World

var trailers = call.GetTrailers();
var myValue = trailers.GetValue("my-trailer-name");
```

<span data-ttu-id="d4032-198">服务器和双向流式调用必须等到出现响应流，然后才能调用 `GetTrailers()`：</span><span class="sxs-lookup"><span data-stu-id="d4032-198">Server and bidirectional streaming calls must finish awaiting the response stream before calling `GetTrailers()`:</span></span>

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

<span data-ttu-id="d4032-199">gRPC 尾部也可通过 `RpcException` 进行访问。</span><span class="sxs-lookup"><span data-stu-id="d4032-199">gRPC trailers are also accessible from `RpcException`.</span></span> <span data-ttu-id="d4032-200">服务可能会同时返回尾部和“异常”gRPC 状态。</span><span class="sxs-lookup"><span data-stu-id="d4032-200">A service may return trailers together with a non-OK gRPC status.</span></span> <span data-ttu-id="d4032-201">在这种情况下，尾部是从 gRPC 客户端引起的异常中检索得到的：</span><span class="sxs-lookup"><span data-stu-id="d4032-201">In this situation the trailers are retrieved from the exception thrown by the gRPC client:</span></span>

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

## <a name="configure-deadline"></a><span data-ttu-id="d4032-202">配置截止时间</span><span class="sxs-lookup"><span data-stu-id="d4032-202">Configure deadline</span></span>

<span data-ttu-id="d4032-203">建议配置 gRPC 调用截止时间，因为它提供调用时间的上限。</span><span class="sxs-lookup"><span data-stu-id="d4032-203">Configuring a gRPC call deadline is recommended because it provides an upper limit on how long a call can run for.</span></span> <span data-ttu-id="d4032-204">它能阻止异常运行的服务持续运行并耗尽服务器资源。</span><span class="sxs-lookup"><span data-stu-id="d4032-204">It stops misbehaving services from running forever and exhausting server resources.</span></span> <span data-ttu-id="d4032-205">截止时间对于构建可靠应用非常有效。</span><span class="sxs-lookup"><span data-stu-id="d4032-205">Deadlines are a useful tool for building reliable apps.</span></span>

<span data-ttu-id="d4032-206">配置 `CallOptions.Deadline` 以设置 gRPC 调用的截止时间：</span><span class="sxs-lookup"><span data-stu-id="d4032-206">Configure `CallOptions.Deadline` to set a deadline for a gRPC call:</span></span>

[!code-csharp[](~/grpc/deadlines-cancellation/deadline-client.cs?highlight=7,12)]

<span data-ttu-id="d4032-207">有关详细信息，请参阅 <xref:grpc/deadlines-cancellation#deadlines>。</span><span class="sxs-lookup"><span data-stu-id="d4032-207">For more information, see <xref:grpc/deadlines-cancellation#deadlines>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d4032-208">其他资源</span><span class="sxs-lookup"><span data-stu-id="d4032-208">Additional resources</span></span>

* <xref:grpc/clientfactory>
* <xref:grpc/deadlines-cancellation>
* <xref:grpc/basics>
