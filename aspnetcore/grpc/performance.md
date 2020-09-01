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
# <a name="performance-best-practices-in-grpc-for-aspnet-core"></a><span data-ttu-id="6eb1c-103">适用于 ASP.NET Core 的 gRPC 中的性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="6eb1c-103">Performance best practices in gRPC for ASP.NET Core</span></span>

<span data-ttu-id="6eb1c-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="6eb1c-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="6eb1c-105">gRPC 专用于高性能服务。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="6eb1c-106">本文档介绍如何从 gRPC 获得最佳性能。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-channel"></a><span data-ttu-id="6eb1c-107">重用通道</span><span class="sxs-lookup"><span data-stu-id="6eb1c-107">Reuse channel</span></span>

<span data-ttu-id="6eb1c-108">进行 gRPC 调用时，应重新使用 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="6eb1c-109">重用通道后通过现有的 HTTP/2 连接对调用进行多路复用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="6eb1c-110">如果为每个 gRPC 调用创建一个新通道，则完成此操作所需的时间可能会显著增加。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="6eb1c-111">每次调用都需要在客户端和服务器之间进行多个网络往返，以创建 HTTP/2 连接：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-111">Each call will require multiple network round-trips between the client and the server to create an HTTP/2 connection:</span></span>

1. <span data-ttu-id="6eb1c-112">打开套接字</span><span class="sxs-lookup"><span data-stu-id="6eb1c-112">Opening a socket</span></span>
2. <span data-ttu-id="6eb1c-113">建立 TCP 连接</span><span class="sxs-lookup"><span data-stu-id="6eb1c-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="6eb1c-114">协商 TLS</span><span class="sxs-lookup"><span data-stu-id="6eb1c-114">Negotiating TLS</span></span>
4. <span data-ttu-id="6eb1c-115">启动 HTTP/2 连接</span><span class="sxs-lookup"><span data-stu-id="6eb1c-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="6eb1c-116">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="6eb1c-116">Making the gRPC call</span></span>

<span data-ttu-id="6eb1c-117">在 gRPC 调用之间可以安全地共享和重用通道：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="6eb1c-118">gRPC 客户端是使用通道创建的。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="6eb1c-119">gRPC 客户端是轻型对象，无需缓存或重用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="6eb1c-120">可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="6eb1c-121">通道和从该通道创建的客户端可由多个线程安全使用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="6eb1c-122">从通道创建的客户端可同时进行多个调用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="6eb1c-123">连接并发</span><span class="sxs-lookup"><span data-stu-id="6eb1c-123">Connection concurrency</span></span>

<span data-ttu-id="6eb1c-124">HTTP/2 连接通常会限制一个连接上同时存在的[最大并发流（活动 HTTP 请求）数](https://http2.github.io/http2-spec/#rfc.section.5.1.2)。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-124">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://http2.github.io/http2-spec/#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="6eb1c-125">默认情况下，大多数服务器将此限制设置为 100 个并发流。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-125">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="6eb1c-126">gRPC 通道使用单个 HTTP/2 连接，并且并发调用在该连接上多路复用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-126">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="6eb1c-127">当活动调用数达到连接流限制时，其他调用会在客户端中排队。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-127">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="6eb1c-128">排队调用等待活动调用完成后再发送。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-128">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="6eb1c-129">由于此限制，具有高负载或长时间运行的流式处理 gRPC 调用的应用程序可能会因调用排队而出现性能问题。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-129">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="6eb1c-130">.NET 5 引入 `SocketsHttpHandler.EnableMultipleHttp2Connections` 属性。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-130">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="6eb1c-131">如果设置为 `true`，则当达到并发流限制时，通道会创建额外的 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-131">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="6eb1c-132">创建 `GrpcChannel` 时，会自动将其内部 `SocketsHttpHandler` 配置为创建额外的 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-132">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="6eb1c-133">如果应用配置其自己的处理程序，请考虑将 `EnableMultipleHttp2Connections` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-133">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="6eb1c-134">.NET Core 3.1 应用有几种解决方法：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-134">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="6eb1c-135">为具有高负载的应用的区域创建单独的 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-135">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="6eb1c-136">例如，`Logger` gRPC 服务可能具有高负载。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-136">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="6eb1c-137">使用单独的通道在应用中创建 `LoggerClient`。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-137">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="6eb1c-138">使用 gRPC 通道池，例如创建 gRPC 通道列表。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-138">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="6eb1c-139">每次需要 gRPC 通道时，使用 `Random` 从列表中选取一个通道。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-139">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="6eb1c-140">使用 `Random` 在多个连接上随机分配调用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-140">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6eb1c-141">提升服务器上的最大并发流限制是解决此问题的另一种方法。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-141">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="6eb1c-142">在 Kestrel 中，这是用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 配置的。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-142">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="6eb1c-143">不建议提升最大并发流限制。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-143">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="6eb1c-144">单个 HTTP/2 连接上的流过多会带来新的性能问题：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-144">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="6eb1c-145">尝试写入连接的流之间发生线程争用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-145">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="6eb1c-146">连接数据包丢失导致在 TCP 层阻止所有调用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-146">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="keep-alive-pings"></a><span data-ttu-id="6eb1c-147">保持活动 ping</span><span class="sxs-lookup"><span data-stu-id="6eb1c-147">Keep alive pings</span></span>

<span data-ttu-id="6eb1c-148">保持活动 ping 可用于在非活动期间使 HTTP/2 连接保持为活动状态。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-148">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="6eb1c-149">如果在应用恢复活动时已准备好现有 HTTP/2 连接，则可以快速进行初始 gRPC 调用，而不会因重新建立连接而导致延迟。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-149">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="6eb1c-150">在 <xref:System.Net.Http.SocketsHttpHandler> 上配置保持活动 ping：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-150">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="6eb1c-151">前面的代码配置了一个通道，该通道在非活动期间每 60 秒向服务器发送一次保持活动 ping。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-151">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="6eb1c-152">ping 确保服务器和使用中的任何代理不会由于不活动而关闭连接。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-152">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="6eb1c-153">流式处理</span><span class="sxs-lookup"><span data-stu-id="6eb1c-153">Streaming</span></span>

<span data-ttu-id="6eb1c-154">在高性能方案中，可使用 gRPC 双向流式处理取代一元 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-154">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="6eb1c-155">双向流启动后，来回流式处理消息比使用多个一元 gRPC 调用发送消息更快。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-155">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="6eb1c-156">流式处理消息作为现有 HTTP/2 请求上的数据发送，节省了为每个一元调用创建新的 HTTP/2 请求的开销。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-156">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="6eb1c-157">示例服务：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-157">Example service:</span></span>

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

<span data-ttu-id="6eb1c-158">示例客户端：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-158">Example client:</span></span>

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

<span data-ttu-id="6eb1c-159">将一元调用替换为双向流式处理是一种高级技术，由于性能原因，这在许多情况下并不适用。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-159">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="6eb1c-160">有以下情况时，使用流式处理调用是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-160">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="6eb1c-161">需要高吞吐量或低延迟。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-161">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="6eb1c-162">gRPC 和 HTTP/2 被标识为性能瓶颈。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-162">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="6eb1c-163">客户端的辅助程序使用 gRPC 服务发送或接收常规消息。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-163">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="6eb1c-164">请注意使用流式处理调用而不是一元调用的其他复杂性和限制：</span><span class="sxs-lookup"><span data-stu-id="6eb1c-164">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="6eb1c-165">流可能会因服务或连接错误而中断。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-165">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="6eb1c-166">需要在出现错误时重启流的逻辑。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-166">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="6eb1c-167">对于多线程处理，`RequestStream.WriteAsync` 并不安全。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-167">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="6eb1c-168">一次只能将一条消息写入流中。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-168">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="6eb1c-169">通过单个流从多个线程发送消息需要制造者/使用者队列（如 <xref:System.Threading.Channels.Channel%601>）来整理消息。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-169">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="6eb1c-170">gRPC 流式处理方法仅限于接收一种类型的消息并发送一种类型的消息。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-170">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="6eb1c-171">例如，`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 并发送 `ResponseMessage`。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-171">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="6eb1c-172">Protobuf 对使用 `Any` 和 `oneof` 支持未知消息或条件消息，可以解决此限制。</span><span class="sxs-lookup"><span data-stu-id="6eb1c-172">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
