---
uid: signalr/overview/testing-and-debugging/enabling-signalr-tracing
title: 启用 SignalR 跟踪 |Microsoft Docs
author: bradygaster
description: 本文档介绍如何启用和配置跟踪 SignalR 服务器和客户端。 跟踪可用于查看有关事件的诊断信息...
ms.author: bradyg
ms.date: 08/08/2014
ms.assetid: 30060acb-be3e-4347-996f-3870f0c37829
msc.legacyurl: /signalr/overview/testing-and-debugging/enabling-signalr-tracing
msc.type: authoredcontent
ms.openlocfilehash: c6515e3653798ef50e2d2dcb7354ffed407a190e
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836462"
---
<a name="enabling-signalr-tracing"></a><span data-ttu-id="7ac82-104">启用 SignalR 跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-104">Enabling SignalR Tracing</span></span>
====================
<span data-ttu-id="7ac82-105">通过[Tom FitzMacken](https://github.com/tfitzmac)</span><span class="sxs-lookup"><span data-stu-id="7ac82-105">by [Tom FitzMacken](https://github.com/tfitzmac)</span></span>

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="7ac82-106">本文档介绍如何启用和配置跟踪 SignalR 服务器和客户端。</span><span class="sxs-lookup"><span data-stu-id="7ac82-106">This document describes how to enable and configure tracing for SignalR servers and clients.</span></span> <span data-ttu-id="7ac82-107">跟踪可用于在 SignalR 应用程序中查看有关事件的诊断信息。</span><span class="sxs-lookup"><span data-stu-id="7ac82-107">Tracing enables you to view diagnostic information about events in your SignalR application.</span></span>
>
> <span data-ttu-id="7ac82-108">本主题是最初编写的 Patrick Fletcher。</span><span class="sxs-lookup"><span data-stu-id="7ac82-108">This topic was originally written by Patrick Fletcher.</span></span>
>
> ## <a name="software-versions-used-in-the-tutorial"></a><span data-ttu-id="7ac82-109">在本教程中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="7ac82-109">Software versions used in the tutorial</span></span>
>
>
> - [<span data-ttu-id="7ac82-110">Visual Studio 2013</span><span class="sxs-lookup"><span data-stu-id="7ac82-110">Visual Studio 2013</span></span>](https://my.visualstudio.com/Downloads?q=visual%20studio%202013)
> - <span data-ttu-id="7ac82-111">.NET Framework 4.5</span><span class="sxs-lookup"><span data-stu-id="7ac82-111">.NET Framework 4.5</span></span>
> - <span data-ttu-id="7ac82-112">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="7ac82-112">SignalR version 2</span></span>
>
>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="7ac82-113">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="7ac82-113">Questions and comments</span></span>
>
> <span data-ttu-id="7ac82-114">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="7ac82-114">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="7ac82-115">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="7ac82-115">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>


<span data-ttu-id="7ac82-116">启用跟踪，SignalR 应用程序创建事件日志的条目。</span><span class="sxs-lookup"><span data-stu-id="7ac82-116">When tracing is enabled, a SignalR application creates log entries for events.</span></span> <span data-ttu-id="7ac82-117">可以从客户端和服务器来记录事件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-117">You can log events from both the client and the server.</span></span> <span data-ttu-id="7ac82-118">跟踪对服务器日志连接、 横向扩展提供程序和消息总线的事件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-118">Tracing on the server logs connection, scaleout provider, and message bus events.</span></span> <span data-ttu-id="7ac82-119">跟踪对客户端日志连接事件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-119">Tracing on the client logs connection events.</span></span> <span data-ttu-id="7ac82-120">SignalR 2.1 及更高版本，在客户端上的跟踪记录集线器调用消息的完整的内容。</span><span class="sxs-lookup"><span data-stu-id="7ac82-120">In SignalR 2.1 and later, tracing on the client logs the full content of hub invocation messages.</span></span>

## <a name="contents"></a><span data-ttu-id="7ac82-121">内容</span><span class="sxs-lookup"><span data-stu-id="7ac82-121">Contents</span></span>

- [<span data-ttu-id="7ac82-122">在服务器上启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-122">Enabling tracing on the server</span></span>](#server)

    - [<span data-ttu-id="7ac82-123">服务器事件记录到文本文件</span><span class="sxs-lookup"><span data-stu-id="7ac82-123">Logging server events to text files</span></span>](#server_text)
    - [<span data-ttu-id="7ac82-124">服务器事件记录到事件日志</span><span class="sxs-lookup"><span data-stu-id="7ac82-124">Logging server events to the event log</span></span>](#server_eventlog)
- [<span data-ttu-id="7ac82-125">在.NET 客户端 （Windows 桌面应用程序） 中启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-125">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>](#net_client)

    - [<span data-ttu-id="7ac82-126">桌面客户端事件记录到控制台</span><span class="sxs-lookup"><span data-stu-id="7ac82-126">Logging Desktop client events to the console</span></span>](#desktop_console)
    - [<span data-ttu-id="7ac82-127">桌面客户端事件记录到文本文件</span><span class="sxs-lookup"><span data-stu-id="7ac82-127">Logging Desktop client events to a text file</span></span>](#desktop_text)
- [<span data-ttu-id="7ac82-128">在 Windows Phone 8 客户端中启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-128">Enabling tracing in Windows Phone 8 clients</span></span>](#phone)

    - [<span data-ttu-id="7ac82-129">Windows Phone 客户端事件记录到 UI</span><span class="sxs-lookup"><span data-stu-id="7ac82-129">Logging Windows Phone client events to the UI</span></span>](#phone_ui)
    - [<span data-ttu-id="7ac82-130">Windows Phone 客户端事件记录到调试控制台</span><span class="sxs-lookup"><span data-stu-id="7ac82-130">Logging Windows Phone client events to the debug console</span></span>](#phone_debug)
- [<span data-ttu-id="7ac82-131">在 JavaScript 客户端中启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-131">Enabling tracing in the JavaScript client</span></span>](#javascript)

<a id="server"></a>
## <a name="enabling-tracing-on-the-server"></a><span data-ttu-id="7ac82-132">在服务器上启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-132">Enabling tracing on the server</span></span>

<span data-ttu-id="7ac82-133">启用应用程序的配置文件 （App.config 或 Web.config，具体取决于项目的类型。） 中的服务器的跟踪指定要记录的事件的类别。</span><span class="sxs-lookup"><span data-stu-id="7ac82-133">You enable tracing on the server within the application's configuration file (either App.config or Web.config depending on the type of project.) You specify which categories of events you want to log.</span></span> <span data-ttu-id="7ac82-134">在配置文件中，您还指定是否记录到文本文件、 Windows 事件日志或使用的实现的自定义日志的事件[TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx)。</span><span class="sxs-lookup"><span data-stu-id="7ac82-134">In the configuration file, you also specify whether to log the events to a text file, the Windows event log, or a custom log using an implementation of [TraceListener](https://msdn.microsoft.com/library/system.diagnostics.tracelistener(v=vs.110).aspx).</span></span>

<span data-ttu-id="7ac82-135">Server 事件类别包括以下类型的消息：</span><span class="sxs-lookup"><span data-stu-id="7ac82-135">The server event categories include the following sorts of messages:</span></span>

| <span data-ttu-id="7ac82-136">源</span><span class="sxs-lookup"><span data-stu-id="7ac82-136">Source</span></span> | <span data-ttu-id="7ac82-137">消息</span><span class="sxs-lookup"><span data-stu-id="7ac82-137">Messages</span></span> |
| --- | --- |
| <span data-ttu-id="7ac82-138">SignalR.SqlMessageBus</span><span class="sxs-lookup"><span data-stu-id="7ac82-138">SignalR.SqlMessageBus</span></span> | <span data-ttu-id="7ac82-139">SQL 消息总线的扩展提供程序安装程序、 数据库操作、 错误和超时事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-139">SQL Message Bus scaleout provider setup, database operation, error, and timeout events</span></span> |
| <span data-ttu-id="7ac82-140">SignalR.ServiceBusMessageBus</span><span class="sxs-lookup"><span data-stu-id="7ac82-140">SignalR.ServiceBusMessageBus</span></span> | <span data-ttu-id="7ac82-141">服务总线的扩展提供程序主题创建和订阅、 错误和消息传送事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-141">Service bus scaleout provider topic creation and subscription, error, and messaging events</span></span> |
| <span data-ttu-id="7ac82-142">SignalR.RedisMessageBus</span><span class="sxs-lookup"><span data-stu-id="7ac82-142">SignalR.RedisMessageBus</span></span> | <span data-ttu-id="7ac82-143">Redis 扩展提供程序连接、 断开连接和错误事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-143">Redis scaleout provider connection, disconnection, and error events</span></span> |
| <span data-ttu-id="7ac82-144">SignalR.ScaleoutMessageBus</span><span class="sxs-lookup"><span data-stu-id="7ac82-144">SignalR.ScaleoutMessageBus</span></span> | <span data-ttu-id="7ac82-145">横向扩展消息传送事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-145">Scaleout messaging events</span></span> |
| <span data-ttu-id="7ac82-146">SignalR.Transports.WebSocketTransport</span><span class="sxs-lookup"><span data-stu-id="7ac82-146">SignalR.Transports.WebSocketTransport</span></span> | <span data-ttu-id="7ac82-147">WebSocket 传输连接、 断开连接、 消息和错误事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-147">WebSocket transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="7ac82-148">SignalR.Transports.ServerSentEventsTransport</span><span class="sxs-lookup"><span data-stu-id="7ac82-148">SignalR.Transports.ServerSentEventsTransport</span></span> | <span data-ttu-id="7ac82-149">ServerSentEvents 传输连接、 断开连接、 消息和错误事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-149">ServerSentEvents transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="7ac82-150">SignalR.Transports.ForeverFrameTransport</span><span class="sxs-lookup"><span data-stu-id="7ac82-150">SignalR.Transports.ForeverFrameTransport</span></span> | <span data-ttu-id="7ac82-151">ForeverFrame 传输连接、 断开连接、 消息和错误事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-151">ForeverFrame transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="7ac82-152">SignalR.Transports.LongPollingTransport</span><span class="sxs-lookup"><span data-stu-id="7ac82-152">SignalR.Transports.LongPollingTransport</span></span> | <span data-ttu-id="7ac82-153">LongPolling 传输连接、 断开连接、 消息和错误事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-153">LongPolling transport connection, disconnection, messaging, and error events</span></span> |
| <span data-ttu-id="7ac82-154">SignalR.Transports.TransportHeartBeat</span><span class="sxs-lookup"><span data-stu-id="7ac82-154">SignalR.Transports.TransportHeartBeat</span></span> | <span data-ttu-id="7ac82-155">传输连接、 断开连接，并保持连接事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-155">Transport connection, disconnection, and keepalive events</span></span> |
| <span data-ttu-id="7ac82-156">SignalR.ReflectedHubDescriptorProvider</span><span class="sxs-lookup"><span data-stu-id="7ac82-156">SignalR.ReflectedHubDescriptorProvider</span></span> | <span data-ttu-id="7ac82-157">中心发现事件</span><span class="sxs-lookup"><span data-stu-id="7ac82-157">Hub discovery events</span></span> |

<a id="server_text"></a>
### <a name="logging-server-events-to-text-files"></a><span data-ttu-id="7ac82-158">服务器事件记录到文本文件</span><span class="sxs-lookup"><span data-stu-id="7ac82-158">Logging server events to text files</span></span>

<span data-ttu-id="7ac82-159">下面的代码演示如何启用对每个类别的事件的跟踪。</span><span class="sxs-lookup"><span data-stu-id="7ac82-159">The following code shows how to enable tracing for each category of event.</span></span> <span data-ttu-id="7ac82-160">此示例将配置应用程序以将事件记录到文本文件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-160">This sample configures the application to log events to text files.</span></span>

<span data-ttu-id="7ac82-161">**XML 用于启用跟踪的服务器代码**</span><span class="sxs-lookup"><span data-stu-id="7ac82-161">**XML server code for enabling tracing**</span></span>

[!code-html[Main](enabling-signalr-tracing/samples/sample1.html)]

<span data-ttu-id="7ac82-162">在上面的代码，`SignalRSwitch`项指定了[TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx)用于发送到指定的日志的事件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-162">In the code above, the `SignalRSwitch` entry specifies the [TraceLevel](https://msdn.microsoft.com/library/system.diagnostics.tracelevel(v=vs.110).aspx) used for events sent to the specified log.</span></span> <span data-ttu-id="7ac82-163">在这种情况下，设置为`Verbose`这意味着所有的调试和跟踪消息记录。</span><span class="sxs-lookup"><span data-stu-id="7ac82-163">In this case, it is set to `Verbose` which means all debugging and tracing messages are logged.</span></span>

<span data-ttu-id="7ac82-164">下面的输出显示中的条目`transports.log.txt`使用上面的配置文件的应用程序的文件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-164">The following output shows entries from the `transports.log.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="7ac82-165">它显示了一个新连接、 已删除的连接和传输检测信号事件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-165">It shows a new connection, a removed connection, and transport heartbeat events.</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample2.cmd)]

<a id="server_eventlog"></a>
### <a name="logging-server-events-to-the-event-log"></a><span data-ttu-id="7ac82-166">服务器事件记录到事件日志</span><span class="sxs-lookup"><span data-stu-id="7ac82-166">Logging server events to the event log</span></span>

<span data-ttu-id="7ac82-167">若要事件记录到事件日志而不是一个文本文件，将更改的值中的条目`sharedListeners`节点。</span><span class="sxs-lookup"><span data-stu-id="7ac82-167">To log events to the event log rather than a text file, change the values for the entries in the `sharedListeners` node.</span></span> <span data-ttu-id="7ac82-168">下面的代码演示如何服务器事件记录到事件日志：</span><span class="sxs-lookup"><span data-stu-id="7ac82-168">The following code shows how to log server events to the event log:</span></span>

<span data-ttu-id="7ac82-169">**XML 用于日志记录事件写入事件日志的服务器代码**</span><span class="sxs-lookup"><span data-stu-id="7ac82-169">**XML server code for logging events to the event log**</span></span>

[!code-xml[Main](enabling-signalr-tracing/samples/sample3.xml)]

<span data-ttu-id="7ac82-170">事件记录在应用程序日志中，并且可通过事件查看器中，如下所示：</span><span class="sxs-lookup"><span data-stu-id="7ac82-170">The events are logged in the Application log, and are available through the Event Viewer, as shown below:</span></span>

![事件查看器显示 SignalR 日志](enabling-signalr-tracing/_static/image1.png)

> [!NOTE]
> <span data-ttu-id="7ac82-172">当使用事件日志，设置**TraceLevel**到**错误**以消息的数量保持在易于管理。</span><span class="sxs-lookup"><span data-stu-id="7ac82-172">When using the event log, set the **TraceLevel** to **Error** to keep the number of messages manageable.</span></span>

<a id="net_client"></a>
## <a name="enabling-tracing-in-the-net-client-windows-desktop-apps"></a><span data-ttu-id="7ac82-173">在.NET 客户端 （Windows 桌面应用程序） 中启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-173">Enabling tracing in the .NET client (Windows Desktop apps)</span></span>

<span data-ttu-id="7ac82-174">.NET 客户端可以将事件记录到控制台中，一个文本文件，或使用的实现的自定义日志[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)。</span><span class="sxs-lookup"><span data-stu-id="7ac82-174">The .NET client can log events to the console, a text file, or to a custom log using an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx).</span></span>

<span data-ttu-id="7ac82-175">若要启用.NET 客户端中的日志记录，设置连接的`TraceLevel`属性设置为[TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx)值，并且`TraceWriter`属性设置为有效[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx)实例。</span><span class="sxs-lookup"><span data-stu-id="7ac82-175">To enable logging in the .NET client, set the connection's `TraceLevel` property to a [TraceLevels](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.tracelevels(v=vs.118).aspx) value, and the `TraceWriter` property to a valid [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter.aspx) instance.</span></span>

<a id="desktop_console"></a>
### <a name="logging-desktop-client-events-to-the-console"></a><span data-ttu-id="7ac82-176">桌面客户端事件记录到控制台</span><span class="sxs-lookup"><span data-stu-id="7ac82-176">Logging Desktop client events to the console</span></span>

<span data-ttu-id="7ac82-177">下面的 C# 代码演示如何在.NET 客户端控制台中记录事件：</span><span class="sxs-lookup"><span data-stu-id="7ac82-177">The following C# code shows how to log events in the .NET client to the console:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample4.cs?highlight=2-3)]

<a id="desktop_text"></a>
### <a name="logging-desktop-client-events-to-a-text-file"></a><span data-ttu-id="7ac82-178">桌面客户端事件记录到文本文件</span><span class="sxs-lookup"><span data-stu-id="7ac82-178">Logging Desktop client events to a text file</span></span>

<span data-ttu-id="7ac82-179">下面的 C# 代码演示如何在.NET 客户端到文本文件中记录事件：</span><span class="sxs-lookup"><span data-stu-id="7ac82-179">The following C# code shows how to log events in the .NET client to a text file:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample5.cs?highlight=4-5)]

<span data-ttu-id="7ac82-180">下面的输出显示中的条目`ClientLog.txt`使用上面的配置文件的应用程序的文件。</span><span class="sxs-lookup"><span data-stu-id="7ac82-180">The following output shows entries from the `ClientLog.txt` file for an application using the above configuration file.</span></span> <span data-ttu-id="7ac82-181">它显示了客户端连接到服务器，并调用客户端方法的中心调用`addMessage`:</span><span class="sxs-lookup"><span data-stu-id="7ac82-181">It shows the client connecting to the server, and the hub invoking a client method called `addMessage`:</span></span>

[!code-console[Main](enabling-signalr-tracing/samples/sample6.cmd)]

<a id="phone"></a>
## <a name="enabling-tracing-in-windows-phone-8-clients"></a><span data-ttu-id="7ac82-182">在 Windows Phone 8 客户端中启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-182">Enabling tracing in Windows Phone 8 clients</span></span>

<span data-ttu-id="7ac82-183">对于 Windows Phone 应用的 SignalR 应用程序使用同一个.NET 客户端作为桌面应用程序，但[Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx)和写入的文件[StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx)不可用。</span><span class="sxs-lookup"><span data-stu-id="7ac82-183">SignalR applications for Windows Phone apps use the same .NET client as desktop apps, but [Console.Out](https://msdn.microsoft.com/library/system.console.out(v=vs.110).aspx) and writing to a file with [StreamWriter](https://msdn.microsoft.com/library/system.io.streamwriter(v=vs.110).aspx) are not available.</span></span> <span data-ttu-id="7ac82-184">相反，您需要创建的自定义实现[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx)进行跟踪。</span><span class="sxs-lookup"><span data-stu-id="7ac82-184">Instead, you need to create a custom implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) for tracing.</span></span>

<a id="phone_ui"></a>
### <a name="logging-windows-phone-client-events-to-the-ui"></a><span data-ttu-id="7ac82-185">Windows Phone 客户端事件记录到 UI</span><span class="sxs-lookup"><span data-stu-id="7ac82-185">Logging Windows Phone client events to the UI</span></span>

<span data-ttu-id="7ac82-186">[SignalR o d e b](https://github.com/SignalR/SignalR/archive/master.zip)包括写入跟踪输出发送到了 Windows Phone 示例[TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx)使用自定义[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx)实现调用`TextBlockWriter`。</span><span class="sxs-lookup"><span data-stu-id="7ac82-186">The [SignalR codebase](https://github.com/SignalR/SignalR/archive/master.zip) includes a Windows Phone sample that writes trace output to a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) using a custom [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) implementation called `TextBlockWriter`.</span></span> <span data-ttu-id="7ac82-187">此类可在**samples/Microsoft.AspNet.SignalR.Client.WP8.Samples**项目。</span><span class="sxs-lookup"><span data-stu-id="7ac82-187">This class can be found in the **samples/Microsoft.AspNet.SignalR.Client.WP8.Samples** project.</span></span> <span data-ttu-id="7ac82-188">创建的实例时`TextBlockWriter`，在当前传递[SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx)，和一个[StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx)其中它将创建[TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx)要用于跟踪输出：</span><span class="sxs-lookup"><span data-stu-id="7ac82-188">When creating an instance of `TextBlockWriter`, pass in the current [SynchronizationContext](https://msdn.microsoft.com/library/system.threading.synchronizationcontext(v=vs.110).aspx), and a [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) where it will create a [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) to use for trace output:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample7.cs)]

<span data-ttu-id="7ac82-189">跟踪然后将输出写入到新[TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx)中创建[StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx)中传递：</span><span class="sxs-lookup"><span data-stu-id="7ac82-189">The trace output will then be written to a new [TextBlock](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.textblock.aspx) created in the [StackPanel](https://msdn.microsoft.com/library/windows/apps/windows.ui.xaml.controls.stackpanel.aspx) you passed in:</span></span>

![](enabling-signalr-tracing/_static/image2.png)

<a id="phone_debug"></a>
### <a name="logging-windows-phone-client-events-to-the-debug-console"></a><span data-ttu-id="7ac82-190">Windows Phone 客户端事件记录到调试控制台</span><span class="sxs-lookup"><span data-stu-id="7ac82-190">Logging Windows Phone client events to the debug console</span></span>

<span data-ttu-id="7ac82-191">若要将输出发送到调试控制台而不是在 UI 中，创建的实现[TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) ，将写入到调试窗口中，并将其分配给你的连接[TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx)属性：</span><span class="sxs-lookup"><span data-stu-id="7ac82-191">To send output to the debug console rather than the UI, create an implementation of [TextWriter](https://msdn.microsoft.com/library/system.io.textwriter(v=vs.110).aspx) that writes to the debug window, and assign it to your connection's [TraceWriter](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connection.tracewriter(v=vs.118).aspx) property:</span></span>

[!code-csharp[Main](enabling-signalr-tracing/samples/sample8.cs)]

<span data-ttu-id="7ac82-192">然后将跟踪信息写入 Visual Studio 中的调试窗口：</span><span class="sxs-lookup"><span data-stu-id="7ac82-192">Trace information will then be written to the debug window in Visual Studio:</span></span>

![](enabling-signalr-tracing/_static/image3.png)

<a id="javascript"></a>
## <a name="enabling-tracing-in-the-javascript-client"></a><span data-ttu-id="7ac82-193">在 JavaScript 客户端中启用跟踪</span><span class="sxs-lookup"><span data-stu-id="7ac82-193">Enabling tracing in the JavaScript client</span></span>

<span data-ttu-id="7ac82-194">若要启用客户端的日志记录的连接上，设置`logging`属性对 connection 对象，然后再调用`start`方法以建立连接。</span><span class="sxs-lookup"><span data-stu-id="7ac82-194">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="7ac82-195">**用于启用跟踪 （具有生成的代理） 的浏览器控制台到客户端 JavaScript 代码**</span><span class="sxs-lookup"><span data-stu-id="7ac82-195">**Client JavaScript code for enabling tracing to the browser console (with the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample9.js?highlight=1)]

<span data-ttu-id="7ac82-196">**用于启用跟踪到浏览器控制台 （而不生成的代理） 的客户端 JavaScript 代码**</span><span class="sxs-lookup"><span data-stu-id="7ac82-196">**Client JavaScript code for enabling tracing to the browser console (without the generated proxy)**</span></span>

[!code-javascript[Main](enabling-signalr-tracing/samples/sample10.js?highlight=2)]

<span data-ttu-id="7ac82-197">启用跟踪后，JavaScript 客户端将事件记录到浏览器控制台。</span><span class="sxs-lookup"><span data-stu-id="7ac82-197">When tracing is enabled, the JavaScript client logs events to the browser console.</span></span> <span data-ttu-id="7ac82-198">若要访问浏览器控制台，请参阅[监视传输](../getting-started/introduction-to-signalr.md#MonitoringTransports)。</span><span class="sxs-lookup"><span data-stu-id="7ac82-198">To access the browser console, see [Monitoring Transports](../getting-started/introduction-to-signalr.md#MonitoringTransports).</span></span>

<span data-ttu-id="7ac82-199">下面的屏幕截图显示 SignalR JavaScript 客户端启用了跟踪。</span><span class="sxs-lookup"><span data-stu-id="7ac82-199">The following screenshot shows a SignalR JavaScript client with tracing enabled.</span></span> <span data-ttu-id="7ac82-200">它显示在浏览器控制台中的连接和中心调用事件：</span><span class="sxs-lookup"><span data-stu-id="7ac82-200">It shows connection and hub invocation events in the browser console:</span></span>

![浏览器控制台中的 SignalR 跟踪事件](enabling-signalr-tracing/_static/image4.png)
