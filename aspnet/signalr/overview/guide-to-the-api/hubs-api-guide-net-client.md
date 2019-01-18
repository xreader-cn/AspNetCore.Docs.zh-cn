---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-net-client
title: ASP.NET SignalR 中心 API 指南-.NET 客户端 (C#) |Microsoft Docs
author: pfletcher
description: 本文档介绍了使用 SignalR.NET 客户端，例如 Windows 应用商店 (WinRT)、 WPF、 Silverlight 和缺点在版本 2 的中心 API...
ms.author: riande
ms.date: 01/15/2019
ms.assetid: 6d02d9f7-94e5-4140-9f51-5a6040f274f6
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-net-client
msc.type: authoredcontent
ms.openlocfilehash: 9981b8b91be3395b1a3aa7e0cabb1b7f455d47be
ms.sourcegitcommit: 184ba5b44d1c393076015510ac842b77bc9d4d93
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/18/2019
ms.locfileid: "54396176"
---
<a name="aspnet-signalr-hubs-api-guide---net-client-c"></a><span data-ttu-id="da0d1-103">ASP.NET SignalR 中心 API 指南-.NET 客户端 (C#)</span><span class="sxs-lookup"><span data-stu-id="da0d1-103">ASP.NET SignalR Hubs API Guide - .NET Client (C#)</span></span>
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="da0d1-104">本文档提供使用 SignalR.NET 客户端，例如 Windows 应用商店 (WinRT)、 WPF、 Silverlight 和控制台应用程序中的版本 2 的中心 API 的简介。</span><span class="sxs-lookup"><span data-stu-id="da0d1-104">This document provides an introduction to using the Hubs API for SignalR version 2 in .NET clients, such as Windows Store (WinRT), WPF, Silverlight, and console applications.</span></span>
>
> <span data-ttu-id="da0d1-105">SignalR 中心 API，可从连接的客户端到服务器和客户端到服务器进行远程过程调用 (Rpc)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-105">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="da0d1-106">在服务器代码中，定义可由客户端，调用的方法和调用客户端运行的方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-106">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="da0d1-107">在客户端代码中，定义在服务器上，可以调用的方法，并调用在服务器运行的方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-107">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="da0d1-108">SignalR 将负责所有为你的客户端-服务器探测功能。</span><span class="sxs-lookup"><span data-stu-id="da0d1-108">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
>
> <span data-ttu-id="da0d1-109">SignalR 还提供了一个称为持久连接的较低级别 API。</span><span class="sxs-lookup"><span data-stu-id="da0d1-109">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="da0d1-110">简介 SignalR、 集线器和持久性连接，或者显示了如何构建一个完整的 SignalR 应用程序的教程，请参阅[SignalR-Getting Started](../getting-started/index.md)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-110">For an introduction to SignalR, Hubs, and Persistent Connections, or for a tutorial that shows how to build a complete SignalR application, see [SignalR - Getting Started](../getting-started/index.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="da0d1-111">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="da0d1-111">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="da0d1-112">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="da0d1-112">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)
> - <span data-ttu-id="da0d1-113">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="da0d1-113">.NET 4.5</span></span>
> - <span data-ttu-id="da0d1-114">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="da0d1-114">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="da0d1-115">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="da0d1-115">Previous versions of this topic</span></span>
>
> <span data-ttu-id="da0d1-116">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-116">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="da0d1-117">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="da0d1-117">Questions and comments</span></span>
>
> <span data-ttu-id="da0d1-118">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="da0d1-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="da0d1-119">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="da0d1-120">概述</span><span class="sxs-lookup"><span data-stu-id="da0d1-120">Overview</span></span>

<span data-ttu-id="da0d1-121">本文档包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="da0d1-121">This document contains the following sections:</span></span>

- [<span data-ttu-id="da0d1-122">客户端安装程序</span><span class="sxs-lookup"><span data-stu-id="da0d1-122">Client Setup</span></span>](#clientsetup)
- [<span data-ttu-id="da0d1-123">如何建立连接</span><span class="sxs-lookup"><span data-stu-id="da0d1-123">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="da0d1-124">Silverlight 客户端的跨域连接</span><span class="sxs-lookup"><span data-stu-id="da0d1-124">Cross-domain connections from Silverlight clients</span></span>](#slcrossdomain)
- [<span data-ttu-id="da0d1-125">如何配置连接</span><span class="sxs-lookup"><span data-stu-id="da0d1-125">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="da0d1-126">如何在 WPF 客户端中设置的最大并发连接数</span><span class="sxs-lookup"><span data-stu-id="da0d1-126">How to set the maximum number of concurrent connections in WPF clients</span></span>](#maxconnections)
    - [<span data-ttu-id="da0d1-127">如何指定查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="da0d1-127">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="da0d1-128">如何指定传输方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-128">How to specify the transport method</span></span>](#transport)
    - [<span data-ttu-id="da0d1-129">如何指定 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="da0d1-129">How to specify HTTP headers</span></span>](#httpheaders)
    - [<span data-ttu-id="da0d1-130">如何指定客户端证书</span><span class="sxs-lookup"><span data-stu-id="da0d1-130">How to specify client certificates</span></span>](#clientcertificate)
- [<span data-ttu-id="da0d1-131">如何创建中心代理</span><span class="sxs-lookup"><span data-stu-id="da0d1-131">How to create the Hub proxy</span></span>](#proxy)
- [<span data-ttu-id="da0d1-132">如何定义服务器可以调用的客户端上的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-132">How to define methods on the client that the server can call</span></span>](#callclient)

    - [<span data-ttu-id="da0d1-133">不带参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-133">Methods without parameters</span></span>](#clientmethodswithoutparms)
    - [<span data-ttu-id="da0d1-134">具有指定参数类型的参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-134">Methods with parameters, specifying parameter types</span></span>](#clientmethodswithparmtypes)
    - [<span data-ttu-id="da0d1-135">使用指定的参数的动态对象的参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-135">Methods with parameters, specifying dynamic objects for the parameters</span></span>](#clientmethodswithdynamparms)
    - [<span data-ttu-id="da0d1-136">如何删除一个处理程序</span><span class="sxs-lookup"><span data-stu-id="da0d1-136">How to remove a handler</span></span>](#removehandler)
- [<span data-ttu-id="da0d1-137">如何从客户端调用服务器方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-137">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="da0d1-138">如何处理连接生存期事件</span><span class="sxs-lookup"><span data-stu-id="da0d1-138">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="da0d1-139">如何处理错误</span><span class="sxs-lookup"><span data-stu-id="da0d1-139">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="da0d1-140">如何启用客户端的日志记录</span><span class="sxs-lookup"><span data-stu-id="da0d1-140">How to enable client-side logging</span></span>](#logging)
- [<span data-ttu-id="da0d1-141">WPF、 Silverlight 和控制台应用程序的代码可以调用服务器的客户端方法的示例</span><span class="sxs-lookup"><span data-stu-id="da0d1-141">WPF, Silverlight, and console application code samples for client methods that the server can call</span></span>](#wpfsl)

<span data-ttu-id="da0d1-142">有关示例.NET 客户端项目，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="da0d1-142">For a sample .NET client projects, see the following resources:</span></span>

- <span data-ttu-id="da0d1-143">[gustavo armenta / SignalR 示例](https://github.com/gustavo-armenta/SignalR-Samples)GitHub.com （WinRT，Silverlight 中，控制台应用程序示例） 上。</span><span class="sxs-lookup"><span data-stu-id="da0d1-143">[gustavo-armenta / SignalR-Samples](https://github.com/gustavo-armenta/SignalR-Samples) on GitHub.com (WinRT, Silverlight, console app examples).</span></span>
- <span data-ttu-id="da0d1-144">[DamianEdwards / SignalR MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) GitHub.com （WPF 示例） 上。</span><span class="sxs-lookup"><span data-stu-id="da0d1-144">[DamianEdwards / SignalR-MoveShapeDemo / MoveShape.Desktop](https://github.com/DamianEdwards/SignalR-MoveShapeDemo/tree/master/MoveShape/MoveShape.Desktop) on GitHub.com (WPF example).</span></span>
- <span data-ttu-id="da0d1-145">[SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) GitHub.com （控制台应用程序示例） 上。</span><span class="sxs-lookup"><span data-stu-id="da0d1-145">[SignalR / Microsoft.AspNet.SignalR.Client.Samples](https://github.com/SignalR/SignalR/tree/master/samples/Microsoft.AspNet.SignalR.Client.Samples) on GitHub.com (Console app example).</span></span>

<span data-ttu-id="da0d1-146">有关如何进行编程的服务器或 JavaScript 客户端的文档，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="da0d1-146">For documentation on how to program the server or JavaScript clients, see the following resources:</span></span>

- [<span data-ttu-id="da0d1-147">SignalR 中心 API 指南-服务器</span><span class="sxs-lookup"><span data-stu-id="da0d1-147">SignalR Hubs API Guide - Server</span></span>](hubs-api-guide-server.md)
- [<span data-ttu-id="da0d1-148">SignalR 中心 API 指南-JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="da0d1-148">SignalR Hubs API Guide - JavaScript Client</span></span>](hubs-api-guide-javascript-client.md)

<span data-ttu-id="da0d1-149">API 参考主题的链接将指向.NET 4.5 版本的 API。</span><span class="sxs-lookup"><span data-stu-id="da0d1-149">Links to API Reference topics are to the .NET 4.5 version of the API.</span></span> <span data-ttu-id="da0d1-150">如果使用的.NET 4，请参阅[API 主题的.NET 4 版本](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-150">If you're using .NET 4, see [the .NET 4 version of the API topics](https://msdn.microsoft.com/library/jj891075(v=vs.100).aspx).</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="da0d1-151">客户端安装程序</span><span class="sxs-lookup"><span data-stu-id="da0d1-151">Client setup</span></span>

<span data-ttu-id="da0d1-152">安装[Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet 包 (不[Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr)包)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-152">Install the [Microsoft.AspNet.SignalR.Client](http://nuget.org/packages/Microsoft.AspNet.SignalR.Client) NuGet package (not the [Microsoft.AspNet.SignalR](http://nuget.org/packages/microsoft.aspnet.signalr) package).</span></span> <span data-ttu-id="da0d1-153">此程序包为.NET 4 和.NET 4.5 支持 WinRT、 Silverlight、 WPF、 控制台应用程序和 Windows Phone 客户端。</span><span class="sxs-lookup"><span data-stu-id="da0d1-153">This package supports WinRT, Silverlight, WPF, console application, and Windows Phone clients, for both .NET 4 and .NET 4.5.</span></span>

<span data-ttu-id="da0d1-154">如果在服务器上的版本不同的版本必须在客户端的 SignalR，SignalR 是通常能够适应不同之处。</span><span class="sxs-lookup"><span data-stu-id="da0d1-154">If the version of SignalR that you have on the client is different from the version that you have on the server, SignalR is often able to adapt to the difference.</span></span> <span data-ttu-id="da0d1-155">例如，运行 SignalR 版本 2 的服务器将支持具有 1.1.x 安装的客户端，以及具有版本 2 安装的客户端。</span><span class="sxs-lookup"><span data-stu-id="da0d1-155">For example, a server running SignalR version 2 will support clients that have 1.1.x installed as well as clients that have version 2 installed.</span></span> <span data-ttu-id="da0d1-156">如果在服务器上的版本和客户端上的版本之间的差异太多，或者如果客户端与服务器的较新，SignalR 将引发`InvalidOperationException`客户端将尝试建立连接时出现异常。</span><span class="sxs-lookup"><span data-stu-id="da0d1-156">If the difference between the version on the server and the version on the client is too great, or if the client is newer than the server, SignalR throws an `InvalidOperationException` exception when the client tries to establish a connection.</span></span> <span data-ttu-id="da0d1-157">错误消息是"`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`"。</span><span class="sxs-lookup"><span data-stu-id="da0d1-157">The error message is "`You are using a version of the client that isn't compatible with the server. Client version X.X, server version X.X`".</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="da0d1-158">如何建立连接</span><span class="sxs-lookup"><span data-stu-id="da0d1-158">How to establish a connection</span></span>

<span data-ttu-id="da0d1-159">您可以建立连接之前，必须创建`HubConnection`对象，并创建代理。</span><span class="sxs-lookup"><span data-stu-id="da0d1-159">Before you can establish a connection, you have to create a `HubConnection` object and create a proxy.</span></span> <span data-ttu-id="da0d1-160">若要建立连接，请调用`Start`方法`HubConnection`对象。</span><span class="sxs-lookup"><span data-stu-id="da0d1-160">To establish the connection, call the `Start` method on the `HubConnection` object.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample1.cs?highlight=1,4)]

> [!NOTE]
> <span data-ttu-id="da0d1-161">对于 JavaScript 客户端，需要注册至少一个事件处理程序，然后才能调用`Start`方法以建立连接。</span><span class="sxs-lookup"><span data-stu-id="da0d1-161">For JavaScript clients you have to register at least one event handler before calling the `Start` method to establish the connection.</span></span> <span data-ttu-id="da0d1-162">这不是必需的.NET 客户端。</span><span class="sxs-lookup"><span data-stu-id="da0d1-162">This is not necessary for .NET clients.</span></span> <span data-ttu-id="da0d1-163">对于 JavaScript 客户端，生成的代理代码会自动创建用于所有存在的集线器代理的服务器上，并注册一个处理程序是如何指示哪个中心为客户端想要使用。</span><span class="sxs-lookup"><span data-stu-id="da0d1-163">For JavaScript clients, the generated proxy code automatically creates proxies for all Hubs that exist on the server, and registering a handler is how you indicate which Hubs your client intends to use.</span></span> <span data-ttu-id="da0d1-164">但对于.NET 客户端中心代理手动创建，因此 SignalR 假定，将使用创建的代理的任何中心。</span><span class="sxs-lookup"><span data-stu-id="da0d1-164">But for a .NET client you create Hub proxies manually, so SignalR assumes that you will be using any Hub that you create a proxy for.</span></span>


<span data-ttu-id="da0d1-165">示例代码使用默认值"/ signalr"连接到 SignalR 服务的 URL。</span><span class="sxs-lookup"><span data-stu-id="da0d1-165">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="da0d1-166">有关如何指定不同的基 URL 的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-/signalr URL](hubs-api-guide-server.md#signalrurl)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-166">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="da0d1-167">`Start`方法以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="da0d1-167">The `Start` method executes asynchronously.</span></span> <span data-ttu-id="da0d1-168">若要确保后面的代码行不执行之前建立的连接后，请使用`await`在 ASP.NET 4.5 异步方法或`.Wait()`在同步方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-168">To make sure that subsequent lines of code don't execute until after the connection is established, use `await` in an ASP.NET 4.5 asynchronous method or `.Wait()` in a synchronous method.</span></span> <span data-ttu-id="da0d1-169">不要使用`.Wait()`WinRT 客户端中。</span><span class="sxs-lookup"><span data-stu-id="da0d1-169">Don't use `.Wait()` in a WinRT client.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample2.cs?highlight=1)]

[!code-css[Main](hubs-api-guide-net-client/samples/sample3.css?highlight=1)]

<a id="slcrossdomain"></a>

### <a name="cross-domain-connections-from-silverlight-clients"></a><span data-ttu-id="da0d1-170">Silverlight 客户端的跨域连接</span><span class="sxs-lookup"><span data-stu-id="da0d1-170">Cross-domain connections from Silverlight clients</span></span>

<span data-ttu-id="da0d1-171">有关如何启用 Silverlight 客户端的跨域连接的信息，请参阅[使服务跨域边界可用](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-171">For information about how to enable cross-domain connections from Silverlight clients, see [Making a Service Available Across Domain Boundaries](https://msdn.microsoft.com/library/cc197955(v=vs.95).aspx).</span></span>

<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="da0d1-172">如何配置连接</span><span class="sxs-lookup"><span data-stu-id="da0d1-172">How to configure the connection</span></span>

<span data-ttu-id="da0d1-173">建立连接之前，您可以指定任何以下选项：</span><span class="sxs-lookup"><span data-stu-id="da0d1-173">Before you establish a connection, you can specify any of the following options:</span></span>

- <span data-ttu-id="da0d1-174">并发连接限制。</span><span class="sxs-lookup"><span data-stu-id="da0d1-174">Concurrent connections limit.</span></span>
- <span data-ttu-id="da0d1-175">查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="da0d1-175">Query string parameters.</span></span>
- <span data-ttu-id="da0d1-176">传输方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-176">The transport method.</span></span>
- <span data-ttu-id="da0d1-177">HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="da0d1-177">HTTP headers.</span></span>
- <span data-ttu-id="da0d1-178">客户端证书。</span><span class="sxs-lookup"><span data-stu-id="da0d1-178">Client certificates.</span></span>

<a id="maxconnections"></a>

### <a name="how-to-set-the-maximum-number-of-concurrent-connections-in-wpf-clients"></a><span data-ttu-id="da0d1-179">如何在 WPF 客户端中设置的最大并发连接数</span><span class="sxs-lookup"><span data-stu-id="da0d1-179">How to set the maximum number of concurrent connections in WPF clients</span></span>

<span data-ttu-id="da0d1-180">在 WPF 客户端，您可能需要增加最大数量的并发连接从其默认值为 2。</span><span class="sxs-lookup"><span data-stu-id="da0d1-180">In WPF clients, you might have to increase the maximum number of concurrent connections from its default value of 2.</span></span> <span data-ttu-id="da0d1-181">建议的值为 10。</span><span class="sxs-lookup"><span data-stu-id="da0d1-181">The recommended value is 10.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample4.cs?highlight=4)]

<span data-ttu-id="da0d1-182">有关详细信息，请参阅[ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-182">For more information, see [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx).</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="da0d1-183">如何指定查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="da0d1-183">How to specify query string parameters</span></span>

<span data-ttu-id="da0d1-184">如果你想要将数据发送到服务器，客户端连接时，您可以将查询字符串参数添加到连接对象。</span><span class="sxs-lookup"><span data-stu-id="da0d1-184">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="da0d1-185">下面的示例演示如何在客户端代码中设置的查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="da0d1-185">The following example shows how to set a query string parameter in client code.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample5.cs)]

<span data-ttu-id="da0d1-186">下面的示例演示如何读取服务器代码中的查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="da0d1-186">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample6.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="da0d1-187">如何指定传输方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-187">How to specify the transport method</span></span>

<span data-ttu-id="da0d1-188">作为连接的过程的一部分，SignalR 客户端通常与服务器协商以确定支持的最佳传输服务器和客户端。</span><span class="sxs-lookup"><span data-stu-id="da0d1-188">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="da0d1-189">如果您已经知道你想要使用哪种的传输，可以绕过这个协商过程。</span><span class="sxs-lookup"><span data-stu-id="da0d1-189">If you already know which transport you want to use, you can bypass this negotiation process.</span></span> <span data-ttu-id="da0d1-190">若要指定的传输方法，在传入的传输对象的 Start 方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-190">To specify the transport method, pass in a transport object to the Start method.</span></span> <span data-ttu-id="da0d1-191">下面的示例演示如何在客户端代码中指定的传输方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-191">The following example shows how to specify the transport method in client code.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample7.cs?highlight=4)]

<span data-ttu-id="da0d1-192">[Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx)命名空间包括可用于指定传输的以下类。</span><span class="sxs-lookup"><span data-stu-id="da0d1-192">The [Microsoft.AspNet.SignalR.Client.Transports](https://msdn.microsoft.com/library/jj918090(v=vs.111).aspx) namespace includes the following classes that you can use to specify the transport.</span></span>

- <span data-ttu-id="da0d1-193">[LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)</span><span class="sxs-lookup"><span data-stu-id="da0d1-193">[LongPollingTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.longpollingtransport(v=vs.111).aspx)</span></span>
- <span data-ttu-id="da0d1-194">[ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)</span><span class="sxs-lookup"><span data-stu-id="da0d1-194">[ServerSentEventsTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.serversenteventstransport(v=vs.111).aspx)</span></span>
- <span data-ttu-id="da0d1-195">[WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) （可用仅在服务器和客户端使用.NET 4.5 时。）</span><span class="sxs-lookup"><span data-stu-id="da0d1-195">[WebSocketTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.websockettransport(v=vs.111).aspx) (Available only when both server and client use .NET 4.5.)</span></span>
- <span data-ttu-id="da0d1-196">[AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) （自动选择最佳传输支持的客户端和服务器。</span><span class="sxs-lookup"><span data-stu-id="da0d1-196">[AutoTransport](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.transports.autotransport(v=vs.111).aspx) (Automatically chooses the best transport that is supported by both the client and the server.</span></span> <span data-ttu-id="da0d1-197">这是默认的传输。</span><span class="sxs-lookup"><span data-stu-id="da0d1-197">This is the default transport.</span></span> <span data-ttu-id="da0d1-198">通过这个到`Start`方法具有相同的效果不传递任何内容。)</span><span class="sxs-lookup"><span data-stu-id="da0d1-198">Passing this in to the `Start` method has the same effect as not passing in anything.)</span></span>

<span data-ttu-id="da0d1-199">此列表中不包括 ForeverFrame 传输，因为仅由浏览器使用。</span><span class="sxs-lookup"><span data-stu-id="da0d1-199">The ForeverFrame transport is not included in this list because it is used only by browsers.</span></span>

<span data-ttu-id="da0d1-200">有关如何检查服务器代码中的传输方法的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-如何从上下文属性中获取有关客户端信息](hubs-api-guide-server.md#contextproperty)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-200">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="da0d1-201">有关传输和回退的详细信息，请参阅[简介 SignalR 的传输和回退](../getting-started/introduction-to-signalr.md#transports)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-201">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="httpheaders"></a>

### <a name="how-to-specify-http-headers"></a><span data-ttu-id="da0d1-202">如何指定 HTTP 标头</span><span class="sxs-lookup"><span data-stu-id="da0d1-202">How to specify HTTP headers</span></span>

<span data-ttu-id="da0d1-203">若要设置 HTTP 标头，使用`Headers`连接对象上的属性。</span><span class="sxs-lookup"><span data-stu-id="da0d1-203">To set HTTP headers, use the `Headers` property on the connection object.</span></span> <span data-ttu-id="da0d1-204">下面的示例演示如何添加 HTTP 标头。</span><span class="sxs-lookup"><span data-stu-id="da0d1-204">The following example shows how to add an HTTP header.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample8.cs?highlight=2)]

<a id="clientcertificate"></a>

### <a name="how-to-specify-client-certificates"></a><span data-ttu-id="da0d1-205">如何指定客户端证书</span><span class="sxs-lookup"><span data-stu-id="da0d1-205">How to specify client certificates</span></span>

<span data-ttu-id="da0d1-206">若要添加客户端证书，请使用`AddClientCertificate`连接对象上的方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-206">To add client certificates, use the `AddClientCertificate` method on the connection object.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample9.cs?highlight=2)]

<a id="proxy"></a>

## <a name="how-to-create-the-hub-proxy"></a><span data-ttu-id="da0d1-207">如何创建中心代理</span><span class="sxs-lookup"><span data-stu-id="da0d1-207">How to create the Hub proxy</span></span>

<span data-ttu-id="da0d1-208">为了在服务器上，可以调用集线器的客户端上定义的方法并调用在服务器上的集线器的方法，通过调用创建集线器的代理`CreateHubProxy`连接对象上。</span><span class="sxs-lookup"><span data-stu-id="da0d1-208">In order to define methods on the client that a Hub can call from the server, and to invoke methods on a Hub at the server, create a proxy for the Hub by calling `CreateHubProxy` on the connection object.</span></span> <span data-ttu-id="da0d1-209">在字符串中传递到`CreateHubProxy`是你 Hub 类的名称或指定的名称`HubName`属性如果在服务器上使用一种。</span><span class="sxs-lookup"><span data-stu-id="da0d1-209">The string you pass in to `CreateHubProxy` is the name of your Hub class, or the name specified by the `HubName` attribute if one was used on the server.</span></span> <span data-ttu-id="da0d1-210">名称匹配不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="da0d1-210">Name matching is case-insensitive.</span></span>

<span data-ttu-id="da0d1-211">**在服务器上的 hub 类**</span><span class="sxs-lookup"><span data-stu-id="da0d1-211">**Hub class on server**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample10.cs?highlight=1)]

<span data-ttu-id="da0d1-212">**创建 Hub 类用于客户端代理**</span><span class="sxs-lookup"><span data-stu-id="da0d1-212">**Create client proxy for the Hub class**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample11.cs?highlight=2)]

<span data-ttu-id="da0d1-213">如果修饰在中心类`HubName`属性，请使用该名称。</span><span class="sxs-lookup"><span data-stu-id="da0d1-213">If you decorate your Hub class with a `HubName` attribute, use that name.</span></span>

<span data-ttu-id="da0d1-214">**在服务器上的 hub 类**</span><span class="sxs-lookup"><span data-stu-id="da0d1-214">**Hub class on server**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample12.cs)]

<span data-ttu-id="da0d1-215">**创建 Hub 类用于客户端代理**</span><span class="sxs-lookup"><span data-stu-id="da0d1-215">**Create client proxy for the Hub class**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample13.cs?highlight=2)]

<span data-ttu-id="da0d1-216">如果您调用`HubConnection.CreateHubProxy`多次使用相同`hubName`，获取相同的缓存`IHubProxy`对象。</span><span class="sxs-lookup"><span data-stu-id="da0d1-216">If you call `HubConnection.CreateHubProxy` multiple times with the same `hubName`, you get the same cached `IHubProxy` object.</span></span>

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="da0d1-217">如何定义服务器可以调用的客户端上的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-217">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="da0d1-218">若要定义服务器可以调用的方法，请使用代理服务器的`On`方法来注册事件处理程序。</span><span class="sxs-lookup"><span data-stu-id="da0d1-218">To define a method that the server can call, use the proxy's `On` method to register an event handler.</span></span>

<span data-ttu-id="da0d1-219">方法名称匹配不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="da0d1-219">Method name matching is case-insensitive.</span></span> <span data-ttu-id="da0d1-220">例如，`Clients.All.UpdateStockPrice`在服务器上将执行`updateStockPrice`， `updatestockprice`，或`UpdateStockPrice`客户端上。</span><span class="sxs-lookup"><span data-stu-id="da0d1-220">For example, `Clients.All.UpdateStockPrice` on the server will execute `updateStockPrice`, `updatestockprice`, or `UpdateStockPrice` on the client.</span></span>

<span data-ttu-id="da0d1-221">不同的客户端平台具有不同的要求如何编写方法代码来更新 UI。</span><span class="sxs-lookup"><span data-stu-id="da0d1-221">Different client platforms have different requirements for how you write method code to update the UI.</span></span> <span data-ttu-id="da0d1-222">所示的示例适用于 WinRT (Windows 应用商店.NET) 客户端。</span><span class="sxs-lookup"><span data-stu-id="da0d1-222">The examples shown are for WinRT (Windows Store .NET) clients.</span></span> <span data-ttu-id="da0d1-223">中提供 WPF、 Silverlight 和控制台应用程序示例[本主题后面的单独部分](#wpfsl)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-223">WPF, Silverlight, and console application examples are provided in [a separate section later in this topic](#wpfsl).</span></span>

<a id="clientmethodswithoutparms"></a>

### <a name="methods-without-parameters"></a><span data-ttu-id="da0d1-224">不带参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-224">Methods without parameters</span></span>

<span data-ttu-id="da0d1-225">如果正在处理的方法没有参数，使用的非泛型重载`On`方法：</span><span class="sxs-lookup"><span data-stu-id="da0d1-225">If the method you're handling does not have parameters, use the non-generic overload of the `On` method:</span></span>

<span data-ttu-id="da0d1-226">**服务器代码调用不带参数的客户端方法**</span><span class="sxs-lookup"><span data-stu-id="da0d1-226">**Server code calling client method without parameters**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample14.cs?highlight=5)]

<span data-ttu-id="da0d1-227">**从服务器不带参数调用方法的 WinRT 客户端代码 ([请参阅本主题中的更高版本的 WPF 和 Silverlight 示例](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="da0d1-227">**WinRT Client code for method called from server without parameters ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample15.cs)]

<a id="clientmethodswithparmtypes"></a>

### <a name="methods-with-parameters-specifying-the-parameter-types"></a><span data-ttu-id="da0d1-228">具有指定参数类型的参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-228">Methods with parameters, specifying the parameter types</span></span>

<span data-ttu-id="da0d1-229">如果您正在处理该方法具有参数，指定的参数类型为泛型类型的`On`方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-229">If the method you're handling has parameters, specify the types of the parameters as the generic types of the `On` method.</span></span> <span data-ttu-id="da0d1-230">有的泛型重载`On`方法，以使您可以指定最多 8 个参数 (在 Windows Phone 7 上 4)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-230">There are generic overloads of the `On` method to enable you to specify up to 8 parameters (4 on Windows Phone 7).</span></span> <span data-ttu-id="da0d1-231">在以下示例中，一个参数发送到`UpdateStockPrice`方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-231">In the following example, one parameter is sent to the `UpdateStockPrice` method.</span></span>

<span data-ttu-id="da0d1-232">**服务器代码调用带有参数的客户端方法**</span><span class="sxs-lookup"><span data-stu-id="da0d1-232">**Server code calling client method with a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample16.cs?highlight=3)]

<span data-ttu-id="da0d1-233">**使用参数的 Stock 类**</span><span class="sxs-lookup"><span data-stu-id="da0d1-233">**The Stock class used for the parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample17.cs)]

<span data-ttu-id="da0d1-234">**WinRT 客户端代码的方法调用从服务器使用的参数 ([请参阅本主题中的更高版本的 WPF 和 Silverlight 示例](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="da0d1-234">**WinRT Client code for a method called from server with a parameter ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample18.cs?highlight=1,5)]

<a id="clientmethodswithdynamparms"></a>

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a><span data-ttu-id="da0d1-235">使用指定的参数的动态对象的参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-235">Methods with parameters, specifying dynamic objects for the parameters</span></span>

<span data-ttu-id="da0d1-236">作为泛型类型的指定参数的替代方法作为`On`方法，您可以指定参数作为动态对象：</span><span class="sxs-lookup"><span data-stu-id="da0d1-236">As an alternative to specifying parameters as generic types of the `On` method, you can specify parameters as dynamic objects:</span></span>

<span data-ttu-id="da0d1-237">**服务器代码调用带有参数的客户端方法**</span><span class="sxs-lookup"><span data-stu-id="da0d1-237">**Server code calling client method with a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample19.cs?highlight=3)]

<span data-ttu-id="da0d1-238">**使用参数的 Stock 类**</span><span class="sxs-lookup"><span data-stu-id="da0d1-238">**The Stock class used for the parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample20.cs)]

<span data-ttu-id="da0d1-239">**WinRT 客户端代码的方法调用从 server 参数，并为该参数使用一个动态对象 ([请参阅本主题中的更高版本的 WPF 和 Silverlight 示例](#wpfsl))**</span><span class="sxs-lookup"><span data-stu-id="da0d1-239">**WinRT Client code for a method called from server with a parameter, using a dynamic object for the parameter ([see WPF and Silverlight examples later in this topic](#wpfsl))**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample21.cs?highlight=1,5)]

<a id="removehandler"></a>

### <a name="how-to-remove-a-handler"></a><span data-ttu-id="da0d1-240">如何删除一个处理程序</span><span class="sxs-lookup"><span data-stu-id="da0d1-240">How to remove a handler</span></span>

<span data-ttu-id="da0d1-241">若要删除一个处理程序，请调用其`Dispose`方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-241">To remove a handler, call its `Dispose` method.</span></span>

<span data-ttu-id="da0d1-242">**从服务器调用的方法的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-242">**Client code for a method called from server**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample22.cs?highlight=1)]

<span data-ttu-id="da0d1-243">**若要删除的处理程序的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-243">**Client code to remove the handler**</span></span>

[!code-css[Main](hubs-api-guide-net-client/samples/sample23.css?highlight=1)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="da0d1-244">如何从客户端调用服务器方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-244">How to call server methods from the client</span></span>

<span data-ttu-id="da0d1-245">若要在服务器上调用方法，使用`Invoke`中心代理上的方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-245">To call a method on the server, use the `Invoke` method on the Hub proxy.</span></span>

<span data-ttu-id="da0d1-246">如果服务器方法没有返回值，请使用非泛型重载`Invoke`方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-246">If the server method has no return value, use the non-generic overload of the `Invoke` method.</span></span>

<span data-ttu-id="da0d1-247">**服务器没有返回值的方法的代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-247">**Server code for a method that has no return value**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample24.cs?highlight=3)]

<span data-ttu-id="da0d1-248">**客户端代码调用没有返回值的方法**</span><span class="sxs-lookup"><span data-stu-id="da0d1-248">**Client code calling a method that has no return value**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample25.cs?highlight=1)]

<span data-ttu-id="da0d1-249">如果服务器方法返回值，指定的返回类型的泛型类型作为`Invoke`方法。</span><span class="sxs-lookup"><span data-stu-id="da0d1-249">If the server method has a return value, specify the return type as the generic type of the `Invoke` method.</span></span>

<span data-ttu-id="da0d1-250">**服务器代码的方法的返回值，采用复杂类型参数**</span><span class="sxs-lookup"><span data-stu-id="da0d1-250">**Server code for a method that has a return value and takes a complex type parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample26.cs?highlight=1)]

<span data-ttu-id="da0d1-251">**使用参数和返回值的 Stock 类**</span><span class="sxs-lookup"><span data-stu-id="da0d1-251">**The Stock class used for the parameter and return value**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample27.cs)]

<span data-ttu-id="da0d1-252">**客户端代码调用方法的返回值和 ASP.NET 4.5 的异步方法中采用复杂类型参数，**</span><span class="sxs-lookup"><span data-stu-id="da0d1-252">**Client code calling a method that has a return value and takes a complex type parameter, in an ASP.NET 4.5 async method**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample28.cs?highlight=1-2)]

<span data-ttu-id="da0d1-253">**客户端代码调用方法的返回值和复杂类型参数，采用一种同步方法**</span><span class="sxs-lookup"><span data-stu-id="da0d1-253">**Client code calling a method that has a return value and takes a complex type parameter, in a synchronous method**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample29.cs?highlight=1-2)]

<span data-ttu-id="da0d1-254">`Invoke`方法以异步方式执行，并返回`Task`对象。</span><span class="sxs-lookup"><span data-stu-id="da0d1-254">The `Invoke` method executes asynchronously and returns a `Task` object.</span></span> <span data-ttu-id="da0d1-255">如果未指定`await`或`.Wait()`，调用该方法执行完毕之前，将执行下的一行代码。</span><span class="sxs-lookup"><span data-stu-id="da0d1-255">If you don't specify `await` or `.Wait()`, the next line of code will execute before the method that you invoke has finished executing.</span></span>

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="da0d1-256">如何处理连接生存期事件</span><span class="sxs-lookup"><span data-stu-id="da0d1-256">How to handle connection lifetime events</span></span>

<span data-ttu-id="da0d1-257">SignalR 提供了以下连接可以处理的生存期事件：</span><span class="sxs-lookup"><span data-stu-id="da0d1-257">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="da0d1-258">`Received`：在连接上接收到任何数据时引发。</span><span class="sxs-lookup"><span data-stu-id="da0d1-258">`Received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="da0d1-259">提供接收到的数据。</span><span class="sxs-lookup"><span data-stu-id="da0d1-259">Provides the received data.</span></span>
- <span data-ttu-id="da0d1-260">`ConnectionSlow`：当客户端检测到慢速或频繁删除连接时引发。</span><span class="sxs-lookup"><span data-stu-id="da0d1-260">`ConnectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="da0d1-261">`Reconnecting`：基础传输开始重新连接时引发。</span><span class="sxs-lookup"><span data-stu-id="da0d1-261">`Reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="da0d1-262">`Reconnected`：当基础传输已重新连接时引发。</span><span class="sxs-lookup"><span data-stu-id="da0d1-262">`Reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="da0d1-263">`StateChanged`：连接状态更改时引发。</span><span class="sxs-lookup"><span data-stu-id="da0d1-263">`StateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="da0d1-264">提供的旧状态和新的状态。</span><span class="sxs-lookup"><span data-stu-id="da0d1-264">Provides the old state and the new state.</span></span> <span data-ttu-id="da0d1-265">有关连接状态的值请参阅[ConnectionState 枚举](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-265">For information about connection state values see [ConnectionState Enumeration](https://msdn.microsoft.com/library/microsoft.aspnet.signalr.client.connectionstate(v=vs.111).aspx).</span></span>
- <span data-ttu-id="da0d1-266">`Closed`：当连接已断开连接时引发。</span><span class="sxs-lookup"><span data-stu-id="da0d1-266">`Closed`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="da0d1-267">例如，如果你想要显示的错误的不严重，但会导致间歇性连接问题的警告消息，如缓慢或频繁删除的连接，处理`ConnectionSlow`事件。</span><span class="sxs-lookup"><span data-stu-id="da0d1-267">For example, if you want to display warning messages for errors that are not fatal but cause intermittent connection problems, such as slowness or frequent dropping of the connection, handle the `ConnectionSlow` event.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample30.cs)]

<span data-ttu-id="da0d1-268">有关详细信息，请参阅[理解和 SignalR 中的处理连接生存期事件](handling-connection-lifetime-events.md)。</span><span class="sxs-lookup"><span data-stu-id="da0d1-268">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="da0d1-269">如何处理错误</span><span class="sxs-lookup"><span data-stu-id="da0d1-269">How to handle errors</span></span>

<span data-ttu-id="da0d1-270">如果未显式启用服务器上的详细的错误消息，SignalR 返回出现错误后的异常对象包含有关错误的最少信息。</span><span class="sxs-lookup"><span data-stu-id="da0d1-270">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="da0d1-271">例如，如果调用`newContosoChatMessage`失败，错误对象中的错误消息包含"`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`"发送到在生产环境中的客户端的详细的错误消息不建议出于安全原因，但如果你想要启用详细的错误消息在服务器上进行故障排除，使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="da0d1-271">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample31.cs?highlight=2)]

<a id="handleerrors"></a>

<span data-ttu-id="da0d1-272">若要处理 SignalR 引发的错误，可以添加的处理程序`Error`连接对象上的事件。</span><span class="sxs-lookup"><span data-stu-id="da0d1-272">To handle errors that SignalR raises, you can add a handler for the `Error` event on the connection object.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample32.cs)]

<span data-ttu-id="da0d1-273">若要处理的方法调用中的错误，请将代码包装在 try catch 块中。</span><span class="sxs-lookup"><span data-stu-id="da0d1-273">To handle errors from method invocations, wrap the code in a try-catch block.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample33.cs)]

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="da0d1-274">如何启用客户端的日志记录</span><span class="sxs-lookup"><span data-stu-id="da0d1-274">How to enable client-side logging</span></span>

<span data-ttu-id="da0d1-275">若要启用客户端日志记录，请设置`TraceLevel`和`TraceWriter`连接对象上的属性。</span><span class="sxs-lookup"><span data-stu-id="da0d1-275">To enable client-side logging, set the `TraceLevel` and `TraceWriter` properties on the connection object.</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample34.cs?highlight=2-3)]

<a id="wpfsl"></a>

## <a name="wpf-silverlight-and-console-application-code-samples-for-client-methods-that-the-server-can-call"></a><span data-ttu-id="da0d1-276">WPF、 Silverlight 和控制台应用程序的代码可以调用服务器的客户端方法的示例</span><span class="sxs-lookup"><span data-stu-id="da0d1-276">WPF, Silverlight, and console application code samples for client methods that the server can call</span></span>

<span data-ttu-id="da0d1-277">用于定义服务器可以调用的客户端方法的前面所示的代码示例适用于 WinRT 客户端。</span><span class="sxs-lookup"><span data-stu-id="da0d1-277">The code samples shown earlier for defining client methods that the server can call apply to WinRT clients.</span></span> <span data-ttu-id="da0d1-278">下面的示例演示的 WPF、 Silverlight 和控制台应用程序客户端的等效代码。</span><span class="sxs-lookup"><span data-stu-id="da0d1-278">The following samples show the equivalent code for WPF, Silverlight, and console application clients.</span></span>

### <a name="methods-without-parameters"></a><span data-ttu-id="da0d1-279">不带参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-279">Methods without parameters</span></span>

<span data-ttu-id="da0d1-280">**从服务器不带参数调用方法的 WPF 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-280">**WPF client code for method called from server without parameters**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample35.cs?highlight=1)]

<span data-ttu-id="da0d1-281">**从服务器不带参数调用方法的 Silverlight 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-281">**Silverlight client code for method called from server without parameters**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample36.cs?highlight=1)]

<span data-ttu-id="da0d1-282">**从服务器不带参数调用方法的控制台应用程序客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-282">**Console application client code for method called from server without parameters**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample37.cs?highlight=1)]

### <a name="methods-with-parameters-specifying-the-parameter-types"></a><span data-ttu-id="da0d1-283">具有指定参数类型的参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-283">Methods with parameters, specifying the parameter types</span></span>

<span data-ttu-id="da0d1-284">**从服务器使用的参数调用方法的 WPF 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-284">**WPF client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample38.cs?highlight=1,4)]

<span data-ttu-id="da0d1-285">**从服务器使用的参数调用方法的 Silverlight 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-285">**Silverlight client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample39.cs?highlight=1,5)]

<span data-ttu-id="da0d1-286">**从服务器使用的参数调用方法的控制台应用程序客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-286">**Console application client code for a method called from server with a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample40.cs?highlight=1-2)]

### <a name="methods-with-parameters-specifying-dynamic-objects-for-the-parameters"></a><span data-ttu-id="da0d1-287">使用指定的参数的动态对象的参数的方法</span><span class="sxs-lookup"><span data-stu-id="da0d1-287">Methods with parameters, specifying dynamic objects for the parameters</span></span>

<span data-ttu-id="da0d1-288">**从服务器使用的参数，使用动态对象作为参数调用方法的 WPF 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-288">**WPF client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample41.cs?highlight=1,4)]

<span data-ttu-id="da0d1-289">**从服务器使用的参数，使用动态对象作为参数调用方法的 Silverlight 客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-289">**Silverlight client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample42.cs?highlight=1,5)]

<span data-ttu-id="da0d1-290">**从服务器使用的参数，使用动态对象作为参数调用方法的控制台应用程序客户端代码**</span><span class="sxs-lookup"><span data-stu-id="da0d1-290">**Console application client code for a method called from server with a parameter, using a dynamic object for the parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-net-client/samples/sample43.cs?highlight=1-2)]
