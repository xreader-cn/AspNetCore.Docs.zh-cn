---
title: ASP.NET Core SignalR 生产承载和扩展
author: bradygaster
description: 了解如何避免使用 ASP.NET Core 的应用程序中的性能和缩放问题 SignalR 。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/17/2020
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
uid: signalr/scale
ms.openlocfilehash: 2d128d54dc9b1189124563e45d72d74b19704ab1
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88022519"
---
# <a name="aspnet-core-no-locsignalr-hosting-and-scaling"></a><span data-ttu-id="f9a35-103">ASP.NET Core SignalR 托管和缩放</span><span class="sxs-lookup"><span data-stu-id="f9a35-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="f9a35-104">作者： [Andrew Stanton](https://twitter.com/anurse)、 [Brady Gaster](https://twitter.com/bradygaster)和[Tom Dykstra](https://github.com/tdykstra)</span><span class="sxs-lookup"><span data-stu-id="f9a35-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="f9a35-105">本文介绍使用 ASP.NET Core 的高流量应用的托管和扩展注意事项 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f9a35-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="sticky-sessions"></a><span data-ttu-id="f9a35-106">粘滞会话</span><span class="sxs-lookup"><span data-stu-id="f9a35-106">Sticky Sessions</span></span>

<span data-ttu-id="f9a35-107">SignalR要求针对特定连接的所有 HTTP 请求都由同一服务器进程处理。</span><span class="sxs-lookup"><span data-stu-id="f9a35-107">SignalR requires that all HTTP requests for a specific connection be handled by the same server process.</span></span> <span data-ttu-id="f9a35-108">当 SignalR 在服务器场中运行 (多个服务器) 时，必须使用 "粘滞会话"。</span><span class="sxs-lookup"><span data-stu-id="f9a35-108">When SignalR is running on a server farm (multiple servers), "sticky sessions" must be used.</span></span> <span data-ttu-id="f9a35-109">某些负载均衡器也称为 "粘滞会话"。</span><span class="sxs-lookup"><span data-stu-id="f9a35-109">"Sticky sessions" are also called session affinity by some load balancers.</span></span> <span data-ttu-id="f9a35-110">Azure App Service 使用[应用程序请求路由](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) 来路由请求。</span><span class="sxs-lookup"><span data-stu-id="f9a35-110">Azure App Service uses [Application Request Routing](https://docs.microsoft.com/iis/extensions/planning-for-arr/application-request-routing-version-2-overview) (ARR) to route requests.</span></span> <span data-ttu-id="f9a35-111">启用 Azure App Service 中的 "ARR 相似性" 设置将启用 "粘滞会话"。</span><span class="sxs-lookup"><span data-stu-id="f9a35-111">Enabling the "ARR Affinity" setting in your Azure App Service will enable "sticky sessions".</span></span> <span data-ttu-id="f9a35-112">不需要手写会话的唯一情况是：</span><span class="sxs-lookup"><span data-stu-id="f9a35-112">The only circumstances in which sticky sessions are not required are:</span></span>

1. <span data-ttu-id="f9a35-113">在单个服务器上承载时，在单个进程中。</span><span class="sxs-lookup"><span data-stu-id="f9a35-113">When hosting on a single server, in a single process.</span></span>
1. <span data-ttu-id="f9a35-114">使用 Azure 服务时 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f9a35-114">When using the Azure SignalR Service.</span></span>
1. <span data-ttu-id="f9a35-115">当所有客户端都配置为**仅**使用 websocket 时，**并且**在客户端配置中启用了[SkipNegotiation 设置](xref:signalr/configuration#configure-additional-options)。</span><span class="sxs-lookup"><span data-stu-id="f9a35-115">When all clients are configured to **only** use WebSockets, **and** the [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span>

<span data-ttu-id="f9a35-116">在所有其他情况下 (包括) 使用 Redis 底板时，必须为粘滞会话配置服务器环境。</span><span class="sxs-lookup"><span data-stu-id="f9a35-116">In all other circumstances (including when the Redis backplane is used), the server environment must be configured for sticky sessions.</span></span>

<span data-ttu-id="f9a35-117">有关为配置 Azure App Service 的指南 SignalR ，请参阅 <xref:signalr/publish-to-azure-web-app> 。</span><span class="sxs-lookup"><span data-stu-id="f9a35-117">For guidance on configuring Azure App Service for SignalR, see <xref:signalr/publish-to-azure-web-app>.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="f9a35-118">TCP 连接资源</span><span class="sxs-lookup"><span data-stu-id="f9a35-118">TCP connection resources</span></span>

<span data-ttu-id="f9a35-119">Web 服务器可以支持的并发 TCP 连接数受到限制。</span><span class="sxs-lookup"><span data-stu-id="f9a35-119">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="f9a35-120">标准 HTTP 客户端使用*临时*连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-120">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="f9a35-121">当客户端进入空闲状态并在稍后重新打开时，可以关闭这些连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-121">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="f9a35-122">另一方面， SignalR 连接是*永久性*的。</span><span class="sxs-lookup"><span data-stu-id="f9a35-122">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="f9a35-123">SignalR即使客户端进入空闲状态，连接仍保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="f9a35-123">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="f9a35-124">在服务于多个客户端的高流量应用程序中，这些持久连接可能会导致服务器达到其最大连接数。</span><span class="sxs-lookup"><span data-stu-id="f9a35-124">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="f9a35-125">持久性连接还会占用一些额外的内存，用于跟踪每个连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-125">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="f9a35-126">与连接相关的资源的大量使用 SignalR 会影响托管在同一服务器上的其他 web 应用程序。</span><span class="sxs-lookup"><span data-stu-id="f9a35-126">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="f9a35-127">当 SignalR 打开并保存最近可用的 TCP 连接时，同一服务器上的其他 web 应用也不会有更多的可用连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-127">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="f9a35-128">如果服务器的连接用尽，你会看到随机套接字错误和连接重置错误。</span><span class="sxs-lookup"><span data-stu-id="f9a35-128">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="f9a35-129">例如：</span><span class="sxs-lookup"><span data-stu-id="f9a35-129">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="f9a35-130">若要防止 SignalR 资源使用导致其他 web 应用中出现错误，请 SignalR 在不同于其他 web 应用的服务器上运行。</span><span class="sxs-lookup"><span data-stu-id="f9a35-130">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="f9a35-131">为了避免 SignalR 资源使用导致应用中出现错误 SignalR ，横向扩展以限制服务器必须处理的连接数。</span><span class="sxs-lookup"><span data-stu-id="f9a35-131">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="f9a35-132">向外扩展</span><span class="sxs-lookup"><span data-stu-id="f9a35-132">Scale out</span></span>

<span data-ttu-id="f9a35-133">使用的应用程序 SignalR 需要跟踪其所有连接，这会为服务器场带来问题。</span><span class="sxs-lookup"><span data-stu-id="f9a35-133">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="f9a35-134">添加服务器，并获取其他服务器不知道的新连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-134">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="f9a35-135">例如， SignalR 在下图中的每个服务器上都不知道其他服务器上的连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-135">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="f9a35-136">当 SignalR 某个服务器要向所有客户端发送消息时，该消息只会发送到连接到该服务器的客户端。</span><span class="sxs-lookup"><span data-stu-id="f9a35-136">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![缩放：：：非 loc (SignalR) ：：：无底板](scale/_static/scale-no-backplane.png)

<span data-ttu-id="f9a35-138">解决此问题的方法是[Azure SignalR 服务](#azure-signalr-service)和[Redis 底板](#redis-backplane)。</span><span class="sxs-lookup"><span data-stu-id="f9a35-138">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-no-locsignalr-service"></a><span data-ttu-id="f9a35-139">Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="f9a35-139">Azure SignalR Service</span></span>

<span data-ttu-id="f9a35-140">Azure SignalR 服务是一种代理，而不是底板。</span><span class="sxs-lookup"><span data-stu-id="f9a35-140">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="f9a35-141">每次客户端启动与服务器的连接时，客户端都将被重定向以连接到服务。</span><span class="sxs-lookup"><span data-stu-id="f9a35-141">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="f9a35-142">下图说明了该过程：</span><span class="sxs-lookup"><span data-stu-id="f9a35-142">That process is illustrated in the following diagram:</span></span>

![建立与 Azure：：： no (SignalR) ：：： Service 的连接](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="f9a35-144">因此，服务管理所有客户端连接，而每个服务器只需要与服务建立少量的固定连接，如下图所示：</span><span class="sxs-lookup"><span data-stu-id="f9a35-144">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![连接到服务的客户端，连接到服务的服务器](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="f9a35-146">与 Redis 底板替代方法相比，这种扩展方法具有多个优点：</span><span class="sxs-lookup"><span data-stu-id="f9a35-146">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="f9a35-147">粘滞会话（也称为[客户端关联](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)）不是必需的，因为客户端在连接时立即重定向到 Azure SignalR 服务。</span><span class="sxs-lookup"><span data-stu-id="f9a35-147">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="f9a35-148">SignalR应用可根据发送的消息数进行扩展，而 Azure SignalR 服务会自动缩放以处理任意数量的连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-148">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="f9a35-149">例如，可能有数千个客户端，但如果每秒只发送了几条消息，则该 SignalR 应用无需向外扩展到多个服务器即可直接处理连接。</span><span class="sxs-lookup"><span data-stu-id="f9a35-149">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="f9a35-150">SignalR与 web 应用相比，应用不会使用更多的连接资源 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f9a35-150">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="f9a35-151">出于此原因，我们建议 azure SignalR 服务适用于 SignalR azure 上托管的所有 ASP.NET Core 应用，包括应用服务、vm 和容器。</span><span class="sxs-lookup"><span data-stu-id="f9a35-151">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="f9a35-152">有关详细信息，请参阅[Azure SignalR 服务文档](/azure/azure-signalr/signalr-overview)。</span><span class="sxs-lookup"><span data-stu-id="f9a35-152">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="f9a35-153">Redis 底板</span><span class="sxs-lookup"><span data-stu-id="f9a35-153">Redis backplane</span></span>

<span data-ttu-id="f9a35-154">[Redis](https://redis.io/)是内存中的键-值存储，它支持具有发布/订阅模型的消息传送系统。</span><span class="sxs-lookup"><span data-stu-id="f9a35-154">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="f9a35-155">SignalRRedis 底板使用 pub/sub 功能将消息转发到其他服务器。</span><span class="sxs-lookup"><span data-stu-id="f9a35-155">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="f9a35-156">当客户端建立连接时，会将连接信息传递到底板。</span><span class="sxs-lookup"><span data-stu-id="f9a35-156">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="f9a35-157">当服务器要向所有客户端发送消息时，它会发送到底板。</span><span class="sxs-lookup"><span data-stu-id="f9a35-157">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="f9a35-158">底板知道所有连接的客户端和它们所在的服务器。</span><span class="sxs-lookup"><span data-stu-id="f9a35-158">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="f9a35-159">它通过各自的服务器将消息发送到所有客户端。</span><span class="sxs-lookup"><span data-stu-id="f9a35-159">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="f9a35-160">下图演示了此过程：</span><span class="sxs-lookup"><span data-stu-id="f9a35-160">This process is illustrated in the following diagram:</span></span>

![Redis 底板，从一台服务器发送到所有客户端的消息](scale/_static/redis-backplane.png)

<span data-ttu-id="f9a35-162">对于托管在你自己的基础结构上的应用，建议使用 Redis 底板。</span><span class="sxs-lookup"><span data-stu-id="f9a35-162">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="f9a35-163">如果在数据中心与 Azure 数据中心之间存在明显的连接延迟，则 SignalR 对于具有低延迟或高吞吐量要求的本地应用，Azure 服务可能不是可行的选择。</span><span class="sxs-lookup"><span data-stu-id="f9a35-163">If there is significant connection latency between your data center and an Azure data center, Azure SignalR Service may not be a practical option for on-premises apps with low latency or high throughput requirements.</span></span>

<span data-ttu-id="f9a35-164">前面所 SignalR 述的 Azure 服务优点是 Redis 底板的缺点：</span><span class="sxs-lookup"><span data-stu-id="f9a35-164">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="f9a35-165">需要将粘滞会话（也称为[客户端关联](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)）用于以下**两**种情况：</span><span class="sxs-lookup"><span data-stu-id="f9a35-165">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required, except when **both** of the following are true:</span></span>
  * <span data-ttu-id="f9a35-166">所有客户端都配置为**仅**使用 websocket。</span><span class="sxs-lookup"><span data-stu-id="f9a35-166">All clients are configured to **only** use WebSockets.</span></span>
  * <span data-ttu-id="f9a35-167">在客户端配置中启用了[SkipNegotiation 设置](xref:signalr/configuration#configure-additional-options)。</span><span class="sxs-lookup"><span data-stu-id="f9a35-167">The [SkipNegotiation setting](xref:signalr/configuration#configure-additional-options) is enabled in the client configuration.</span></span> 
   <span data-ttu-id="f9a35-168">在服务器上启动连接后，连接必须停留在该服务器上。</span><span class="sxs-lookup"><span data-stu-id="f9a35-168">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="f9a35-169">SignalR即使发送的消息太少，应用程序也必须基于客户端数进行扩展。</span><span class="sxs-lookup"><span data-stu-id="f9a35-169">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="f9a35-170">SignalR应用比 web 应用使用的连接资源明显更多 SignalR 。</span><span class="sxs-lookup"><span data-stu-id="f9a35-170">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="iis-limitations-on-windows-client-os"></a><span data-ttu-id="f9a35-171">Windows 客户端操作系统上的 IIS 限制</span><span class="sxs-lookup"><span data-stu-id="f9a35-171">IIS limitations on Windows client OS</span></span>

<span data-ttu-id="f9a35-172">Windows 10 和 Windows 8.x 是客户端操作系统。</span><span class="sxs-lookup"><span data-stu-id="f9a35-172">Windows 10 and Windows 8.x are client operating systems.</span></span> <span data-ttu-id="f9a35-173">客户端操作系统上的 IIS 的并发连接数限制为10个。</span><span class="sxs-lookup"><span data-stu-id="f9a35-173">IIS on client operating systems has a limit of 10 concurrent connections.</span></span> <span data-ttu-id="f9a35-174">SignalR的连接是：</span><span class="sxs-lookup"><span data-stu-id="f9a35-174">SignalR's connections are:</span></span>

* <span data-ttu-id="f9a35-175">暂时性并经常重新建立。</span><span class="sxs-lookup"><span data-stu-id="f9a35-175">Transient and frequently re-established.</span></span>
* <span data-ttu-id="f9a35-176">不再使用时**不会**立即释放。</span><span class="sxs-lookup"><span data-stu-id="f9a35-176">**Not** disposed immediately when no longer used.</span></span>

<span data-ttu-id="f9a35-177">上述情况可能导致在客户端操作系统上达到10个连接限制。</span><span class="sxs-lookup"><span data-stu-id="f9a35-177">The preceding conditions make it likely to hit the 10 connection limit on a client OS.</span></span> <span data-ttu-id="f9a35-178">当客户端操作系统用于开发时，建议：</span><span class="sxs-lookup"><span data-stu-id="f9a35-178">When a client OS is used for development, we recommend:</span></span>

* <span data-ttu-id="f9a35-179">避免 IIS。</span><span class="sxs-lookup"><span data-stu-id="f9a35-179">Avoid IIS.</span></span>
* <span data-ttu-id="f9a35-180">使用 Kestrel 或 IIS Express 作为部署目标。</span><span class="sxs-lookup"><span data-stu-id="f9a35-180">Use Kestrel or IIS Express as deployment targets.</span></span>

## <a name="linux-with-nginx"></a><span data-ttu-id="f9a35-181">Linux 与 Nginx</span><span class="sxs-lookup"><span data-stu-id="f9a35-181">Linux with Nginx</span></span>

<span data-ttu-id="f9a35-182">对于 websocket，请将代理 `Connection` 和 `Upgrade` 标头设置为以下 SignalR 内容：</span><span class="sxs-lookup"><span data-stu-id="f9a35-182">Set the proxy's `Connection` and `Upgrade` headers to the following for SignalR WebSockets:</span></span>

```nginx
proxy_set_header Upgrade $http_upgrade;
proxy_set_header Connection $connection_upgrade;
```

<span data-ttu-id="f9a35-183">有关详细信息，请参阅 [NGINX 作为 WebSocket 代理](https://www.nginx.com/blog/websocket-nginx/)。</span><span class="sxs-lookup"><span data-stu-id="f9a35-183">For more information, see [NGINX as a WebSocket Proxy](https://www.nginx.com/blog/websocket-nginx/).</span></span>

## <a name="third-party-no-locsignalr-backplane-providers"></a><span data-ttu-id="f9a35-184">第三方 SignalR 底板提供程序</span><span class="sxs-lookup"><span data-stu-id="f9a35-184">Third-party SignalR backplane providers</span></span>

* [<span data-ttu-id="f9a35-185">NCache</span><span class="sxs-lookup"><span data-stu-id="f9a35-185">NCache</span></span>](https://www.alachisoft.com/ncache/asp-net-core-signalr.html)
* <span data-ttu-id="f9a35-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span><span class="sxs-lookup"><span data-stu-id="f9a35-186">[Orleans](https://github.com/OrleansContrib/SignalR.Orleans)</span></span>

## <a name="next-steps"></a><span data-ttu-id="f9a35-187">后续步骤</span><span class="sxs-lookup"><span data-stu-id="f9a35-187">Next steps</span></span>

<span data-ttu-id="f9a35-188">有关详细信息，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="f9a35-188">For more information, see the following resources:</span></span>

* [<span data-ttu-id="f9a35-189">Azure SignalR 服务文档</span><span class="sxs-lookup"><span data-stu-id="f9a35-189">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="f9a35-190">设置 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="f9a35-190">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
