---
uid: signalr/overview/performance/scaleout-in-signalr
title: SignalR 的横向扩展简介 |Microsoft Docs
author: bradygaster
description: Visual Studio 2013.NET 4.5 SignalR 本主题中使用软件版本信息有关早期版本的版本 2 的本主题的早期版本...
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 7e781fc1-1c1f-45a8-bc1d-338e96dbe9c9
msc.legacyurl: /signalr/overview/performance/scaleout-in-signalr
msc.type: authoredcontent
ms.openlocfilehash: 60ae0353745284796eb7e0ddb6397ecb48eceaf0
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836192"
---
<a name="introduction-to-scaleout-in-signalr"></a>SignalR 的横向扩展简介
====================
通过[Mike Wasson](https://github.com/MikeWasson)， [Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> ## <a name="software-versions-used-in-this-topic"></a>本主题中使用的软件版本
>
>
> - [Visual Studio 2013](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - .NET 4.5
> - SignalR 版本 2
>
>
>
> ## <a name="previous-versions-of-this-topic"></a>本主题的早期版本
>
> 有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。
>
> ## <a name="questions-and-comments"></a>问题和提出的意见
>
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。


一般情况下，有两种方法来缩放 web 应用程序：*纵向*并*横向扩展*。

- 扩展方法中使用更大的服务器 （或更大的 VM） 更多 RAM、 Cpu，等等。
- 横向扩展意味着添加更多服务器来处理负载。

向上扩展的问题是，您很快就按下计算机的大小限制。 除此之外，您需要向外扩展。但是，当向外扩展，客户端可以将路由到不同的服务器。 连接到一个服务器的客户端不会收到从另一台服务器发送的消息。

![](scaleout-in-signalr/_static/image1.png)

一种解决方案是使用名为的组件的服务器之间转发消息*底板*。 使用启用了基架，每个应用程序实例将消息发送到底板，并底板将其转发到其他应用程序实例。 （在电子设备底板是并行的连接器组。 通过类推，SignalR 基架连接多个服务器。）

![](scaleout-in-signalr/_static/image2.png)

SignalR 目前提供三个背板：

- **Azure 服务总线**。 服务总线是允许组件以松散耦合的方式发送消息的消息传送基础结构。
- **Redis**。 Redis 是内存中键 / 值存储。 Redis 支持发布/订阅 （"发布/订阅"） 模式，用于发送消息。
- **SQL Server**。 SQL Server 底板将消息写入 SQL 表。 基架使用 Service Broker 为有效的消息传递。 但是，它还适用如果未启用 Service Broker。

如果在部署 Azure 上的应用程序，请考虑使用 Redis 底板使用[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。 如果您要部署到服务器场，请考虑 SQL Server 或 Redis 背板。

以下主题包含针对每个基架的分步教程：

- [使用 Azure 服务总线的 SignalR 横向扩展](scaleout-with-windows-azure-service-bus.md)
- [使用 Redis 的 SignalR 横向扩展](scaleout-with-redis.md)
- [使用 SQL Server 的 SignalR 横向扩展](scaleout-with-sql-server.md)

## <a name="implementation"></a>实现

在 SignalR 中，通过消息总线发送每条消息。 消息总线实现[IMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.messaging.imessagebus(v=vs.100).aspx)接口，它提供了发布/订阅抽象。 通过替换默认的工作的底板来**IMessageBus**设计为对该底板总线。 例如，是适用于 Redis 消息总线[RedisMessageBus](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.redis.redismessagebus(v=vs.100).aspx)，并使用 Redis [pub/sub](http://redis.io/topics/pubsub)机制来发送和接收消息。

每个服务器实例连接到总线通过基架。 发送一条消息后，转到基架，并底板将其发送到每个服务器。 当服务器从底板获取一条消息时，它将该消息放在其本地缓存中。 服务器然后将其本地缓存中的消息传送到客户端。

对于每个客户端连接，在读取消息流中的客户端的进度进行跟踪使用游标。 （游标表示的消息流中的位置。）如果客户端断开连接，然后重新连接，它会请求到达后客户端的游标值的任何消息总线。 连接使用时，会发生相同的操作[长轮询](../getting-started/introduction-to-signalr.md#transports)。 长时轮询请求完成后，客户端打开新连接，并请求到达光标位置后的消息。

游标机制的工作即使客户端路由到不同的服务器上重新连接。 底板可以识别的所有服务器和客户端连接到哪个服务器无关紧要。

## <a name="limitations"></a>限制

使用基架，最大消息吞吐量低于客户端直接与单个服务器节点进行通信时，它是。 这是因为底板将转发到每个节点，每条消息，因此底板可能成为瓶颈。 此限制是否是一个问题取决于应用程序。 例如，下面是一些典型的 SignalR 方案：

- [服务器广播](../getting-started/tutorial-server-broadcast-with-signalr.md)（例如，股票行情）：背板适合此方案中，因为服务器控制发送消息的速率。
- [客户端到客户端](../getting-started/tutorial-getting-started-with-signalr.md)（如聊天）：在此方案中，基架可能如果成为瓶颈的消息数会随着客户端; 的数量也就是说，如果消息的速率增长按比例更多客户端加入。
- [高频率实时功能](../getting-started/tutorial-high-frequency-realtime-with-signalr.md)（例如，实时游戏）：基架不建议用于此方案。

## <a name="enabling-tracing-for-signalr-scaleout"></a>为的 SignalR 横向扩展启用跟踪

若要启用的底板来跟踪，请将以下各节添加到 web.config 文件中，根下**配置**元素：

[!code-html[Main](scaleout-in-signalr/samples/sample1.html)]
