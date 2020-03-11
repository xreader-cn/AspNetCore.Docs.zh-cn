---
title: ASP.NET Core 中的 WebSocket 支持
author: rick-anderson
description: 了解如何在 ASP.NET Core 中开始使用 WebSocket。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: fundamentals/websockets
ms.openlocfilehash: a8040003374906fd93e12c9fde44c4a5ccc2cf37
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78645084"
---
# <a name="websockets-support-in-aspnet-core"></a><span data-ttu-id="563a8-103">ASP.NET Core 中的 WebSocket 支持</span><span class="sxs-lookup"><span data-stu-id="563a8-103">WebSockets support in ASP.NET Core</span></span>

<span data-ttu-id="563a8-104">作者：[Tom Dykstra](https://github.com/tdykstra) 和 [Andrew Stanton-Nurse](https://github.com/anurse)</span><span class="sxs-lookup"><span data-stu-id="563a8-104">By [Tom Dykstra](https://github.com/tdykstra) and [Andrew Stanton-Nurse](https://github.com/anurse)</span></span>

<span data-ttu-id="563a8-105">本文介绍 ASP.NET Core 中 WebSocket 的入门方法。</span><span class="sxs-lookup"><span data-stu-id="563a8-105">This article explains how to get started with WebSockets in ASP.NET Core.</span></span> <span data-ttu-id="563a8-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) 是一个协议，支持通过 TCP 连接建立持久的双向信道。</span><span class="sxs-lookup"><span data-stu-id="563a8-106">[WebSocket](https://wikipedia.org/wiki/WebSocket) ([RFC 6455](https://tools.ietf.org/html/rfc6455)) is a protocol that enables two-way persistent communication channels over TCP connections.</span></span> <span data-ttu-id="563a8-107">它用于从快速实时通信中获益的应用，如聊天、仪表板和游戏应用。</span><span class="sxs-lookup"><span data-stu-id="563a8-107">It's used in apps that benefit from fast, real-time communication, such as chat, dashboard, and game apps.</span></span>

<span data-ttu-id="563a8-108">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="563a8-108">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="563a8-109">[如何运行](#sample-app)。</span><span class="sxs-lookup"><span data-stu-id="563a8-109">[How to run](#sample-app).</span></span>

## <a name="signalr"></a><span data-ttu-id="563a8-110">SignalR</span><span class="sxs-lookup"><span data-stu-id="563a8-110">SignalR</span></span>

<span data-ttu-id="563a8-111">[ASP.NET Core SignalR](xref:signalr/introduction) 是一个库，可用于简化向应用添加实时 Web 功能。</span><span class="sxs-lookup"><span data-stu-id="563a8-111">[ASP.NET Core SignalR](xref:signalr/introduction) is a library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="563a8-112">它会尽可能地使用 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="563a8-112">It uses WebSockets whenever possible.</span></span>

<span data-ttu-id="563a8-113">对于大多数应用程序，我们建议使用 SignalR，而不是原始 Websocket。</span><span class="sxs-lookup"><span data-stu-id="563a8-113">For most applications, we recommend SignalR over raw WebSockets.</span></span> <span data-ttu-id="563a8-114">SignalR 可为 Websocket 不可用的环境提供传输回退。</span><span class="sxs-lookup"><span data-stu-id="563a8-114">SignalR provides transport fallback for environments where WebSockets is not available.</span></span> <span data-ttu-id="563a8-115">它还可提供简单的远程过程调用应用模型。</span><span class="sxs-lookup"><span data-stu-id="563a8-115">It also provides a simple remote procedure call app model.</span></span> <span data-ttu-id="563a8-116">并且在大多数情况下，与使用原始 Websocket 相比，SignalR 没有显著的性能缺点。</span><span class="sxs-lookup"><span data-stu-id="563a8-116">And in most scenarios, SignalR has no significant performance disadvantage compared to using raw WebSockets.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="563a8-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="563a8-117">Prerequisites</span></span>

* <span data-ttu-id="563a8-118">ASP.NET Core 1.1 或更高版本</span><span class="sxs-lookup"><span data-stu-id="563a8-118">ASP.NET Core 1.1 or later</span></span>
* <span data-ttu-id="563a8-119">支持 ASP.NET Core 的任何操作系统：</span><span class="sxs-lookup"><span data-stu-id="563a8-119">Any OS that supports ASP.NET Core:</span></span>
  
  * <span data-ttu-id="563a8-120">Windows 7/Windows Server 2008 或更高版本</span><span class="sxs-lookup"><span data-stu-id="563a8-120">Windows 7 / Windows Server 2008 or later</span></span>
  * <span data-ttu-id="563a8-121">Linux</span><span class="sxs-lookup"><span data-stu-id="563a8-121">Linux</span></span>
  * <span data-ttu-id="563a8-122">macOS</span><span class="sxs-lookup"><span data-stu-id="563a8-122">macOS</span></span>
  
* <span data-ttu-id="563a8-123">如果应用在安装了 IIS 的 Windows 上运行：</span><span class="sxs-lookup"><span data-stu-id="563a8-123">If the app runs on Windows with IIS:</span></span>

  * <span data-ttu-id="563a8-124">Windows 8 / Windows Server 2012 及更高版本</span><span class="sxs-lookup"><span data-stu-id="563a8-124">Windows 8 / Windows Server 2012 or later</span></span>
  * <span data-ttu-id="563a8-125">IIS 8 / IIS 8 Express</span><span class="sxs-lookup"><span data-stu-id="563a8-125">IIS 8 / IIS 8 Express</span></span>
  * <span data-ttu-id="563a8-126">必须启用 WebSocket（请参阅 [IIS/IIS Express 支持](#iisiis-express-support)部分。）。</span><span class="sxs-lookup"><span data-stu-id="563a8-126">WebSockets must be enabled (See the [IIS/IIS Express support](#iisiis-express-support) section.).</span></span>
  
* <span data-ttu-id="563a8-127">如果应用在 [HTTP.sys](xref:fundamentals/servers/httpsys) 上运行：</span><span class="sxs-lookup"><span data-stu-id="563a8-127">If the app runs on [HTTP.sys](xref:fundamentals/servers/httpsys):</span></span>

  * <span data-ttu-id="563a8-128">Windows 8 / Windows Server 2012 及更高版本</span><span class="sxs-lookup"><span data-stu-id="563a8-128">Windows 8 / Windows Server 2012 or later</span></span>

* <span data-ttu-id="563a8-129">有关支持的浏览器，请参阅 https://caniuse.com/#feat=websockets 。</span><span class="sxs-lookup"><span data-stu-id="563a8-129">For supported browsers, see https://caniuse.com/#feat=websockets.</span></span>

::: moniker range="< aspnetcore-2.1"

## <a name="nuget-package"></a><span data-ttu-id="563a8-130">NuGet 程序包</span><span class="sxs-lookup"><span data-stu-id="563a8-130">NuGet package</span></span>

<span data-ttu-id="563a8-131">安装 [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) 包。</span><span class="sxs-lookup"><span data-stu-id="563a8-131">Install the [Microsoft.AspNetCore.WebSockets](https://www.nuget.org/packages/Microsoft.AspNetCore.WebSockets/) package.</span></span>

::: moniker-end

## <a name="configure-the-middleware"></a><span data-ttu-id="563a8-132">配置中间件</span><span class="sxs-lookup"><span data-stu-id="563a8-132">Configure the middleware</span></span>


<span data-ttu-id="563a8-133">在 `Startup` 类的 `Configure` 方法中添加 WebSocket 中间件：</span><span class="sxs-lookup"><span data-stu-id="563a8-133">Add the WebSockets middleware in the `Configure` method of the `Startup` class:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSockets)]

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="563a8-134">可配置以下设置：</span><span class="sxs-lookup"><span data-stu-id="563a8-134">The following settings can be configured:</span></span>

* <span data-ttu-id="563a8-135">`KeepAliveInterval` - 向客户端发送“ping”帧的频率，以确保代理保持连接处于打开状态。</span><span class="sxs-lookup"><span data-stu-id="563a8-135">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="563a8-136">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="563a8-136">The default is two minutes.</span></span>
* <span data-ttu-id="563a8-137">`ReceiveBufferSize` - 用于接收数据的缓冲区的大小。</span><span class="sxs-lookup"><span data-stu-id="563a8-137">`ReceiveBufferSize` - The size of the buffer used to receive data.</span></span> <span data-ttu-id="563a8-138">高级用户可能需要对其进行更改，以便根据数据大小调整性能。</span><span class="sxs-lookup"><span data-stu-id="563a8-138">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="563a8-139">默认值为 4 KB。</span><span class="sxs-lookup"><span data-stu-id="563a8-139">The default is 4 KB.</span></span>

::: moniker-end

::: moniker range=">= aspnetcore-2.2"

<span data-ttu-id="563a8-140">可配置以下设置：</span><span class="sxs-lookup"><span data-stu-id="563a8-140">The following settings can be configured:</span></span>

* <span data-ttu-id="563a8-141">`KeepAliveInterval` - 向客户端发送“ping”帧的频率，以确保代理保持连接处于打开状态。</span><span class="sxs-lookup"><span data-stu-id="563a8-141">`KeepAliveInterval` - How frequently to send "ping" frames to the client to ensure proxies keep the connection open.</span></span> <span data-ttu-id="563a8-142">默认值为 2 分钟。</span><span class="sxs-lookup"><span data-stu-id="563a8-142">The default is two minutes.</span></span>
* <span data-ttu-id="563a8-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - 用于接收数据的缓冲区的大小。</span><span class="sxs-lookup"><span data-stu-id="563a8-143"><xref:Microsoft.AspNetCore.Builder.WebSocketOptions.ReceiveBufferSize> - The size of the buffer used to receive data.</span></span> <span data-ttu-id="563a8-144">高级用户可能需要对其进行更改，以便根据数据大小调整性能。</span><span class="sxs-lookup"><span data-stu-id="563a8-144">Advanced users may need to change this for performance tuning based on the size of the data.</span></span> <span data-ttu-id="563a8-145">默认值为 4 KB。</span><span class="sxs-lookup"><span data-stu-id="563a8-145">The default is 4 KB.</span></span>
* <span data-ttu-id="563a8-146">`AllowedOrigins` - 用于 WebSocket 请求的允许的 Origin 标头值列表。</span><span class="sxs-lookup"><span data-stu-id="563a8-146">`AllowedOrigins` - A list of allowed Origin header values for WebSocket requests.</span></span> <span data-ttu-id="563a8-147">默认情况下，允许使用所有源。</span><span class="sxs-lookup"><span data-stu-id="563a8-147">By default, all origins are allowed.</span></span> <span data-ttu-id="563a8-148">有关详细信息，请参阅以下“WebSocket 源限制”。</span><span class="sxs-lookup"><span data-stu-id="563a8-148">See "WebSocket origin restriction" below for details.</span></span>

::: moniker-end

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptions)]

## <a name="accept-websocket-requests"></a><span data-ttu-id="563a8-149">接受 WebSocket 请求</span><span class="sxs-lookup"><span data-stu-id="563a8-149">Accept WebSocket requests</span></span>

<span data-ttu-id="563a8-150">在请求生命周期后期（例如在 `Configure` 方法或操作方法的后期），检查它是否是 WebSocket 请求并接受 WebSocket 请求。</span><span class="sxs-lookup"><span data-stu-id="563a8-150">Somewhere later in the request life cycle (later in the `Configure` method or in an action method, for example) check if it's a WebSocket request and accept the WebSocket request.</span></span>

<span data-ttu-id="563a8-151">以下示例来自 `Configure` 方法的后期：</span><span class="sxs-lookup"><span data-stu-id="563a8-151">The following example is from later in the `Configure` method:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=AcceptWebSocket&highlight=7)]

<span data-ttu-id="563a8-152">WebSocket 请求可以来自任何 URL，但此示例代码只接受 `/ws` 的请求。</span><span class="sxs-lookup"><span data-stu-id="563a8-152">A WebSocket request could come in on any URL, but this sample code only accepts requests for `/ws`.</span></span>

<span data-ttu-id="563a8-153">使用 WebSocket 时，“必须”  在连接期间保持中间件管道运行。</span><span class="sxs-lookup"><span data-stu-id="563a8-153">When using a WebSocket, you **must** keep the middleware pipeline running for the duration of the connection.</span></span> <span data-ttu-id="563a8-154">如果在中间件管道结束后尝试发送或接收 WebSocket 消息，可能会遇到以下异常情况：</span><span class="sxs-lookup"><span data-stu-id="563a8-154">If you attempt to send or receive a WebSocket message after the middleware pipeline ends, you may get an exception like the following:</span></span>

```
System.Net.WebSockets.WebSocketException (0x80004005): The remote party closed the WebSocket connection without completing the close handshake. ---> System.ObjectDisposedException: Cannot write to the response body, the response has completed.
Object name: 'HttpResponseStream'.
```

<span data-ttu-id="563a8-155">如果使用后台服务将数据写入 WebSocket，请确保保持中间件管道运行。</span><span class="sxs-lookup"><span data-stu-id="563a8-155">If you're using a background service to write data to a WebSocket, make sure you keep the middleware pipeline running.</span></span> <span data-ttu-id="563a8-156">通过使用 <xref:System.Threading.Tasks.TaskCompletionSource%601> 执行此操作。</span><span class="sxs-lookup"><span data-stu-id="563a8-156">Do this by using a <xref:System.Threading.Tasks.TaskCompletionSource%601>.</span></span> <span data-ttu-id="563a8-157">传递 `TaskCompletionSource` 到背景服务，并在通过 WebSocket 完成时让其调用 <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A>。</span><span class="sxs-lookup"><span data-stu-id="563a8-157">Pass the `TaskCompletionSource` to your background service and have it call <xref:System.Threading.Tasks.TaskCompletionSource%601.TrySetResult%2A> when you finish with the WebSocket.</span></span> <span data-ttu-id="563a8-158">在请求期间对 <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> 执行 `await`，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="563a8-158">Then `await` the <xref:System.Threading.Tasks.TaskCompletionSource%601.Task> property during the request, as shown in the following example:</span></span>

```csharp
app.Use(async (context, next) => {
    var socket = await context.WebSockets.AcceptWebSocketAsync();
    var socketFinishedTcs = new TaskCompletionSource<object>();

    BackgroundSocketProcessor.AddSocket(socket, socketFinishedTcs); 

    await socketFinishedTcs.Task;
});
```
<span data-ttu-id="563a8-159">如果从操作方法返回过快，则还可能发生 WebSocket 关闭异常。</span><span class="sxs-lookup"><span data-stu-id="563a8-159">The WebSocket closed exception can also happen if you return too soon from an action method.</span></span> <span data-ttu-id="563a8-160">如果接受操作方法中的套接字，请等待使用套接字的代码完成运行，然后再从操作方法返回。</span><span class="sxs-lookup"><span data-stu-id="563a8-160">If you accept a socket in an action method, wait for the code that uses the socket to complete before returning from the action method.</span></span>

<span data-ttu-id="563a8-161">坚决不要使用 `Task.Wait()`、`Task.Result` 或类似阻塞调用来等待套接字完成，因为这可能导致严重的线程处理问题。</span><span class="sxs-lookup"><span data-stu-id="563a8-161">Never use `Task.Wait()`, `Task.Result`, or similar blocking calls to wait for the socket to complete, as that can cause serious threading issues.</span></span> <span data-ttu-id="563a8-162">请始终使用 `await`。</span><span class="sxs-lookup"><span data-stu-id="563a8-162">Always use `await`.</span></span>

## <a name="send-and-receive-messages"></a><span data-ttu-id="563a8-163">发送和接收消息</span><span class="sxs-lookup"><span data-stu-id="563a8-163">Send and receive messages</span></span>

<span data-ttu-id="563a8-164">`AcceptWebSocketAsync` 方法将 TCP 连接升级到 WebSocket 连接，并提供 [WebSocket](/dotnet/core/api/system.net.websockets.websocket) 对象。</span><span class="sxs-lookup"><span data-stu-id="563a8-164">The `AcceptWebSocketAsync` method upgrades the TCP connection to a WebSocket connection and provides a [WebSocket](/dotnet/core/api/system.net.websockets.websocket) object.</span></span> <span data-ttu-id="563a8-165">使用 `WebSocket` 对象发送和接收消息。</span><span class="sxs-lookup"><span data-stu-id="563a8-165">Use the `WebSocket` object to send and receive messages.</span></span>

<span data-ttu-id="563a8-166">之前显示的接受 WebSocket 请求的代码将 `WebSocket` 对象传递给 `Echo` 方法。</span><span class="sxs-lookup"><span data-stu-id="563a8-166">The code shown earlier that accepts the WebSocket request passes the `WebSocket` object to an `Echo` method.</span></span> <span data-ttu-id="563a8-167">代码接收消息并立即发回相同的消息。</span><span class="sxs-lookup"><span data-stu-id="563a8-167">The code receives a message and immediately sends back the same message.</span></span> <span data-ttu-id="563a8-168">循环发送和接收消息，直到客户端关闭连接：</span><span class="sxs-lookup"><span data-stu-id="563a8-168">Messages are sent and received in a loop until the client closes the connection:</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=Echo)]

<span data-ttu-id="563a8-169">如果在开始循环之前接受 WebSocket 连接，中间件管道会结束。</span><span class="sxs-lookup"><span data-stu-id="563a8-169">When accepting the WebSocket connection before beginning the loop, the middleware pipeline ends.</span></span> <span data-ttu-id="563a8-170">关闭套接字后，管道展开。</span><span class="sxs-lookup"><span data-stu-id="563a8-170">Upon closing the socket, the pipeline unwinds.</span></span> <span data-ttu-id="563a8-171">即接受 WebSocket 时，请求停止在管道中推进。</span><span class="sxs-lookup"><span data-stu-id="563a8-171">That is, the request stops moving forward in the pipeline when the WebSocket is accepted.</span></span> <span data-ttu-id="563a8-172">循环结束且套接字关闭时，请求继续回到管道。</span><span class="sxs-lookup"><span data-stu-id="563a8-172">When the loop is finished and the socket is closed, the request proceeds back up the pipeline.</span></span>

::: moniker range=">= aspnetcore-2.2"

## <a name="handle-client-disconnects"></a><span data-ttu-id="563a8-173">处理客户端连接断开</span><span class="sxs-lookup"><span data-stu-id="563a8-173">Handle client disconnects</span></span>

<span data-ttu-id="563a8-174">当客户端由于失去连接而断开连接时不会自动向服务器发送通知。</span><span class="sxs-lookup"><span data-stu-id="563a8-174">The server is not automatically informed when the client disconnects due to loss of connectivity.</span></span> <span data-ttu-id="563a8-175">服务器只有在客户端发送通知时才会收到断开连接消息，而此操作无法在失去 Internet 连接的情况下进行。</span><span class="sxs-lookup"><span data-stu-id="563a8-175">The server receives a disconnect message only if the client sends it, which can't be done if the internet connection is lost.</span></span> <span data-ttu-id="563a8-176">如果想要在发生此情况时采取某个操作，在特定时间范围内未收到来自客户端的任何消息后设置超时。</span><span class="sxs-lookup"><span data-stu-id="563a8-176">If you want to take some action when that happens, set a timeout after nothing is received from the client within a certain time window.</span></span>

<span data-ttu-id="563a8-177">如果客户端并非总是发送消息且不希望仅由于连接进入空闲状态就设置超时，则让客户端使用一个计时器并每隔 X 秒发送一条 ping 消息。</span><span class="sxs-lookup"><span data-stu-id="563a8-177">If the client isn't always sending messages and you don't want to timeout just because the connection goes idle, have the client use a timer to send a ping message every X seconds.</span></span> <span data-ttu-id="563a8-178">在服务器上，如果某条消息在上一条消息发出后的 2\*X 秒内尚未到达，则终止连接并报告客户端已断开连接。</span><span class="sxs-lookup"><span data-stu-id="563a8-178">On the server, if a message hasn't arrived within 2\*X seconds after the previous one, terminate the connection and report that the client disconnected.</span></span> <span data-ttu-id="563a8-179">等待两次预测的时间间隔，以便为可能延迟 ping 消息的网络延迟提供额外的时间。</span><span class="sxs-lookup"><span data-stu-id="563a8-179">Wait for twice the expected time interval to leave extra time for network delays that might hold up the ping message.</span></span>

## <a name="websocket-origin-restriction"></a><span data-ttu-id="563a8-180">WebSocket 源限制</span><span class="sxs-lookup"><span data-stu-id="563a8-180">WebSocket origin restriction</span></span>

<span data-ttu-id="563a8-181">CORS 提供的保护不适用于 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="563a8-181">The protections provided by CORS don't apply to WebSockets.</span></span> <span data-ttu-id="563a8-182">浏览器不会  ：</span><span class="sxs-lookup"><span data-stu-id="563a8-182">Browsers do **not**:</span></span>

* <span data-ttu-id="563a8-183">执行 CORS 预检请求。</span><span class="sxs-lookup"><span data-stu-id="563a8-183">Perform CORS pre-flight requests.</span></span>
* <span data-ttu-id="563a8-184">在发出 WebSocket 请求时，遵守 `Access-Control` 标头中指定的限制。</span><span class="sxs-lookup"><span data-stu-id="563a8-184">Respect the restrictions specified in `Access-Control` headers when making WebSocket requests.</span></span>

<span data-ttu-id="563a8-185">但是，浏览器在发出 WebSocket 请求时会发送 `Origin` 标头。</span><span class="sxs-lookup"><span data-stu-id="563a8-185">However, browsers do send the `Origin` header when issuing WebSocket requests.</span></span> <span data-ttu-id="563a8-186">应将应用程序配置为验证这些标头，以确保只允许来自预期来源的 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="563a8-186">Applications should be configured to validate these headers to ensure that only WebSockets coming from the expected origins are allowed.</span></span>

<span data-ttu-id="563a8-187">如果在“https://server.com ”上托管服务器并在“https://client.com”上托管客户端，请将“https://client.com”添加到 `AllowedOrigins` 列表以验证 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="563a8-187">If you're hosting your server on "https://server.com" and hosting your client on "https://client.com", add "https://client.com" to the `AllowedOrigins` list for WebSockets to verify.</span></span>

[!code-csharp[](websockets/samples/2.x/WebSocketsSample/Startup.cs?name=UseWebSocketsOptionsAO&highlight=6-7)]

> [!NOTE]
> <span data-ttu-id="563a8-188">与 `Referer` 标头一样，`Origin` 标头由客户端控制，并可以伪造。</span><span class="sxs-lookup"><span data-stu-id="563a8-188">The `Origin` header is controlled by the client and, like the `Referer` header, can be faked.</span></span> <span data-ttu-id="563a8-189"> 请勿将这些标头用作身份验证机制。</span><span class="sxs-lookup"><span data-stu-id="563a8-189">Do **not** use these headers as an authentication mechanism.</span></span>

::: moniker-end

## <a name="iisiis-express-support"></a><span data-ttu-id="563a8-190">IIS/IIS Express 支持</span><span class="sxs-lookup"><span data-stu-id="563a8-190">IIS/IIS Express support</span></span>

<span data-ttu-id="563a8-191">安装了 IIS/IIS Express 8 或更高版本的 Windows Server 2012 或更高版本以及 Windows 8 或更高版本支持 WebSocket 协议。</span><span class="sxs-lookup"><span data-stu-id="563a8-191">Windows Server 2012 or later and Windows 8 or later with IIS/IIS Express 8 or later has support for the WebSocket protocol.</span></span>

> [!NOTE]
> <span data-ttu-id="563a8-192">使用 IIS Express 时始终启用 WebSocket。</span><span class="sxs-lookup"><span data-stu-id="563a8-192">WebSockets are always enabled when using IIS Express.</span></span>

### <a name="enabling-websockets-on-iis"></a><span data-ttu-id="563a8-193">在 IIS 上启用 Websocket</span><span class="sxs-lookup"><span data-stu-id="563a8-193">Enabling WebSockets on IIS</span></span>

<span data-ttu-id="563a8-194">在 Windows Server 2012 或更高版本上启用对 WebSocket 协议的支持：</span><span class="sxs-lookup"><span data-stu-id="563a8-194">To enable support for the WebSocket protocol on Windows Server 2012 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="563a8-195">使用 IIS Express 时无需执行这些步骤</span><span class="sxs-lookup"><span data-stu-id="563a8-195">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="563a8-196">通过“管理”  菜单或“服务器管理器”  中的链接使用“添加角色和功能”  向导。</span><span class="sxs-lookup"><span data-stu-id="563a8-196">Use the **Add Roles and Features** wizard from the **Manage** menu or the link in **Server Manager**.</span></span>
1. <span data-ttu-id="563a8-197">选择“基于角色或基于功能的安装”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-197">Select **Role-based or Feature-based Installation**.</span></span> <span data-ttu-id="563a8-198">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-198">Select **Next**.</span></span>
1. <span data-ttu-id="563a8-199">选择适当的服务器（默认情况下选择本地服务器）。</span><span class="sxs-lookup"><span data-stu-id="563a8-199">Select the appropriate server (the local server is selected by default).</span></span> <span data-ttu-id="563a8-200">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-200">Select **Next**.</span></span>
1. <span data-ttu-id="563a8-201">在“角色”树中展开“Web 服务器 (IIS)”、然后依次展开“Web 服务器”和“应用程序开发”     。</span><span class="sxs-lookup"><span data-stu-id="563a8-201">Expand **Web Server (IIS)** in the **Roles** tree, expand **Web Server**, and then expand **Application Development**.</span></span>
1. <span data-ttu-id="563a8-202">选择“WebSocket 协议”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-202">Select **WebSocket Protocol**.</span></span> <span data-ttu-id="563a8-203">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-203">Select **Next**.</span></span>
1. <span data-ttu-id="563a8-204">如果无需其他功能，请选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-204">If additional features aren't needed, select **Next**.</span></span>
1. <span data-ttu-id="563a8-205">选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-205">Select **Install**.</span></span>
1. <span data-ttu-id="563a8-206">安装完成后，选择“关闭”以退出向导  。</span><span class="sxs-lookup"><span data-stu-id="563a8-206">When the installation completes, select **Close** to exit the wizard.</span></span>

<span data-ttu-id="563a8-207">在 Windows 8 或更高版本上启用对 WebSocket 协议的支持：</span><span class="sxs-lookup"><span data-stu-id="563a8-207">To enable support for the WebSocket protocol on Windows 8 or later:</span></span>

> [!NOTE]
> <span data-ttu-id="563a8-208">使用 IIS Express 时无需执行这些步骤</span><span class="sxs-lookup"><span data-stu-id="563a8-208">These steps are not required when using IIS Express</span></span>

1. <span data-ttu-id="563a8-209">导航到“控制面板” > “程序” > “程序和功能” > “启用或禁用 Windows 功能”（位于屏幕左侧）     。</span><span class="sxs-lookup"><span data-stu-id="563a8-209">Navigate to **Control Panel** > **Programs** > **Programs and Features** > **Turn Windows features on or off** (left side of the screen).</span></span>
1. <span data-ttu-id="563a8-210">打开以下节点：“Internet Information Services” > “万维网服务” > “应用程序开发功能”    。</span><span class="sxs-lookup"><span data-stu-id="563a8-210">Open the following nodes: **Internet Information Services** > **World Wide Web Services** > **Application Development Features**.</span></span>
1. <span data-ttu-id="563a8-211">选择“WebSocket 协议”  功能。</span><span class="sxs-lookup"><span data-stu-id="563a8-211">Select the **WebSocket Protocol** feature.</span></span> <span data-ttu-id="563a8-212">选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-212">Select **OK**.</span></span>

### <a name="disable-websocket-when-using-socketio-on-nodejs"></a><span data-ttu-id="563a8-213">在 Node.js 上使用 socket.io 时禁用 WebSocket</span><span class="sxs-lookup"><span data-stu-id="563a8-213">Disable WebSocket when using socket.io on Node.js</span></span>

<span data-ttu-id="563a8-214">如果在 [Node.js](https://nodejs.org/) 的 [socket.io](https://socket.io/) 中使用 WebSocket 支持，请使用 web.config 或 applicationHost.config 中的 `webSocket` 元素禁用默认的 IIS WebSocket 模块   。如果不执行此步骤，IIS WebSocket 模块将尝试处理 WebSocket 通信而不是 Node.js 和应用。</span><span class="sxs-lookup"><span data-stu-id="563a8-214">If using the WebSocket support in [socket.io](https://socket.io/) on [Node.js](https://nodejs.org/), disable the default IIS WebSocket module using the `webSocket` element in *web.config* or *applicationHost.config*. If this step isn't performed, the IIS WebSocket module attempts to handle the WebSocket communication rather than Node.js and the app.</span></span>

```xml
<system.webServer>
  <webSocket enabled="false" />
</system.webServer>
```

## <a name="sample-app"></a><span data-ttu-id="563a8-215">示例应用</span><span class="sxs-lookup"><span data-stu-id="563a8-215">Sample app</span></span>

<span data-ttu-id="563a8-216">本文附带的[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples)是一个 echo 应用。</span><span class="sxs-lookup"><span data-stu-id="563a8-216">The [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/websockets/samples) that accompanies this article is an echo app.</span></span> <span data-ttu-id="563a8-217">它有一个可建立 WebSocket 连接的网页，且服务器将其收到的消息重新发回到客户端。</span><span class="sxs-lookup"><span data-stu-id="563a8-217">It has a web page that makes WebSocket connections, and the server resends any messages it receives back to the client.</span></span> <span data-ttu-id="563a8-218">从命令提示符运行该应用（它未设置为在安装了 IIS Express 的 Visual Studio 中运行）并导航到 http://localhost:5000 。</span><span class="sxs-lookup"><span data-stu-id="563a8-218">Run the app from a command prompt (it's not set up to run from Visual Studio with IIS Express) and navigate to http://localhost:5000.</span></span> <span data-ttu-id="563a8-219">该网页的左上方显示连接状态：</span><span class="sxs-lookup"><span data-stu-id="563a8-219">The web page shows the connection status in the upper left:</span></span>

![网页的初始状态](websockets/_static/start.png)

<span data-ttu-id="563a8-221">选择“连接”，向显示的 URL 发送 WebSocket 请求  。</span><span class="sxs-lookup"><span data-stu-id="563a8-221">Select **Connect** to send a WebSocket request to the URL shown.</span></span> <span data-ttu-id="563a8-222">输入测试消息并选择“发送”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-222">Enter a test message and select **Send**.</span></span> <span data-ttu-id="563a8-223">完成后，请选择“关闭套接字”  。</span><span class="sxs-lookup"><span data-stu-id="563a8-223">When done, select **Close Socket**.</span></span> <span data-ttu-id="563a8-224">“通信日志”  部分会报告每一个发生的“打开”、“发送”和“关闭”操作。</span><span class="sxs-lookup"><span data-stu-id="563a8-224">The **Communication Log** section reports each open, send, and close action as it happens.</span></span>

![网页的初始状态](websockets/_static/end.png)

