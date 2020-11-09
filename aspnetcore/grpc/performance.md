---
title: GRPC 性能最佳做法
author: jamesnk
description: 了解生成高性能 gRPC 服务的最佳做法。
monikerRange: '>= aspnetcore-3.0'
ms.author: jamesnk
ms.date: 08/23/2020
no-loc:
- 'appsettings.json'
- 'ASP.NET Core Identity'
- 'cookie'
- 'Cookie'
- 'Blazor'
- 'Blazor Server'
- 'Blazor WebAssembly'
- 'Identity'
- "Let's Encrypt"
- 'Razor'
- 'SignalR'
uid: grpc/performance
ms.openlocfilehash: 622c6ba042c5832f99bba379fadd9aba7d7163f2
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060399"
---
# <a name="performance-best-practices-with-grpc"></a><span data-ttu-id="984f7-103">GRPC 性能最佳做法</span><span class="sxs-lookup"><span data-stu-id="984f7-103">Performance best practices with gRPC</span></span>

<span data-ttu-id="984f7-104">作者：[James Newton-King](https://twitter.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="984f7-104">By [James Newton-King](https://twitter.com/jamesnk)</span></span>

<span data-ttu-id="984f7-105">gRPC 专用于高性能服务。</span><span class="sxs-lookup"><span data-stu-id="984f7-105">gRPC is designed for high-performance services.</span></span> <span data-ttu-id="984f7-106">本文档介绍如何从 gRPC 获得最佳性能。</span><span class="sxs-lookup"><span data-stu-id="984f7-106">This document explains how to get the best performance possible from gRPC.</span></span>

## <a name="reuse-grpc-channels"></a><span data-ttu-id="984f7-107">重用 gRPC 通道</span><span class="sxs-lookup"><span data-stu-id="984f7-107">Reuse gRPC channels</span></span>

<span data-ttu-id="984f7-108">进行 gRPC 调用时，应重新使用 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="984f7-108">A gRPC channel should be reused when making gRPC calls.</span></span> <span data-ttu-id="984f7-109">重用通道后通过现有的 HTTP/2 连接对调用进行多路复用。</span><span class="sxs-lookup"><span data-stu-id="984f7-109">Reusing a channel allows calls to be multiplexed through an existing HTTP/2 connection.</span></span>

<span data-ttu-id="984f7-110">如果为每个 gRPC 调用创建一个新通道，则完成此操作所需的时间可能会显著增加。</span><span class="sxs-lookup"><span data-stu-id="984f7-110">If a new channel is created for each gRPC call then the amount of time it takes to complete can increase significantly.</span></span> <span data-ttu-id="984f7-111">每次调用都需要在客户端和服务器之间进行多个网络往返，以创建新的 HTTP/2 连接：</span><span class="sxs-lookup"><span data-stu-id="984f7-111">Each call will require multiple network round-trips between the client and the server to create a new HTTP/2 connection:</span></span>

1. <span data-ttu-id="984f7-112">打开套接字</span><span class="sxs-lookup"><span data-stu-id="984f7-112">Opening a socket</span></span>
2. <span data-ttu-id="984f7-113">建立 TCP 连接</span><span class="sxs-lookup"><span data-stu-id="984f7-113">Establishing TCP connection</span></span>
3. <span data-ttu-id="984f7-114">协商 TLS</span><span class="sxs-lookup"><span data-stu-id="984f7-114">Negotiating TLS</span></span>
4. <span data-ttu-id="984f7-115">启动 HTTP/2 连接</span><span class="sxs-lookup"><span data-stu-id="984f7-115">Starting HTTP/2 connection</span></span>
5. <span data-ttu-id="984f7-116">进行 gRPC 调用</span><span class="sxs-lookup"><span data-stu-id="984f7-116">Making the gRPC call</span></span>

<span data-ttu-id="984f7-117">在 gRPC 调用之间可以安全地共享和重用通道：</span><span class="sxs-lookup"><span data-stu-id="984f7-117">Channels are safe to share and reuse between gRPC calls:</span></span>

* <span data-ttu-id="984f7-118">gRPC 客户端是使用通道创建的。</span><span class="sxs-lookup"><span data-stu-id="984f7-118">gRPC clients are created with channels.</span></span> <span data-ttu-id="984f7-119">gRPC 客户端是轻型对象，无需缓存或重用。</span><span class="sxs-lookup"><span data-stu-id="984f7-119">gRPC clients are lightweight objects and don't need to be cached or reused.</span></span>
* <span data-ttu-id="984f7-120">可从一个通道创建多个 gRPC 客户端（包括不同类型的客户端）。</span><span class="sxs-lookup"><span data-stu-id="984f7-120">Multiple gRPC clients can be created from a channel, including different types of clients.</span></span>
* <span data-ttu-id="984f7-121">通道和从该通道创建的客户端可由多个线程安全使用。</span><span class="sxs-lookup"><span data-stu-id="984f7-121">A channel and clients created from the channel can safely be used by multiple threads.</span></span>
* <span data-ttu-id="984f7-122">从通道创建的客户端可同时进行多个调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-122">Clients created from the channel can make multiple simultaneous calls.</span></span>

<span data-ttu-id="984f7-123">GRPC 客户端工厂提供了一种集中配置通道的方法。</span><span class="sxs-lookup"><span data-stu-id="984f7-123">gRPC client factory offers a centralized way to configure channels.</span></span> <span data-ttu-id="984f7-124">它会自动重用基础通道。</span><span class="sxs-lookup"><span data-stu-id="984f7-124">It automatically reuses underlying channels.</span></span> <span data-ttu-id="984f7-125">有关详细信息，请参阅 <xref:grpc/clientfactory>。</span><span class="sxs-lookup"><span data-stu-id="984f7-125">For more information, see <xref:grpc/clientfactory>.</span></span>

## <a name="connection-concurrency"></a><span data-ttu-id="984f7-126">连接并发</span><span class="sxs-lookup"><span data-stu-id="984f7-126">Connection concurrency</span></span>

<span data-ttu-id="984f7-127">HTTP/2 连接通常会限制一个连接上同时存在的[最大并发流（活动 HTTP 请求）数](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2)。</span><span class="sxs-lookup"><span data-stu-id="984f7-127">HTTP/2 connections typically have a limit on the number of [maximum concurrent streams (active HTTP requests)](https://httpwg.github.io/specs/rfc7540.html#rfc.section.5.1.2) on a connection at one time.</span></span> <span data-ttu-id="984f7-128">默认情况下，大多数服务器将此限制设置为 100 个并发流。</span><span class="sxs-lookup"><span data-stu-id="984f7-128">By default, most servers set this limit to 100 concurrent streams.</span></span>

<span data-ttu-id="984f7-129">gRPC 通道使用单个 HTTP/2 连接，并且并发调用在该连接上多路复用。</span><span class="sxs-lookup"><span data-stu-id="984f7-129">A gRPC channel uses a single HTTP/2 connection, and concurrent calls are multiplexed on that connection.</span></span> <span data-ttu-id="984f7-130">当活动调用数达到连接流限制时，其他调用会在客户端中排队。</span><span class="sxs-lookup"><span data-stu-id="984f7-130">When the number of active calls reaches the connection stream limit, additional calls are queued in the client.</span></span> <span data-ttu-id="984f7-131">排队调用等待活动调用完成后再发送。</span><span class="sxs-lookup"><span data-stu-id="984f7-131">Queued calls wait for active calls to complete before they are sent.</span></span> <span data-ttu-id="984f7-132">由于此限制，具有高负载或长时间运行的流式处理 gRPC 调用的应用程序可能会因调用排队而出现性能问题。</span><span class="sxs-lookup"><span data-stu-id="984f7-132">Applications with high load, or long running streaming gRPC calls, could see performance issues caused by calls queuing because of this limit.</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="984f7-133">.NET 5 引入 `SocketsHttpHandler.EnableMultipleHttp2Connections` 属性。</span><span class="sxs-lookup"><span data-stu-id="984f7-133">.NET 5 introduces the `SocketsHttpHandler.EnableMultipleHttp2Connections` property.</span></span> <span data-ttu-id="984f7-134">如果设置为 `true`，则当达到并发流限制时，通道会创建额外的 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="984f7-134">When set to `true`, additional HTTP/2 connections are created by a channel when the concurrent stream limit is reached.</span></span> <span data-ttu-id="984f7-135">创建 `GrpcChannel` 时，会自动将其内部 `SocketsHttpHandler` 配置为创建额外的 HTTP/2 连接。</span><span class="sxs-lookup"><span data-stu-id="984f7-135">When a `GrpcChannel` is created its internal `SocketsHttpHandler` is automatically configured to create additional HTTP/2 connections.</span></span> <span data-ttu-id="984f7-136">如果应用配置其自己的处理程序，请考虑将 `EnableMultipleHttp2Connections` 设置为 `true`：</span><span class="sxs-lookup"><span data-stu-id="984f7-136">If an app configures its own handler, consider setting `EnableMultipleHttp2Connections` to `true`:</span></span>

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

<span data-ttu-id="984f7-137">.NET Core 3.1 应用有几种解决方法：</span><span class="sxs-lookup"><span data-stu-id="984f7-137">There are a couple of workarounds for .NET Core 3.1 apps:</span></span>

* <span data-ttu-id="984f7-138">为具有高负载的应用的区域创建单独的 gRPC 通道。</span><span class="sxs-lookup"><span data-stu-id="984f7-138">Create separate gRPC channels for areas of the app with high load.</span></span> <span data-ttu-id="984f7-139">例如，`Logger` gRPC 服务可能具有高负载。</span><span class="sxs-lookup"><span data-stu-id="984f7-139">For example, the `Logger` gRPC service might have a high load.</span></span> <span data-ttu-id="984f7-140">使用单独的通道在应用中创建 `LoggerClient`。</span><span class="sxs-lookup"><span data-stu-id="984f7-140">Use a separate channel to create the `LoggerClient` in the app.</span></span>
* <span data-ttu-id="984f7-141">使用 gRPC 通道池，例如创建 gRPC 通道列表。</span><span class="sxs-lookup"><span data-stu-id="984f7-141">Use a pool of gRPC channels, for example,  create a list of gRPC channels.</span></span> <span data-ttu-id="984f7-142">每次需要 gRPC 通道时，使用 `Random` 从列表中选取一个通道。</span><span class="sxs-lookup"><span data-stu-id="984f7-142">`Random` is used to pick a channel from the list each time a gRPC channel is needed.</span></span> <span data-ttu-id="984f7-143">使用 `Random` 在多个连接上随机分配调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-143">Using `Random` randomly distributes calls over multiple connections.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="984f7-144">提升服务器上的最大并发流限制是解决此问题的另一种方法。</span><span class="sxs-lookup"><span data-stu-id="984f7-144">Increasing the maximum concurrent stream limit on the server is another way to solve this problem.</span></span> <span data-ttu-id="984f7-145">在 Kestrel 中，这是用 <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection> 配置的。</span><span class="sxs-lookup"><span data-stu-id="984f7-145">In Kestrel this is configured with <xref:Microsoft.AspNetCore.Server.Kestrel.Core.Http2Limits.MaxStreamsPerConnection>.</span></span>
>
> <span data-ttu-id="984f7-146">不建议提升最大并发流限制。</span><span class="sxs-lookup"><span data-stu-id="984f7-146">Increasing the maximum concurrent stream limit is not recommended.</span></span> <span data-ttu-id="984f7-147">单个 HTTP/2 连接上的流过多会带来新的性能问题：</span><span class="sxs-lookup"><span data-stu-id="984f7-147">Too many streams on a single HTTP/2 connection introduces new performance issues:</span></span>
>
> * <span data-ttu-id="984f7-148">尝试写入连接的流之间发生线程争用。</span><span class="sxs-lookup"><span data-stu-id="984f7-148">Thread contention between streams trying to write to the connection.</span></span>
> * <span data-ttu-id="984f7-149">连接数据包丢失导致在 TCP 层阻止所有调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-149">Connection packet loss causes all calls to be blocked at the TCP layer.</span></span>

## <a name="load-balancing"></a><span data-ttu-id="984f7-150">负载均衡</span><span class="sxs-lookup"><span data-stu-id="984f7-150">Load balancing</span></span>

<span data-ttu-id="984f7-151">一些负载均衡器不能与 gRPC 一起高效工作。</span><span class="sxs-lookup"><span data-stu-id="984f7-151">Some load balancers don't work effectively with gRPC.</span></span> <span data-ttu-id="984f7-152">通过在终结点之间分布 TCP 连接，L4（传输）负载均衡器在连接级别上运行。</span><span class="sxs-lookup"><span data-stu-id="984f7-152">L4 (transport) load balancers operate at a connection level, by distributing TCP connections across endpoints.</span></span> <span data-ttu-id="984f7-153">这种方法非常适合使用 HTTP / 1.1 进行的负载均衡 API 调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-153">This approach works well for loading balancing API calls made with HTTP/1.1.</span></span> <span data-ttu-id="984f7-154">使用 HTTP/1.1 进行的并发调用在不同的连接上发送，实现调用在终结点之间的负载均衡。</span><span class="sxs-lookup"><span data-stu-id="984f7-154">Concurrent calls made with HTTP/1.1 are sent on different connections, allowing calls to be load balanced across endpoints.</span></span>

<span data-ttu-id="984f7-155">由于 L4 负载均衡器是在连接级别运行的，它们不太适用于 gRPC。</span><span class="sxs-lookup"><span data-stu-id="984f7-155">Because L4 load balancers operate at a connection level, they don't work well with gRPC.</span></span> <span data-ttu-id="984f7-156">GRPC 使用 HTTP/2，在单个 TCP 连接上多路复用多个调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-156">gRPC uses HTTP/2, which multiplexes multiple calls on a single TCP connection.</span></span> <span data-ttu-id="984f7-157">通过该连接的所有 gRPC 调用都将前往一个终结点。</span><span class="sxs-lookup"><span data-stu-id="984f7-157">All gRPC calls over that connection go to one endpoint.</span></span>

<span data-ttu-id="984f7-158">有两种方法可以高效地对 gRPC 进行负载均衡：</span><span class="sxs-lookup"><span data-stu-id="984f7-158">There are two options to effectively load balance gRPC:</span></span>

* <span data-ttu-id="984f7-159">客户端负载均衡</span><span class="sxs-lookup"><span data-stu-id="984f7-159">Client-side load balancing</span></span>
* <span data-ttu-id="984f7-160">L7（应用程序）代理负载均衡</span><span class="sxs-lookup"><span data-stu-id="984f7-160">L7 (application) proxy load balancing</span></span>

> [!NOTE]
> <span data-ttu-id="984f7-161">只有 gRPC 调用可以在终结点之间进行负载均衡。</span><span class="sxs-lookup"><span data-stu-id="984f7-161">Only gRPC calls can be load balanced between endpoints.</span></span> <span data-ttu-id="984f7-162">一旦建立了流式 gRPC 调用，通过流发送的所有消息都将前往一个终结点。</span><span class="sxs-lookup"><span data-stu-id="984f7-162">Once a streaming gRPC call is established, all messages sent over the stream go to one endpoint.</span></span>

### <a name="client-side-load-balancing"></a><span data-ttu-id="984f7-163">客户端负载均衡</span><span class="sxs-lookup"><span data-stu-id="984f7-163">Client-side load balancing</span></span>

<span data-ttu-id="984f7-164">对于客户端负载均衡，客户端了解终结点。</span><span class="sxs-lookup"><span data-stu-id="984f7-164">With client-side load balancing, the client knows about endpoints.</span></span> <span data-ttu-id="984f7-165">对于每个 gRPC 调用，客户端会选择一个不同的终结点作为将该调用发送到的目的地。</span><span class="sxs-lookup"><span data-stu-id="984f7-165">For each gRPC call it selects a different endpoint to send the call to.</span></span> <span data-ttu-id="984f7-166">如果延迟很重要，那么客户端负载均衡是一个很好的选择。</span><span class="sxs-lookup"><span data-stu-id="984f7-166">Client-side load balancing is a good choice when latency is important.</span></span> <span data-ttu-id="984f7-167">客户端和服务之间没有代理，因此调用直接发送到服务。</span><span class="sxs-lookup"><span data-stu-id="984f7-167">There is no proxy between the client and the service so the call is sent to the service directly.</span></span> <span data-ttu-id="984f7-168">客户端负载均衡的缺点是每个客户端必须跟踪它应该使用的可用终结点。</span><span class="sxs-lookup"><span data-stu-id="984f7-168">The downside to client-side load balancing is that each client must keep track of available endpoints it should use.</span></span>

<span data-ttu-id="984f7-169">Lookaside 客户端负载均衡是一种将负载均衡状态存储在中心位置的技术。</span><span class="sxs-lookup"><span data-stu-id="984f7-169">Lookaside client load balancing is a technique where load balancing state is stored in a central location.</span></span> <span data-ttu-id="984f7-170">客户端定期查询中心位置以获取在作出负载均衡决策时要使用的信息。</span><span class="sxs-lookup"><span data-stu-id="984f7-170">Clients periodically query the central location for information to use when making load balancing decisions.</span></span>

<span data-ttu-id="984f7-171">`Grpc.Net.Client` 当前不支持客户端负载均衡。</span><span class="sxs-lookup"><span data-stu-id="984f7-171">`Grpc.Net.Client` currently doesn't support client-side load balancing.</span></span> <span data-ttu-id="984f7-172">如果 .NET 中需要客户端负载均衡，则 [Grpc.Core](https://www.nuget.org/packages/Grpc.Core) 是一个不错的选择。</span><span class="sxs-lookup"><span data-stu-id="984f7-172">[Grpc.Core](https://www.nuget.org/packages/Grpc.Core) is a good choice if client-side load balancing is required in .NET.</span></span>

### <a name="proxy-load-balancing"></a><span data-ttu-id="984f7-173">代理负载均衡</span><span class="sxs-lookup"><span data-stu-id="984f7-173">Proxy load balancing</span></span>

<span data-ttu-id="984f7-174">L7（应用程序）代理的工作级别高于 L4（传输）代理。</span><span class="sxs-lookup"><span data-stu-id="984f7-174">An L7 (application) proxy works at a higher level than an L4 (transport) proxy.</span></span> <span data-ttu-id="984f7-175">L7 代理了解 HTTP/2，并且能够在多个终结点之间的一个 HTTP/2 连接上将多路复用的 gRPC 调用分发给代理。</span><span class="sxs-lookup"><span data-stu-id="984f7-175">L7 proxies understand HTTP/2, and are able to distribute gRPC calls multiplexed to the proxy on one HTTP/2 connection across multiple endpoints.</span></span> <span data-ttu-id="984f7-176">使用代理比客户端负载均衡更简单，但会增加 gRPC 调用的额外延迟。</span><span class="sxs-lookup"><span data-stu-id="984f7-176">Using a proxy is simpler than client-side load balancing, but can add extra latency to gRPC calls.</span></span>

<span data-ttu-id="984f7-177">有很多 L7 代理可用。</span><span class="sxs-lookup"><span data-stu-id="984f7-177">There are many L7 proxies available.</span></span> <span data-ttu-id="984f7-178">一些选项包括：</span><span class="sxs-lookup"><span data-stu-id="984f7-178">Some options are:</span></span>

* <span data-ttu-id="984f7-179">[Envoy](https://www.envoyproxy.io/) - 一种常用的开源代理。</span><span class="sxs-lookup"><span data-stu-id="984f7-179">[Envoy](https://www.envoyproxy.io/) - A popular open source proxy.</span></span>
* <span data-ttu-id="984f7-180">[Linkerd](https://linkerd.io/) - Kubernetes 服务网格。</span><span class="sxs-lookup"><span data-stu-id="984f7-180">[Linkerd](https://linkerd.io/) - Service mesh for Kubernetes.</span></span>
* <span data-ttu-id="984f7-181">[YARP:反向代理](https://microsoft.github.io/reverse-proxy/) - 用 .NET 编写的预览开源代理。</span><span class="sxs-lookup"><span data-stu-id="984f7-181">[YARP: A Reverse Proxy](https://microsoft.github.io/reverse-proxy/) - A preview open source proxy written in .NET.</span></span>

::: moniker range=">= aspnetcore-5.0"

## <a name="inter-process-communication"></a><span data-ttu-id="984f7-182">进程内通信</span><span class="sxs-lookup"><span data-stu-id="984f7-182">Inter-process communication</span></span>

<span data-ttu-id="984f7-183">客户端和服务之间的 gRPC 调用通常通过 TCP 套接字发送。</span><span class="sxs-lookup"><span data-stu-id="984f7-183">gRPC calls between a client and service are usually sent over TCP sockets.</span></span> <span data-ttu-id="984f7-184">TCP 非常适用于网络中的通信，但当客户端和服务在同一台计算机上时，[进程间通信 (IPC)](https://wikipedia.org/wiki/Inter-process_communication) 的效率更高。</span><span class="sxs-lookup"><span data-stu-id="984f7-184">TCP is great for communicating across a network, but [inter-process communication (IPC)](https://wikipedia.org/wiki/Inter-process_communication) is more efficient when the client and service are on the same machine.</span></span>

<span data-ttu-id="984f7-185">考虑在同一台计算机上的进程之间使用 Unix 域套接字或命名管道之类的传输进行 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-185">Consider using a transport like Unix domain sockets or named pipes for gRPC calls between processes on the same machine.</span></span> <span data-ttu-id="984f7-186">有关详细信息，请参阅 <xref:grpc/interprocess>。</span><span class="sxs-lookup"><span data-stu-id="984f7-186">For more information, see <xref:grpc/interprocess>.</span></span>

## <a name="keep-alive-pings"></a><span data-ttu-id="984f7-187">保持活动 ping</span><span class="sxs-lookup"><span data-stu-id="984f7-187">Keep alive pings</span></span>

<span data-ttu-id="984f7-188">保持活动 ping 可用于在非活动期间使 HTTP/2 连接保持为活动状态。</span><span class="sxs-lookup"><span data-stu-id="984f7-188">Keep alive pings can be used to keep HTTP/2 connections alive during periods of inactivity.</span></span> <span data-ttu-id="984f7-189">如果在应用恢复活动时已准备好现有 HTTP/2 连接，则可以快速进行初始 gRPC 调用，而不会因重新建立连接而导致延迟。</span><span class="sxs-lookup"><span data-stu-id="984f7-189">Having an existing HTTP/2 connection ready when an app resumes activity allows for the initial gRPC calls to be made quickly, without a delay caused by the connection being reestablished.</span></span>

<span data-ttu-id="984f7-190">在 <xref:System.Net.Http.SocketsHttpHandler> 上配置保持活动 ping：</span><span class="sxs-lookup"><span data-stu-id="984f7-190">Keep alive pings are configured on <xref:System.Net.Http.SocketsHttpHandler>:</span></span>

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

<span data-ttu-id="984f7-191">前面的代码配置了一个通道，该通道在非活动期间每 60 秒向服务器发送一次保持活动 ping。</span><span class="sxs-lookup"><span data-stu-id="984f7-191">The preceding code configures a channel that sends a keep alive ping to the server every 60 seconds during periods of inactivity.</span></span> <span data-ttu-id="984f7-192">ping 确保服务器和使用中的任何代理不会由于不活动而关闭连接。</span><span class="sxs-lookup"><span data-stu-id="984f7-192">The ping ensures the server and any proxies in use won't close the connection because of inactivity.</span></span>

::: moniker-end

## <a name="streaming"></a><span data-ttu-id="984f7-193">流式处理</span><span class="sxs-lookup"><span data-stu-id="984f7-193">Streaming</span></span>

<span data-ttu-id="984f7-194">在高性能方案中，可使用 gRPC 双向流式处理取代一元 gRPC 调用。</span><span class="sxs-lookup"><span data-stu-id="984f7-194">gRPC bidirectional streaming can be used to replace unary gRPC calls in high-performance scenarios.</span></span> <span data-ttu-id="984f7-195">双向流启动后，来回流式处理消息比使用多个一元 gRPC 调用发送消息更快。</span><span class="sxs-lookup"><span data-stu-id="984f7-195">Once a bidirectional stream has started, streaming messages back and forth is faster than sending messages with multiple unary gRPC calls.</span></span> <span data-ttu-id="984f7-196">流式处理消息作为现有 HTTP/2 请求上的数据发送，节省了为每个一元调用创建新的 HTTP/2 请求的开销。</span><span class="sxs-lookup"><span data-stu-id="984f7-196">Streamed messages are sent as data on an existing HTTP/2 request and eliminates the overhead of creating a new HTTP/2 request for each unary call.</span></span>

<span data-ttu-id="984f7-197">示例服务：</span><span class="sxs-lookup"><span data-stu-id="984f7-197">Example service:</span></span>

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

<span data-ttu-id="984f7-198">示例客户端：</span><span class="sxs-lookup"><span data-stu-id="984f7-198">Example client:</span></span>

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

<span data-ttu-id="984f7-199">将一元调用替换为双向流式处理是一种高级技术，由于性能原因，这在许多情况下并不适用。</span><span class="sxs-lookup"><span data-stu-id="984f7-199">Replacing unary calls with bidirectional streaming for performance reasons is an advanced technique and is not appropriate in many situations.</span></span>

<span data-ttu-id="984f7-200">有以下情况时，使用流式处理调用是一个不错的选择：</span><span class="sxs-lookup"><span data-stu-id="984f7-200">Using streaming calls is a good choice when:</span></span>

1. <span data-ttu-id="984f7-201">需要高吞吐量或低延迟。</span><span class="sxs-lookup"><span data-stu-id="984f7-201">High throughput or low latency is required.</span></span>
2. <span data-ttu-id="984f7-202">gRPC 和 HTTP/2 被标识为性能瓶颈。</span><span class="sxs-lookup"><span data-stu-id="984f7-202">gRPC and HTTP/2 are identified as a performance bottleneck.</span></span>
3. <span data-ttu-id="984f7-203">客户端的辅助程序使用 gRPC 服务发送或接收常规消息。</span><span class="sxs-lookup"><span data-stu-id="984f7-203">A worker in the client is sending or receiving regular messages with a gRPC service.</span></span>

<span data-ttu-id="984f7-204">请注意使用流式处理调用而不是一元调用的其他复杂性和限制：</span><span class="sxs-lookup"><span data-stu-id="984f7-204">Be aware of the additional complexity and limitations of using streaming calls instead of unary:</span></span>

1. <span data-ttu-id="984f7-205">流可能会因服务或连接错误而中断。</span><span class="sxs-lookup"><span data-stu-id="984f7-205">A stream can be interrupted by a service or connection error.</span></span> <span data-ttu-id="984f7-206">需要在出现错误时重启流的逻辑。</span><span class="sxs-lookup"><span data-stu-id="984f7-206">Logic is required to restart stream if there is an error.</span></span>
2. <span data-ttu-id="984f7-207">对于多线程处理，`RequestStream.WriteAsync` 并不安全。</span><span class="sxs-lookup"><span data-stu-id="984f7-207">`RequestStream.WriteAsync` is not safe for multi-threading.</span></span> <span data-ttu-id="984f7-208">一次只能将一条消息写入流中。</span><span class="sxs-lookup"><span data-stu-id="984f7-208">Only one message can be written to a stream at a time.</span></span> <span data-ttu-id="984f7-209">通过单个流从多个线程发送消息需要制造者/使用者队列（如 <xref:System.Threading.Channels.Channel%601>）来整理消息。</span><span class="sxs-lookup"><span data-stu-id="984f7-209">Sending messages from multiple threads over a single stream requires a producer/consumer queue like <xref:System.Threading.Channels.Channel%601> to marshall messages.</span></span>
3. <span data-ttu-id="984f7-210">gRPC 流式处理方法仅限于接收一种类型的消息并发送一种类型的消息。</span><span class="sxs-lookup"><span data-stu-id="984f7-210">A gRPC streaming method is limited to receiving one type of message and sending one type of message.</span></span> <span data-ttu-id="984f7-211">例如，`rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` 接收 `RequestMessage` 并发送 `ResponseMessage`。</span><span class="sxs-lookup"><span data-stu-id="984f7-211">For example, `rpc StreamingCall(stream RequestMessage) returns (stream ResponseMessage)` receives `RequestMessage` and sends `ResponseMessage`.</span></span> <span data-ttu-id="984f7-212">Protobuf 对使用 `Any` 和 `oneof` 支持未知消息或条件消息，可以解决此限制。</span><span class="sxs-lookup"><span data-stu-id="984f7-212">Protobuf's support for unknown or conditional messages using `Any` and `oneof` can work around this limitation.</span></span>
