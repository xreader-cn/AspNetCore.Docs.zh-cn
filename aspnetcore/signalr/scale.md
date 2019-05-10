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
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64895074"
---
# <a name="aspnet-core-signalr-hosting-and-scaling"></a>ASP.NET Core SignalR 承载和扩展

通过[Andrew Stanton-nurse](https://twitter.com/anurse)， [Brady Gaster](https://twitter.com/bradygaster)，并[Tom Dykstra](https://github.com/tdykstra)，

本文介绍承载和扩展使用 ASP.NET Core SignalR 的高流量应用的注意事项。

## <a name="tcp-connection-resources"></a>TCP 连接资源

Web 服务器可以支持的并发 TCP 连接数会受到限制。 使用标准 HTTP 客户端*临时*连接。 当客户端进入空闲状态，并在以后再次打开时，可以关闭这些连接。 但是，SignalR 连接是*持久*。 甚至当客户端进入空闲状态时，SignalR 连接保持打开状态。 在高流量应用中提供多个客户端，这些持久连接可能会导致服务器达到其最大连接数。

持久连接还使用一些额外的内存，则还跟踪每个连接。

大量使用 SignalR 通过与连接相关的资源可能会影响托管在同一台服务器的其他 web 应用。 当 SignalR 打开和保存最后一个可用的 TCP 连接时，在同一服务器上的其他 web 应用还可提供给他们没有更多连接。

如果服务器的连接，会看到随机套接字错误和连接重置错误。 例如：

```
An attempt was made to access a socket in a way forbidden by its access permissions...
```

若要防止在其他 web 应用中导致错误 SignalR 资源使用情况，比在其他 web 应用程序的不同服务器上运行 SignalR。

若要防止 SignalR 资源使用情况导致出错的 SignalR 应用程序，横向扩展以限制服务器必须处理的连接数。

## <a name="scale-out"></a>横向扩展

使用 SignalR 的应用程序需要跟踪的所有连接，这将创建服务器场的问题。 添加服务器，并获取其他服务器不了解的内容的新连接。 例如，以下关系图中每个服务器上的 SignalR 是不知道的其他服务器上的连接数。 当在一台服务器上的 SignalR 想要将消息发送到所有客户端时，消息将只发送到连接到该服务器的客户端。

![不带底板缩放 SignalR](scale/_static/scale-no-backplane.png)

解决此问题的选项都[Azure SignalR 服务](#azure-signalr-service)并[Redis 底板](#redis-backplane)。

## <a name="azure-signalr-service"></a>Azure SignalR 服务

Azure SignalR 服务是一个代理而不是基架。 客户端启动连接到服务器，每次客户端将重定向连接到服务。 下图说明了该过程：

![建立与 Azure SignalR 服务的连接](scale/_static/azure-signalr-service-one-connection.png)

结果是，该服务管理的所有客户端连接，而每台服务器需要仅小的一定数量的连接到服务，如以下关系图中所示：

![客户端连接到服务，连接到服务的服务器](scale/_static/azure-signalr-service-multiple-connections.png)

若要横向扩展此方法具有几大优势，Redis 底板替代方法：

* 粘性会话，也称为[客户端相关性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)，不是必需的因为客户端将立即重定向到 Azure SignalR 服务在连接时。
* 应用程序可以向外扩展 SignalR 基于 Azure SignalR 服务可以自动缩放以处理任意数量的连接时发送的消息数。 例如，可能有数千个客户端，但如果只发送少量消息数 / 秒，SignalR 应用程序不需要向外扩展到多个服务器只是为了处理连接本身。
* SignalR 应用程序不会使用更多的连接资源，而无需 SignalR 的 web 应用比。

出于这些原因，我们建议 Azure SignalR 服务托管在 Azure 中，包括应用服务、 Vm 和容器上的所有 ASP.NET Core SignalR 应用程序。

有关详细信息请参阅[Azure SignalR 服务文档](/azure/azure-signalr/signalr-overview)。

## <a name="redis-backplane"></a>Redis 底板

[Redis](https://redis.io/)是支持发布/订阅模型的消息传送系统的内存中键 / 值存储。 Redis 的 SignalR 基架使用发布/订阅功能以将消息转发到其他服务器。 当客户端进行连接时，连接信息传递给基架。 当服务器想要将消息发送到所有客户端时，它将发送到底板。 底板知道所有连接的客户端和服务器它们是在。 它将消息发送给所有客户端通过其各自的服务器。 此过程是在下图中所示：

![Redis 底板，消息从一台服务器发送到所有客户端](scale/_static/redis-backplane.png)

Redis 底板是用于在自己的基础结构上托管的应用建议的向外缩放方法。 Azure SignalR 服务并不可行的本地应用，原因是你的数据中心和 Azure 数据中心之间的连接延迟用于生产。

前面记下 Azure SignalR 服务的优点是 Redis 底板的缺点：

* 粘性会话，也称为[客户端相关性](/iis/extensions/configuring-application-request-routing-arr/http-load-balancing-using-application-request-routing#step-3---configure-client-affinity)，是必需的。 在服务器上启动的连接，连接后继续使用该服务器。
* SignalR 应用程序必须横向扩展根据的客户端数，即使正发送的几个消息。
* SignalR 应用程序使用的更多 web 应用而无需 SignalR 连接资源。

## <a name="next-steps"></a>后续步骤

有关更多信息，请参见以下资源：

* [Azure SignalR 服务文档](/azure/azure-signalr/signalr-overview)
* [设置 Redis 底板](xref:signalr/redis-backplane)
