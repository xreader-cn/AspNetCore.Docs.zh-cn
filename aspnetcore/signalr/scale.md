---
title: ASP.NET Core SignalR 生产承载和扩展
author: bradygaster
description: 了解如何避免性能和缩放的应用程序使用 ASP.NET Core SignalR 中的问题。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/28/2018
uid: signalr/scale
ms.openlocfilehash: 4ac4509acc89d0091a3757c7cfbc9981614f29ad
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836917"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a><span data-ttu-id="8b7f6-103">ASP.NET Core SignalR 承载和扩展</span><span class="sxs-lookup"><span data-stu-id="8b7f6-103">ASP.NET Core SignalR hosting and scaling</span></span>

<span data-ttu-id="8b7f6-104">通过[Andrew Stanton-nurse](https://twitter.com/anurse)， [Brady Gaster](https://twitter.com/bradygaster)，并[Tom Dykstra](https://github.com/tdykstra)，</span><span class="sxs-lookup"><span data-stu-id="8b7f6-104">By [Andrew Stanton-Nurse](https://twitter.com/anurse), [Brady Gaster](https://twitter.com/bradygaster), and [Tom Dykstra](https://github.com/tdykstra),</span></span>

<span data-ttu-id="8b7f6-105">本文介绍承载和扩展使用 ASP.NET Core SignalR 的高流量应用的注意事项。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-105">This article explains hosting and scaling considerations for high-traffic apps that use ASP.NET Core SignalR.</span></span>

## <a name="tcp-connection-resources"></a><span data-ttu-id="8b7f6-106">TCP 连接资源</span><span class="sxs-lookup"><span data-stu-id="8b7f6-106">TCP connection resources</span></span>

<span data-ttu-id="8b7f6-107">Web 服务器可以支持的并发 TCP 连接数会受到限制。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-107">The number of concurrent TCP connections that a web server can support is limited.</span></span> <span data-ttu-id="8b7f6-108">使用标准 HTTP 客户端*临时*连接。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-108">Standard HTTP clients use *ephemeral* connections.</span></span> <span data-ttu-id="8b7f6-109">当客户端进入空闲状态，并在以后再次打开时，可以关闭这些连接。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-109">These connections can be closed when the client goes idle and reopened later.</span></span> <span data-ttu-id="8b7f6-110">但是，SignalR 连接是*持久*。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-110">On the other hand, a SignalR connection is *persistent*.</span></span> <span data-ttu-id="8b7f6-111">甚至当客户端进入空闲状态时，SignalR 连接保持打开状态。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-111">SignalR connections stay open even when the client goes idle.</span></span> <span data-ttu-id="8b7f6-112">在高流量应用中提供多个客户端，这些持久连接可能会导致服务器达到其最大连接数。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-112">In a high-traffic app that serves many clients, these persistent connections can cause servers to hit their maximum number of connections.</span></span>

<span data-ttu-id="8b7f6-113">持久连接还使用一些额外的内存，则还跟踪每个连接。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-113">Persistent connections also consume some additional memory, to track each connection.</span></span>

<span data-ttu-id="8b7f6-114">大量使用 SignalR 通过与连接相关的资源可能会影响托管在同一台服务器的其他 web 应用。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-114">The heavy use of connection-related resources by SignalR can affect other web apps that are hosted on the same server.</span></span> <span data-ttu-id="8b7f6-115">当 SignalR 打开和保存最后一个可用的 TCP 连接时，在同一服务器上的其他 web 应用还可提供给他们没有更多连接。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-115">When SignalR opens and holds the last available TCP connections, other web apps on the same server also have no more connections available to them.</span></span>

<span data-ttu-id="8b7f6-116">如果服务器的连接，会看到随机套接字错误和连接重置错误。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-116">If a server runs out of connections, you'll see random socket errors and connection reset errors.</span></span> <span data-ttu-id="8b7f6-117">例如：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-117">For example:</span></span>

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

<span data-ttu-id="8b7f6-118">若要防止在其他 web 应用中导致错误 SignalR 资源使用情况，比在其他 web 应用程序的不同服务器上运行 SignalR。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-118">To keep SignalR resource usage from causing errors in other web apps, run SignalR on different servers than your other web apps.</span></span>

<span data-ttu-id="8b7f6-119">若要防止 SignalR 资源使用情况导致出错的 SignalR 应用程序，横向扩展以限制服务器必须处理的连接数。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-119">To keep SignalR resource usage from causing errors in a SignalR app, scale out to limit the number of connections a server has to handle.</span></span>

## <a name="scale-out"></a><span data-ttu-id="8b7f6-120">横向扩展</span><span class="sxs-lookup"><span data-stu-id="8b7f6-120">Scale out</span></span>

<span data-ttu-id="8b7f6-121">使用 SignalR 的应用程序需要跟踪的所有连接，这将创建服务器场的问题。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-121">An app that uses SignalR needs to keep track of all its connections, which creates problems for a server farm.</span></span> <span data-ttu-id="8b7f6-122">添加服务器，并获取其他服务器不了解的内容的新连接。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-122">Add a server, and it gets new connections that the other servers don't know about.</span></span> <span data-ttu-id="8b7f6-123">例如，以下关系图中每个服务器上的 SignalR 是不知道的其他服务器上的连接数。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-123">For example, SignalR on each server in the following diagram is unaware of the connections on the other servers.</span></span> <span data-ttu-id="8b7f6-124">当在一台服务器上的 SignalR 想要将消息发送到所有客户端时，消息将只发送到连接到该服务器的客户端。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-124">When SignalR on one of the servers wants to send a message to all clients, the message only goes to the clients connected to that server.</span></span>

![不带底板缩放 SignalR](scale/_static/scale-no-backplane.png)

<span data-ttu-id="8b7f6-126">解决此问题的选项都[Azure SignalR 服务](#azure-signalr-service)并[Redis 底板](#redis-backplane)。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-126">The options for solving this problem are the [Azure SignalR Service](#azure-signalr-service) and [Redis backplane](#redis-backplane).</span></span>

## <a name="azure-signalr-service"></a><span data-ttu-id="8b7f6-127">Azure SignalR 服务</span><span class="sxs-lookup"><span data-stu-id="8b7f6-127">Azure SignalR Service</span></span>

<span data-ttu-id="8b7f6-128">Azure SignalR 服务是一个代理而不是基架。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-128">The Azure SignalR Service is a proxy rather than a backplane.</span></span> <span data-ttu-id="8b7f6-129">客户端启动连接到服务器，每次客户端将重定向连接到服务。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-129">Each time a client initiates a connection to the server, the client is redirected to connect to the service.</span></span> <span data-ttu-id="8b7f6-130">下图说明了该过程：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-130">That process is illustrated in the following diagram:</span></span>

![建立与 Azure SignalR 服务的连接](scale/_static/azure-signalr-service-one-connection.png)

<span data-ttu-id="8b7f6-132">结果是，该服务管理的所有客户端连接，而每台服务器需要仅小的一定数量的连接到服务，如以下关系图中所示：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-132">The result is that the service manages all of the client connections, while each server needs only a small constant number of connections to the service, as shown in the following diagram:</span></span>

![客户端连接到服务，连接到服务的服务器](scale/_static/azure-signalr-service-multiple-connections.png)

<span data-ttu-id="8b7f6-134">若要横向扩展此方法具有几大优势，Redis 底板替代方法：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-134">This approach to scale-out has several advantages over the Redis backplane alternative:</span></span>

* <span data-ttu-id="8b7f6-135">粘性会话，也称为[客户端相关性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)，不是必需的因为客户端将立即重定向到 Azure SignalR 服务在连接时。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-135">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is not required, because clients are immediately redirected to the Azure SignalR Service when they connect.</span></span>
* <span data-ttu-id="8b7f6-136">应用程序可以向外扩展 SignalR 基于 Azure SignalR 服务可以自动缩放以处理任意数量的连接时发送的消息数。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-136">A SignalR app can scale out based on the number of messages sent, while the Azure SignalR Service automatically scales to handle any number of connections.</span></span> <span data-ttu-id="8b7f6-137">例如，可能有数千个客户端，但如果只发送少量消息数 / 秒，SignalR 应用程序不需要向外扩展到多个服务器只是为了处理连接本身。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-137">For example, there could be thousands of clients, but if only a few messages per second are sent, the SignalR app won't need to scale out to multiple servers just to handle the connections themselves.</span></span>
* <span data-ttu-id="8b7f6-138">SignalR 应用程序不会使用更多的连接资源，而无需 SignalR 的 web 应用比。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-138">A SignalR app won't use significantly more connection resources than a web app without SignalR.</span></span>

<span data-ttu-id="8b7f6-139">出于这些原因，我们建议 Azure SignalR 服务托管在 Azure 中，包括应用服务、 Vm 和容器上的所有 ASP.NET Core SignalR 应用程序。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-139">For these reasons, we recommend the Azure SignalR Service for all ASP.NET Core SignalR apps hosted on Azure, including App Service, VMs, and containers.</span></span>

<span data-ttu-id="8b7f6-140">有关详细信息请参阅[Azure SignalR 服务文档](/azure/azure-signalr/signalr-overview)。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-140">For more information see the [Azure SignalR Service documentation](/azure/azure-signalr/signalr-overview).</span></span>

## <a name="redis-backplane"></a><span data-ttu-id="8b7f6-141">Redis 底板</span><span class="sxs-lookup"><span data-stu-id="8b7f6-141">Redis backplane</span></span>

<span data-ttu-id="8b7f6-142">[Redis](https://redis.io/)是支持发布/订阅模型的消息传送系统的内存中键 / 值存储。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-142">[Redis](https://redis.io/) is an in-memory key-value store that supports a messaging system with a publish/subscribe model.</span></span> <span data-ttu-id="8b7f6-143">Redis 的 SignalR 基架使用发布/订阅功能以将消息转发到其他服务器。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-143">The SignalR Redis backplane uses the pub/sub feature to forward messages to other servers.</span></span> <span data-ttu-id="8b7f6-144">当客户端进行连接时，连接信息传递给基架。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-144">When a client makes a connection, the connection information is passed to the backplane.</span></span> <span data-ttu-id="8b7f6-145">当服务器想要将消息发送到所有客户端时，它将发送到底板。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-145">When a server wants to send a message to all clients, it sends to the backplane.</span></span> <span data-ttu-id="8b7f6-146">底板知道所有连接的客户端和服务器它们是在。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-146">The backplane knows all connected clients and which servers they're on.</span></span> <span data-ttu-id="8b7f6-147">它将消息发送给所有客户端通过其各自的服务器。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-147">It sends the message to all clients via their respective servers.</span></span> <span data-ttu-id="8b7f6-148">此过程是在下图中所示：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-148">This process is illustrated in the following diagram:</span></span>

![Redis 底板，消息从一台服务器发送到所有客户端](scale/_static/redis-backplane.png)

<span data-ttu-id="8b7f6-150">Redis 底板是用于在自己的基础结构上托管的应用建议的向外缩放方法。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-150">The Redis backplane is the recommended scale-out approach for apps hosted on your own infrastructure.</span></span> <span data-ttu-id="8b7f6-151">Azure SignalR 服务并不可行的本地应用，原因是你的数据中心和 Azure 数据中心之间的连接延迟用于生产。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-151">Azure SignalR Service isn't a practical option for production use with on-premises apps due to connection latency between your data center and an Azure data center.</span></span>

<span data-ttu-id="8b7f6-152">前面记下 Azure SignalR 服务的优点是 Redis 底板的缺点：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-152">The Azure SignalR Service advantages noted earlier are disadvantages for the Redis backplane:</span></span>

* <span data-ttu-id="8b7f6-153">粘性会话，也称为[客户端相关性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)，是必需的。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-153">Sticky sessions, also known as [client affinity](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity), is required.</span></span> <span data-ttu-id="8b7f6-154">在服务器上启动的连接，连接后继续使用该服务器。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-154">Once a connection is initiated on a server, the connection has to stay on that server.</span></span>
* <span data-ttu-id="8b7f6-155">SignalR 应用程序必须横向扩展根据的客户端数，即使正发送的几个消息。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-155">A SignalR app must scale out based on number of clients even if few messages are being sent.</span></span>
* <span data-ttu-id="8b7f6-156">SignalR 应用程序使用的更多 web 应用而无需 SignalR 连接资源。</span><span class="sxs-lookup"><span data-stu-id="8b7f6-156">A SignalR app uses significantly more connection resources than a web app without SignalR.</span></span>

## <a name="next-steps"></a><span data-ttu-id="8b7f6-157">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8b7f6-157">Next steps</span></span>

<span data-ttu-id="8b7f6-158">有关更多信息，请参见以下资源：</span><span class="sxs-lookup"><span data-stu-id="8b7f6-158">For more information, see the following resources:</span></span>

* [<span data-ttu-id="8b7f6-159">Azure SignalR 服务文档</span><span class="sxs-lookup"><span data-stu-id="8b7f6-159">Azure SignalR Service documentation</span></span>](/azure/azure-signalr/signalr-overview)
* [<span data-ttu-id="8b7f6-160">设置 Redis 底板</span><span class="sxs-lookup"><span data-stu-id="8b7f6-160">Set up a Redis backplane</span></span>](xref:signalr/redis-backplane)
