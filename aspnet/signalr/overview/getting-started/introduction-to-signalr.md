---
uid: signalr/overview/getting-started/introduction-to-signalr
title: SignalR 简介 |Microsoft Docs
author: bradygaster
description: 本文介绍了 SignalR 是什么，以及一些了要创建的解决方案。
ms.author: bradyg
ms.date: 06/10/2014
ms.assetid: 0fab5e35-8c1f-43d4-8635-b8aba8766a71
msc.legacyurl: /signalr/overview/getting-started/introduction-to-signalr
msc.type: authoredcontent
ms.openlocfilehash: 7b9dae3e5d79319a9fefee41f4525a59f950746a
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837762"
---
<a name="introduction-to-signalr"></a>SignalR 简介
====================

通过[Patrick Fletcher](https://github.com/pfletcher)

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]


> 本文介绍了 SignalR 是什么，以及一些了要创建的解决方案。 
> 
> ## <a name="questions-and-comments"></a>问题和提出的意见
> 
> 请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。 如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](https://stackoverflow.com/questions/tagged/signalr)。

## <a name="what-is-signalr"></a>SignalR 是什么？

ASP.NET SignalR 是一个面向 ASP.NET 开发人员的库，可简化将实时 web 功能添加到应用程序的过程。 实时 web 功能是让服务器代码将内容推送到连接的客户端立即可用，而不是让服务器等待客户端请求新数据的能力。

SignalR 可用于将任何类型的"实时"web 功能添加到 ASP.NET 应用程序。 尽管聊天通常用作示例，您可以更为很多。 只要用户刷新 web 页面以查看新数据或页面实现[长轮询](http://en.wikipedia.org/wiki/Push_technology#Long_polling)若要检索新数据，可以考虑对它使用 SignalR。 示例包括仪表板和监视应用程序，协作应用程序 （如同时进行编辑的文档） 时，作业的进度更新，并实时窗体。

SignalR 还能让全新类型的 web 应用程序需要高频率更新从服务器中，例如，实时游戏。

SignalR 提供一个简单的 API，用于创建从服务器端.NET 代码中调用 JavaScript 函数在客户端浏览器 （和其他客户端平台） 的服务器到客户端的远程过程调用 (RPC)。 SignalR 还包括连接管理的 API （例如，连接和断开连接事件），并对连接进行分组。

![使用 signalr 实现调用方法](introduction-to-signalr/_static/image1.png)

SignalR 自动处理的连接管理，并允许您将消息广播到所有连接的客户端同时，如聊天室。 您还可以将消息发送到特定的客户端。 客户端和服务器之间的连接是持久性的与不同的是经典的 HTTP 连接，这是重新建立的每个通信。

SignalR 支持"服务器推送"功能，在其中服务器代码可以调用远程过程调用 (RPC)，而不是常见请求-响应模式目前上使用 web 浏览器中的客户端代码。

SignalR 应用程序可以横向扩展到数千个客户端使用服务总线、 SQL Server 或[Redis](http://redis.io)。

SignalR 是开放源代码，可通过访问[GitHub](https://github.com/signalr)。

## <a name="signalr-and-websocket"></a>SignalR 和 WebSocket

SignalR 使用新的 WebSocket 传输 （如果有），并在必要时回退到较旧的传输。 尽管您当然可以编写您的应用程序直接使用 WebSocket，并使用 SignalR 意味着已为你自动完成了大量的额外功能需要实现。 最重要的是，这意味着您可以编写代码应用程序以充分利用 WebSocket 而无需担心如何为旧版客户端创建单独的代码路径。 SignalR 还使你免受无需担心对 WebSocket，更新，因为 SignalR 将更新以支持基础传输中的更改的 WebSocket 版本间提供你的应用程序一致的接口。

<a id="transports"></a>

## <a name="transports-and-fallbacks"></a>传输和回退

SignalR 是一种抽象，通过某些要求进行客户端和服务器之间的实时工作的传输。 SignalR 连接作为 HTTP，启动，然后升级到 WebSocket 连接是否可用。 WebSocket 是 SignalR 的理想之选传输，因为它使服务器内存的最有效地使用，具有最低的延迟，并且具有最基本功能 （如完整双工客户端和服务器之间的通信），但它还具有最严格要求：WebSocket 要求要使用 Windows Server 2012 或 Windows 8 和.NET Framework 4.5 的服务器。 如果不满足这些要求，SignalR 将尝试使用其他传输以其连接。

### <a name="html-5-transports"></a>HTML 5 传输

这些传输协议依赖于支持[HTML 5](http://en.wikipedia.org/wiki/HTML5)。 如果客户端浏览器不支持 HTML 5 标准，将使用较旧的传输。

- **WebSocket** (如果服务器和浏览器指示它们可以支持 Websocket)。 WebSocket 是建立的则返回 true 的持久的双向连接客户端和服务器之间的唯一传输。 但是，WebSocket 还具有最严格的要求;它完全支持仅在最新版本的 Microsoft Internet Explorer、 Google Chrome 和 Mozilla Firefox 和 Opera 和 Safari 等其他浏览器中只有部分实现。
- **服务器发送事件**，也称为 EventSource （如果在浏览器支持服务器发送事件，它基本上是除 Internet Explorer 的所有浏览器）。

### <a name="comet-transports"></a>Comet 传输

基于以下传输[Comet](http://en.wikipedia.org/wiki/Comet_(programming)) web 应用程序模型中，浏览器或其他客户端维护服务器可以用于将数据推送到客户端不使用客户端专门的长时间持有 HTTP 请求请求它。

- **永久帧**（适用于 Internet Explorer)。 永久帧创建隐藏的 IFrame 的未完成的服务器上的终结点向发出请求。 服务器然后不断会脚本发送到客户端立即执行，提供从服务器到客户端的单向实时连接。 从客户端与服务器的连接使用单独的连接从服务器到客户端连接和像标准的 HTTP 请求，为每个条需要发送的数据创建新的连接。
- **Ajax 长轮询**。 长轮询不创建持久性连接，但改为轮询具有保持打开状态直到服务器做出响应，此时将关闭连接，并立即请求新的连接的请求的服务器。 连接重置时，这可能会造成一些延迟。

有关哪些传输受支持的配置下的详细信息，请参阅[支持的平台](supported-platforms.md)。

### <a name="transport-selection-process"></a>传输选择过程

以下列表显示 SignalR 使用来确定使用哪个传输的步骤。

1. 如果浏览器是 Internet Explorer 8 或更早版本，则使用长轮询。
2. 如果配置 JSONP (即`jsonp`参数设置为`true`启动连接时)，使用长轮询。
3. 如果操作正在进行的跨域连接，（即，如果 SignalR 终结点不在托管的页面所在的域中），然后 WebSocket 将在满足以下条件：

   - 客户端支持 CORS （跨域资源共享）。 在其的客户端支持 CORS 的详细信息，请参阅[CORS 在 caniuse.com](http://www.caniuse.com/CORS)。
   - 客户端支持 WebSocket
   - 服务器支持 WebSocket

     如果不满足任何这些条件，则将使用长轮询。 有关跨域连接的详细信息，请参阅[如何建立跨域连接](../guide-to-the-api/hubs-api-guide-javascript-client.md#crossdomain)。
4. 如果未配置 JSONP 并且连接不跨域，如果客户端和服务器支持它，则将使用 WebSocket。
5. 如果客户端或服务器不支持 WebSocket，如果可用，则使用服务器发送事件。
6. 如果服务器发送事件不可用，请尝试使用永久帧。
7. 如果永久帧失败，则使用长轮询。

<a id="MonitoringTransports"></a>
### <a name="monitoring-transports"></a>监视传输

您可以确定应用程序在中心上的日志记录和在浏览器中打开控制台窗口，从而使用什么传输。

若要启用浏览器中的中心的事件日志记录，客户端应用程序中添加以下命令：

`$.connection.hub.logging = true;`

- 在 Internet Explorer 中，通过按 F12 打开开发人员工具，并单击控制台选项卡。

    ![在 Microsoft Internet 资源管理器控制台](introduction-to-signalr/_static/image2.png)
- 在 Chrome 中，通过按 Ctrl + Shift + J 打开控制台。

    ![在 Google Chrome 中的控制台](introduction-to-signalr/_static/image3.png)

控制台打开和启用日志记录后，你可以查看 SignalR 正在使用哪种传输。

![控制台中显示 WebSocket 传输的 Internet 资源管理器](introduction-to-signalr/_static/image4.png)

### <a name="specifying-a-transport"></a>指定在传输协议

协商传输只占用一定的时间和客户端/服务器的资源。 如果已知的客户端功能，则传输可以指定启动客户端连接时。 下面的代码段演示如何启动使用 Ajax 长轮询传输方式，因为如果它已知客户端不支持任何其他协议将使用的连接：

`connection.start({ transport: 'longPolling' });`

如果你想要尝试按顺序的特定传输的客户端，可以指定回退的顺序。 下面的代码段演示尝试 WebSocket，并且如果没有问题，，直接转到长轮询。

`connection.start({ transport: ['webSockets','longPolling'] });`

用于指定传输中的字符串常数定义，如下所示：

- `webSockets`
- `foreverFrame`
- `serverSentEvents`
- `longPolling`

## <a name="connections-and-hubs"></a>连接和集线器

SignalR API 包含客户端和服务器之间进行通信的两个模型：持久连接和集线器。

连接表示用于发送单个收件人、 分组或广播消息的简单终结点。 开发人员直接访问 SignalR 公开低级别通信协议 （由 PersistentConnection 类的.NET 代码中表示） 的持久性连接 API 提供。 使用连接的通信模型都会熟悉的开发人员已使用基于连接的 Api，如 Windows Communication Foundation。

中心是基于连接 API，从而使您的客户端和服务器直接相互调用方法的更多高级管道。 SignalR 处理跨计算机界限调度像变魔术一样，是通过允许客户端在服务器上调用方法，以轻松地为本地方法，反之亦然。 使用中心的通信模型都会熟悉的开发人员已使用远程调用 Api，如.NET 远程处理。 使用中心还允许您将强类型化的参数传递给方法，使模型绑定。

### <a name="architecture-diagram"></a>体系结构关系图

下图显示了中心、 永久连接和用于传输的基础技术之间的关系。

![显示 Api、 传输和客户端 SignalR 体系结构图示](introduction-to-signalr/_static/image5.png)

### <a name="how-hubs-work"></a>中心的工作原理

服务器端代码在客户端上调用的方法时发送一个数据包，在活动传输包含名称和要调用的方法的参数 （作为方法参数发送一个对象，它使用 JSON 序列化）。 然后，客户端匹配到客户端代码中定义的方法的方法名称。 如果没有匹配项，将使用反序列化的参数数据执行客户端方法。

可以使用像这样的工具监视方法调用[Fiddler。](http://fiddler2.com/) 下图显示了从 SignalR 服务器发送到 web 浏览器客户端在 Fiddler 日志窗格中的方法调用。 方法调用从集线器调用发送`MoveShapeHub`，并正在调用的方法称为`updateShape`。

![显示 SignalR 流量的 Fiddler 日志的视图](introduction-to-signalr/_static/image6.png)

在此示例中，中心名称标识与`H`参数; 方法名称标识`M`参数，并发送到该方法的数据标识`A`参数。 生成此消息的应用程序中创建[高频率实时功能](tutorial-high-frequency-realtime-with-signalr.md)教程。

### <a name="choosing-a-communication-model"></a>选择通信模型

大多数应用程序应使用中心 API。 可在以下情况下使用连接 API:

- 需要指定将发送的实际消息的格式。
- 开发人员倾向于使用消息传送与调度模型而不是远程调用模型。
- 使用消息传送模型的现有应用程序移植以使用 SignalR。
