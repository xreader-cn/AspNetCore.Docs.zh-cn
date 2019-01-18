---
uid: signalr/overview/guide-to-the-api/handling-connection-lifetime-events
title: 了解和处理 SignalR 中的连接生存期事件 |Microsoft Docs
author: pfletcher
description: 本文介绍如何使用事件中心 API 公开的。
ms.author: riande
ms.date: 01/15/2019
ms.assetid: 03960de2-8d95-4444-9169-4426dcc64913
msc.legacyurl: /signalr/overview/guide-to-the-api/handling-connection-lifetime-events
msc.type: authoredcontent
ms.openlocfilehash: 5aaec6795f714ba0877ec3b22eff45eb281b44aa
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396163"
---
<a name="understanding-and-handling-connection-lifetime-events-in-signalr"></a>了解和处理 SignalR 中的连接生存期事件
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> 本文概述了可以处理的 SignalR 连接、 重新连接和断开连接事件以及可以配置的超时和保持连接设置。
>
> 本文假设已有的 SignalR 和连接生存期事件一些知识。 有关 SignalR 的简介，请参阅[SignalR 简介](../getting-started/introduction-to-signalr.md)。 有关连接生存期事件的列表，请参阅以下资源：
>
> - [如何处理中心类中的连接生存期事件](hubs-api-guide-server.md#connectionlifetime)
> - [如何处理在 JavaScript 客户端的连接生存期事件](hubs-api-guide-javascript-client.md#connectionlifetime)
> - [如何处理在.NET 客户端的连接生存期事件](hubs-api-guide-net-client.md#connectionlifetime)
>
> ## <a name="software-versions-used-in-this-topic"></a>本主题中使用的软件版本
>
>
> - [Visual Studio 2017](https://visualstudio.microsoft.com/downloads/)
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

## <a name="overview"></a>概述

本文包含以下各节：

- [连接生存期术语和方案](#terminology)

    - [SignalR 连接、 传输连接和物理连接](#signalrvstransport)
    - [传输断开连接方案](#transportdisconnect)
    - [客户端断开连接方案](#clientdisconnect)
    - [服务器断开连接方案](#serverdisconnect)
- [超时和 keepalive 设置](#timeoutkeepalive)

    - [ConnectionTimeout](#connectiontimeout)
    - [DisconnectTimeout](#disconnecttimeout)
    - [KeepAlive](#keepalive)
    - [如何更改超时设置和 keepalive 设置](#changetimeout)
- [如何通知用户有关的断开连接](#notifydisconnect)
- [如何持续重新连接](#continuousreconnect)
- [如何断开连接在服务器代码中的客户端](#disconnectclientfromserver)
- [检测断开连接的原因](#detectingreasonfordisconnection)

API 参考主题的链接将指向.NET 4.5 版本的 API。 如果使用的.NET 4，请参阅[API 主题的.NET 4 版本](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx)。

<a id="terminology"></a>

## <a name="connection-lifetime-terminology-and-scenarios"></a>连接生存期术语和方案

`OnReconnected` SignalR Hub 中的事件处理程序可以执行紧`OnConnected`但不是在`OnDisconnected`给定客户端。 你可以重新连接，而无需断开连接的原因是，有几种方法在 SignalR 中使用单词"连接"。

<a id="signalrvstransport"></a>

### <a name="signalr-connections-transport-connections-and-physical-connections"></a>SignalR 连接、 传输连接和物理连接

本文将区分*SignalR 连接*，*传输连接*，并*物理连接*:

- **SignalR 连接**指的是客户端和服务器 URL，由 SignalR API 维护并由连接 ID 唯一标识之间的逻辑关系 有关此关系的数据由 SignalR 维护，用于建立传输连接。 数据的客户端调用时释放的关系端和 SignalR `Stop` SignalR 尝试重新建立丢失的传输连接时达到方法或超时限制。
- **传输连接**指的是客户端和维护四个传输 Api 之一的服务器之间的逻辑关系：Websocket、 服务器发送事件、 永久帧或很长的轮询。 SignalR 使用传输 API 以创建传输的连接，并传输 API 取决于要创建传输连接的物理网络连接存在。 传输连接结束时 SignalR 将终止该进程，或当传输 API 检测到物理连接已断开。
- **物理连接**指的是物理网络链接--电线，无线信号，路由器，等等-，便于您的客户端计算机和服务器计算机之间的通信。 物理连接必须存在才能建立传输连接，并且必须建立的 SignalR 连接建立的传输连接。 但是，重大的物理连接并不一定会立即结束传输连接或 SignalR 连接，如本主题稍后将对此进行解释。

在下图中，SignalR 连接由中心 API 和 PersistentConnection API SignalR 层、 由传输层，表示传输连接和服务器之间的直线表示的物理连接和的客户端。

![SignalR 体系结构关系图](handling-connection-lifetime-events/_static/image1.png)

当您调用`Start`SignalR 客户端中的方法，您将向 SignalR 客户端代码提供建立物理连接到服务器所需的所有信息。 SignalR 客户端代码使用此信息来发出 HTTP 请求，并建立使用四种传输方法之一的物理连接。 如果传输连接失败或服务器出现故障，SignalR 连接不会立即消失因客户端仍有自动重新建立新的传输连接到相同的 SignalR URL 所需的信息。 在此方案中，涉及到从用户应用程序无需干预，并当 SignalR 客户端代码可创建新的传输连接，它不会启动新的 SignalR 连接。 SignalR 连接的连续性反映在这一事实，在调用时创建的连接 ID`Start`方法，不会更改。

`OnReconnected`集线器上的事件处理程序执行至今已有丢失后自动重新建立传输连接时。 `OnDisconnected` SignalR 连接结束时执行事件处理程序。 SignalR 连接可以结束任何以下方法：

- 如果客户端调用`Stop`方法，一条停止消息发送到服务器，并且客户端和服务器立即结束 SignalR 连接。
- 客户端和服务器之间的连接丢失后，客户端将尝试重新连接和服务器将等待客户端重新连接。 如果尝试重新连接均不成功，并且在断开连接超时期限结束，客户端和服务器端 SignalR 连接。 客户端停止尝试重新连接，并在服务器释放的 SignalR 连接表示形式。
- 如果客户端停止运行而无需调用有机会`Stop`方法中，服务器将等待客户端重新连接，然后结束后断开连接超时期限的 SignalR 连接。
- 如果服务器停止运行，客户端尝试重新连接 （重新创建传输连接），然后结束后断开连接超时期限的 SignalR 连接。

当不存在连接问题，以及用户应用程序通过调用结束 SignalR 连接`Stop`方法、 SignalR 连接和传输连接开始和结束大约在同一时间。 以下部分更详细地介绍了其他方案。

<a id="transportdisconnect"></a>

### <a name="transport-disconnection-scenarios"></a>传输断开连接方案

物理连接可能会较慢，或者可能有连接中断。 具体取决于因素，例如中断的长度，可能会删除该传输连接。 SignalR 然后尝试重新建立传输连接。 有时，传输连接 API 检测到发生中断，并且删除传输连接和 SignalR 发现立即的连接已丢失。 在其他情况下，既不传输连接 API，也不 SignalR 注意将立即变为的连接已丢失。 对于除长轮询的所有传输，SignalR 客户端使用名为的函数*keepalive*来检查传输 API 是无法检测到的连接丢失。 长轮询连接有关的信息，请参阅[超时设置和 keepalive 设置](#timeoutkeepalive)本主题中更高版本。

当连接处于非活动状态时，定期服务器会将保持连接数据包发送到客户端。 截至正在撰写这篇文章，默认频率为每隔 10 秒。 通过侦听这些数据包，客户端可以判断是否存在连接问题。 如果在保持连接数据包未收到预期时，一段时间之后客户端会认为有连接问题，例如速度缓慢问题或中断。 如果在较长的时间后仍未收到保持连接，客户端会认为已删除该连接，并开始尝试重新连接。

下图说明了物理连接不会立即识别传输 API 的问题时引发在典型方案中的客户端和服务器事件。 在关系图适用于下列情况：

- 传输协议是 Websocket、 永久帧或服务器发送事件。
- 有几个不同的物理网络连接中断。
- 传输 API 不会成为了解中断，因此 SignalR 依赖于 keepalive 功能以进行检测。

![传输的断开连接](handling-connection-lifetime-events/_static/image2.png)

如果客户端将进入重新连接模式，但不能建立传输连接在断开连接超时限制内的，在被服务器终止 SignalR 连接。 在这种情况下，服务器执行的中心`OnDisconnected`方法，并将在客户端管理连接更高版本的情况下发送到客户端断开连接消息的队列。 如果客户端然后 does 重新连接，接收断开连接命令，并调用`Stop`方法。 在此方案中，`OnReconnected`时客户端重新连接，不执行并`OnDisconnected`客户端调用时，不执行`Stop`。 下图说明了这种情况。

![传输中断的服务器超时](handling-connection-lifetime-events/_static/image3.png)

可能会在客户端引发的 SignalR 连接生存期事件如下所示：

- `ConnectionSlow` 客户端事件。

    保持连接超时期限的预设的组成部分已通过自最后一条消息，或收到 keepalive ping 时引发。 默认 keepalive 超时警告期为 2/3 的保持连接超时。 保持连接超时是 20 秒，因此会在大约 13 秒出现的警告。

    默认情况下，则服务器将 keepalive ping 每隔 10 秒，则客户端检查 keepalive ping 有关 （一个保持连接超时值和 keepalive 超时警告值之间的差异的第三） 每隔 2 秒。

    如果传输 API 会知道这一断开连接，SignalR 可能通知的断开连接之前保持连接的超时警告时间为止。 在这种情况下，`ConnectionSlow`不会引发事件，并 SignalR 将直接转到`Reconnecting`事件。
- `Reconnecting` 客户端事件。

    当 API （a） 的传输检测到的连接已丢失，或 （b） 保持连接超时时间已通过自最后一条消息，或收到 keepalive ping 时引发。 SignalR 客户端代码先尝试重新连接。 如果你希望应用程序采取某些操作传输连接丢失时，可以处理此事件。 当前，默认 keepalive 超时时间为 20 秒。

    如果客户端代码尝试在 SignalR 中重新连接模式下调用集线器方法，SignalR 将尝试发送该命令。 大多数情况下，此类尝试都将失败，但在某些情况下它们可能会成功。 对于服务器发送事件、 永久帧和长轮询传输，SignalR 使用两个通信通道，一个客户端用于发送消息，另一个用于接收消息。 用于接收的通道是永久打开，并且这是一个物理连接被中断时关闭的。 通道用于发送仍然可用，因此如果还原的物理连接，则重新建立接收通道之前，从客户端对服务器的方法调用可能会成功。 将接收返回的值，直到 SignalR 重新打开用于接收的通道。
- `Reconnected` 客户端事件。

    当重新建立传输连接时引发。 `OnReconnected`中心中的事件处理程序执行。
- `Closed` 客户端事件 (`disconnected`在 JavaScript 中的事件)。

    当断开连接超时期限过期时 SignalR 客户端代码尝试传输连接断开后重新连接时引发。 断开连接的默认超时为 30 秒。 (由于连接而终止时，也会引发此事件`Stop`调用方法。)

通过传输 API 未检测到并不延迟接收来自服务器的时间超过保持连接超时警告期 keepalive ping 的传输连接中断问题可能不会导致任何连接生存期事件被引发。

某些网络环境中有意关闭空闲连接，并保持连接数据包的另一个功能是帮助防止这让这些网络知道 SignalR 连接正在使用的。 在极端情况下的 keepalive ping 默认频率可能不足以防止关闭的连接。 在这种情况下，你可以配置 keepalive ping 更频繁地发送。 有关详细信息，请参阅[超时设置和 keepalive 设置](#timeoutkeepalive)本主题中更高版本。

> [!NOTE]
>
> **重要说明**：此处所述的事件的顺序不能保证。 SignalR 使每次尝试此方案中，根据不可预测的方式引发连接生存期事件，但有许多不同的网络事件以及基础通信框架，如传输 Api 处理它们的各种方法。 例如，`Reconnected`可能无法引发事件，当客户端重新连接，或`OnConnected`当尝试建立的连接不成功时，可能会运行在服务器上的处理程序。 本主题介绍的通常会生成某些典型的情况下的影响。


<a id="clientdisconnect"></a>

### <a name="client-disconnection-scenarios"></a>客户端断开连接方案

在浏览器客户端中维护的 SignalR 连接的 SignalR 客户端代码在网页 JavaScript 上下文中运行。 具有为什么 SignalR 连接结束时从一个导航页到另一个，并且的为什么你有多个连接具有多个连接 Id 如果要连接的多个浏览器窗口或选项卡。 当用户关闭浏览器窗口或选项卡上，或导航到新页或刷新页面时，SignalR 连接立即结束因为 SignalR 客户端代码会为您和调用处理该浏览器事件`Stop`方法。 在这些情况下，或当应用程序调用任何客户端平台`Stop`方法，`OnDisconnected`服务器上立即执行的事件处理程序和客户端引发`Closed`事件 (事件名为`disconnected`中JavaScript)。

如果客户端应用程序或它所在的计算机崩溃或进入睡眠状态 （例如，当用户关闭便携式计算机），服务器不会知道有关发生了什么情况。 就服务器就知道，丢失的客户端可能是由于连接中断，客户端可能会尝试重新连接。 因此，在这些情况下，服务器会等待以使客户端重新连接，有机会和`OnDisconnected`断开连接超时期限过期 （默认情况下大约 30 秒），才会执行。 下图说明了这种情况。

![客户端计算机故障](handling-connection-lifetime-events/_static/image4.png)

<a id="serverdisconnect"></a>

### <a name="server-disconnection-scenarios"></a>服务器断开连接方案

当服务器处于脱机状态-它重新启动，出现故障，应用程序域回收，等等-结果可能是类似于连接断开，或传输 API 和 SignalR 可能会立即知道该服务器将消失，而 SignalR 可能开始尝试而无需重新连接引发`ConnectionSlow`事件。 如果客户端将进入重新连接模式，并且服务器将恢复或重新启动或新的服务器联机的断开连接超时期限过期之前，客户端将重新连接到已还原的或新服务器。 在这种情况下，在客户端将继续 SignalR 连接和`Reconnected`引发事件。 在第一个服务器上，`OnDisconnected`永远不会执行，并在新服务器上，`OnReconnected`尽管执行`OnConnected`永远不会为该客户端之前该服务器上执行。 （效果是相同，则客户端后重新连接到同一台服务器重新启动或应用程序域回收，因为当服务器重新启动它具有以前的连接活动的任何内存。）以下关系图假设，立即传输 API 会知道这一连接丢失，因此`ConnectionSlow`不会引发事件。

![服务器故障和重新连接](handling-connection-lifetime-events/_static/image5.png)

如果服务器不会成为可用在断开连接超时期限内，SignalR 连接结束。 在此方案中，`Closed`事件 (`disconnected`在 JavaScript 客户端) 在客户端上引发但`OnDisconnected`从来不是在服务器上。 以下关系图假设传输 API 不会不将断开连接的注意，因此它检测到 SignalR keepalive 功能和`ConnectionSlow`引发事件。

![服务器故障和超时](handling-connection-lifetime-events/_static/image6.png)

<a id="timeoutkeepalive"></a>

## <a name="timeout-and-keepalive-settings"></a>超时和 keepalive 设置

默认值`ConnectionTimeout`， `DisconnectTimeout`，和`KeepAlive`值适用于大多数方案，但如果你的环境有特殊需求，可以更改。 例如，如果您的网络环境将关闭 5 秒内处于空闲状态的连接，您可能需要减少的 keepalive 值。

<a id="connectiontimeout"></a>

### <a name="connectiontimeout"></a>ConnectionTimeout

此设置表示打开并正在等待响应之前关闭和打开新的连接保持状态的传输连接的时间量。 默认值为 110 秒。

此设置适用仅 keepalive 功能禁用时，这通常仅适用于长时间轮询传输。 下图说明了此设置对长时间的影响轮询传输连接。

![长轮询传输连接](handling-connection-lifetime-events/_static/image7.png)

<a id="disconnecttimeout"></a>

### <a name="disconnecttimeout"></a>DisconnectTimeout

此设置表示的时间之后传输连接丢失引发之前要等待`Disconnected`事件。 默认值为 30 秒。 当您将设置`DisconnectTimeout`，`KeepAlive`将自动设置为 1/3 的`DisconnectTimeout`值。

<a id="keepalive"></a>

### <a name="keepalive"></a>KeepAlive

此设置表示要在通过空闲连接发送保持连接数据包之前等待时间的量。 默认值为 10 秒。 此值不能超过 1/3 的`DisconnectTimeout`值。

如果你想要同时设置`DisconnectTimeout`并`KeepAlive`，请设置`KeepAlive`后`DisconnectTimeout`。 否则为你`KeepAlive`设置时将覆盖`DisconnectTimeout`会自动设置`KeepAlive`为 1/3 的超时值。

如果你想要禁用 keepalive 功能，设置`KeepAlive`为 null。 Keepalive 功能会自动禁用用于长时间轮询传输。

<a id="changetimeout"></a>

### <a name="how-to-change-timeout-and-keepalive-settings"></a>如何更改超时设置和 keepalive 设置

若要更改这些设置的默认值，请将它们设置`Application_Start`在您*Global.asax*文件，在下面的示例所示。 示例代码所示的值是默认值相同。

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample1.cs)]

<a id="notifydisconnect"></a>

## <a name="how-to-notify-the-user-about-disconnections"></a>如何通知用户有关的断开连接

在某些应用程序可能想要连接问题时向用户显示一条消息。 有几种方法，何时执行此操作。 下面的代码示例适用于 JavaScript 客户端使用生成的代理。

- 处理`connectionSlow`事件之前，它将进入重新连接模式下，SignalR 是识别连接问题时，就立即显示一条消息。

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample2.js)]
- 处理`reconnecting`SignalR 已意识到断开连接并且转到重新连接模式时显示一条消息的事件。

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample3.js)]
- 处理`disconnected`事件，以显示一条消息时尝试重新连接已超时。在此方案中，重新建立与服务器的连接再次的唯一方法是通过调用重新启动的 SignalR 连接`Start`方法，将创建一个新的连接 id。 下面的代码示例使用一个标志来确保仅后重新连接超时，不通过调用导致的 SignalR 连接到正常结束后的通知颁发`Stop`方法。

    [!code-javascript[Main](handling-connection-lifetime-events/samples/sample4.js)]

<a id="continuousreconnect"></a>

## <a name="how-to-continuously-reconnect"></a>如何持续重新连接

在某些应用程序可能想要自动在已丢失，并尝试重新连接已超时后重新建立连接。若要执行此操作，可以调用`Start`方法从您`Closed`事件处理程序 (`disconnected` JavaScript 客户端上的事件处理程序)。 可能需要等待一段时间，然后才能调用`Start`以避免执行此操作太频繁的服务器或物理连接不可用时。 下面的代码示例是 JavaScript 客户端使用生成的代理。

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample5.js)]

需要注意的移动客户端中的潜在问题是连续的重新连接尝试的服务器或物理连接不可用时可能会导致不必要的电池耗尽。

<a id="disconnectclientfromserver"></a>

## <a name="how-to-disconnect-a-client-in-server-code"></a>如何断开连接在服务器代码中的客户端

SignalR 2 版本不具有用于客户端断开连接的内置服务器 API。 有[计划在将来添加此功能](https://github.com/SignalR/SignalR/issues/2101)。 在当前的 SignalR 版本中，从服务器中断开客户端的最简单方法是在客户端上实现 disconnect 方法和从服务器调用该方法。 下面的代码示例显示使用生成的代理的 JavaScript 客户端断开连接方法。

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample6.js)]

> [!WARNING]
> 安全性-既不此方法对于客户端断开连接，也不建议内置 API 将会解决受到攻击的客户端运行的恶意代码，因为客户端无法重新连接或受到攻击的代码可能会删除该方案`stopClient`方法或更改它的作用。 实现有状态的拒绝服务 (DOS) 保护的合适位置并不在框架或服务器层，而是中前端基础结构。


<a id="detectingreasonfordisconnection"></a>
## <a name="detecting-the-reason-for-a-disconnection"></a>检测断开连接的原因

SignalR 2.1 将重载添加到服务器`OnDisconnect`指示是否客户端有意断开连接的事件而不会超时。`StopCalled`参数为 true，如果客户端显式关闭了连接。 在 JavaScript 中，如果一个服务器错误导致客户端断开连接，错误信息将被传递到客户端作为`$.connection.hub.lastError`。

**C# 服务器代码：`stopCalled`参数**

[!code-csharp[Main](handling-connection-lifetime-events/samples/sample7.cs?highlight=1,3)]

**JavaScript 客户端代码： 访问`lastError`在`disconnect`事件。**

[!code-javascript[Main](handling-connection-lifetime-events/samples/sample8.js?highlight=2-3)]
