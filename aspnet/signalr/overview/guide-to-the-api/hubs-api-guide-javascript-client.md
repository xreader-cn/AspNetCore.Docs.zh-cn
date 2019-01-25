---
uid: signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
title: ASP.NET SignalR 中心 API 指南-JavaScript 客户端 |Microsoft Docs
author: bradygaster
description: 本文档介绍了使用 SignalR 版本 2 中 JavaScript 客户端，如浏览器和 Windows 应用商店 (WinJS) applicat 中心 API...
ms.author: bradyg
ms.date: 01/15/2019
ms.assetid: a9fd4dc0-1b96-4443-82ca-932a5b4a8ea4
msc.legacyurl: /signalr/overview/guide-to-the-api/hubs-api-guide-javascript-client
msc.type: authoredcontent
ms.openlocfilehash: 7689fa5b6a3e81c9f767831423eb3efad72e1ab3
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836592"
---
<a name="aspnet-signalr-hubs-api-guide---javascript-client"></a><span data-ttu-id="ac8b3-103">ASP.NET SignalR 中心 API 指南-JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="ac8b3-103">ASP.NET SignalR Hubs API Guide - JavaScript Client</span></span>
====================

[!INCLUDE [Consider ASP.NET Core SignalR](~/includes/signalr/signalr-version-disambiguation.md)]

> <span data-ttu-id="ac8b3-104">本文档提供使用 SignalR 版本 2 中 JavaScript 客户端，如浏览器和 Windows 应用商店 (WinJS) 应用程序为中心 API 的简介。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-104">This document provides an introduction to using the Hubs API for SignalR version 2 in JavaScript clients, such as browsers and Windows Store (WinJS) applications.</span></span>
>
> <span data-ttu-id="ac8b3-105">SignalR 中心 API，可从连接的客户端到服务器和客户端到服务器进行远程过程调用 (Rpc)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-105">The SignalR Hubs API enables you to make remote procedure calls (RPCs) from a server to connected clients and from clients to the server.</span></span> <span data-ttu-id="ac8b3-106">在服务器代码中，定义可由客户端，调用的方法和调用客户端运行的方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-106">In server code, you define methods that can be called by clients, and you call methods that run on the client.</span></span> <span data-ttu-id="ac8b3-107">在客户端代码中，定义在服务器上，可以调用的方法，并调用在服务器运行的方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-107">In client code, you define methods that can be called from the server, and you call methods that run on the server.</span></span> <span data-ttu-id="ac8b3-108">SignalR 将负责所有为你的客户端-服务器探测功能。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-108">SignalR takes care of all of the client-to-server plumbing for you.</span></span>
>
> <span data-ttu-id="ac8b3-109">SignalR 还提供了一个称为持久连接的较低级别 API。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-109">SignalR also offers a lower-level API called Persistent Connections.</span></span> <span data-ttu-id="ac8b3-110">SignalR、 集线器和持久性连接的介绍，请参阅[SignalR 简介](../getting-started/introduction-to-signalr.md)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-110">For an introduction to SignalR, Hubs, and Persistent Connections, see [Introduction to SignalR](../getting-started/introduction-to-signalr.md).</span></span>
>
> ## <a name="software-versions-used-in-this-topic"></a><span data-ttu-id="ac8b3-111">本主题中使用的软件版本</span><span class="sxs-lookup"><span data-stu-id="ac8b3-111">Software versions used in this topic</span></span>
>
>
> - [<span data-ttu-id="ac8b3-112">Visual Studio 2017</span><span class="sxs-lookup"><span data-stu-id="ac8b3-112">Visual Studio 2017</span></span>](https://visualstudio.microsoft.com/downloads/)
> - <span data-ttu-id="ac8b3-113">.NET 4.5</span><span class="sxs-lookup"><span data-stu-id="ac8b3-113">.NET 4.5</span></span>
> - <span data-ttu-id="ac8b3-114">SignalR 版本 2</span><span class="sxs-lookup"><span data-stu-id="ac8b3-114">SignalR version 2</span></span>
>
>
>
> ## <a name="previous-versions-of-this-topic"></a><span data-ttu-id="ac8b3-115">本主题的早期版本</span><span class="sxs-lookup"><span data-stu-id="ac8b3-115">Previous versions of this topic</span></span>
>
> <span data-ttu-id="ac8b3-116">有关 SignalR 的早期版本的信息，请参阅[SignalR 较早版本](../older-versions/index.md)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-116">For information about earlier versions of SignalR, see [SignalR Older Versions](../older-versions/index.md).</span></span>
>
> ## <a name="questions-and-comments"></a><span data-ttu-id="ac8b3-117">问题和提出的意见</span><span class="sxs-lookup"><span data-stu-id="ac8b3-117">Questions and comments</span></span>
>
> <span data-ttu-id="ac8b3-118">请在你喜欢本教程的内容以及我们可以改进的页的底部的评论中留下反馈。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-118">Please leave feedback on how you liked this tutorial and what we could improve in the comments at the bottom of the page.</span></span> <span data-ttu-id="ac8b3-119">如果你有与本教程不直接相关的问题，你可以发布到[ASP.NET SignalR 论坛](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR)或[StackOverflow.com](http://stackoverflow.com/)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-119">If you have questions that are not directly related to the tutorial, you can post them to the [ASP.NET SignalR forum](https://forums.asp.net/1254.aspx/1?ASP+NET+SignalR) or [StackOverflow.com](http://stackoverflow.com/).</span></span>

## <a name="overview"></a><span data-ttu-id="ac8b3-120">概述</span><span class="sxs-lookup"><span data-stu-id="ac8b3-120">Overview</span></span>

<span data-ttu-id="ac8b3-121">本文档包含以下各节：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-121">This document contains the following sections:</span></span>

- [<span data-ttu-id="ac8b3-122">生成的代理和它为您的作用</span><span class="sxs-lookup"><span data-stu-id="ac8b3-122">The generated proxy and what it does for you</span></span>](#genproxy)

    - [<span data-ttu-id="ac8b3-123">何时使用生成的代理</span><span class="sxs-lookup"><span data-stu-id="ac8b3-123">When to use the generated proxy</span></span>](#cantusegenproxy)
- [<span data-ttu-id="ac8b3-124">客户端安装程序</span><span class="sxs-lookup"><span data-stu-id="ac8b3-124">Client Setup</span></span>](#clientsetup)

    - [<span data-ttu-id="ac8b3-125">如何引用动态生成的代理</span><span class="sxs-lookup"><span data-stu-id="ac8b3-125">How to reference the dynamically generated proxy</span></span>](#dynamicproxy)
    - [<span data-ttu-id="ac8b3-126">如何为 SignalR 创建物理文件生成代理</span><span class="sxs-lookup"><span data-stu-id="ac8b3-126">How to create a physical file for the SignalR generated proxy</span></span>](#manualproxy)
- [<span data-ttu-id="ac8b3-127">如何建立连接</span><span class="sxs-lookup"><span data-stu-id="ac8b3-127">How to establish a connection</span></span>](#establishconnection)

    - [<span data-ttu-id="ac8b3-128">$。 connection.hub 是相同对象的 $.hubConnection() 创建</span><span class="sxs-lookup"><span data-stu-id="ac8b3-128">$.connection.hub is the same object that $.hubConnection() creates</span></span>](#connequivalence)
    - [<span data-ttu-id="ac8b3-129">异步执行的 start 方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-129">Asynchronous execution of the start method</span></span>](#asyncstart)
- [<span data-ttu-id="ac8b3-130">如何建立跨域连接</span><span class="sxs-lookup"><span data-stu-id="ac8b3-130">How to establish a cross-domain connection</span></span>](#crossdomain)
- [<span data-ttu-id="ac8b3-131">如何配置连接</span><span class="sxs-lookup"><span data-stu-id="ac8b3-131">How to configure the connection</span></span>](#configureconnection)

    - [<span data-ttu-id="ac8b3-132">如何指定查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="ac8b3-132">How to specify query string parameters</span></span>](#querystring)
    - [<span data-ttu-id="ac8b3-133">如何指定传输方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-133">How to specify the transport method</span></span>](#transport)
- [<span data-ttu-id="ac8b3-134">如何获取代理的 Hub 类</span><span class="sxs-lookup"><span data-stu-id="ac8b3-134">How to get a proxy for a Hub class</span></span>](#getproxy)
- [<span data-ttu-id="ac8b3-135">如何定义服务器可以调用的客户端上的方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-135">How to define methods on the client that the server can call</span></span>](#callclient)
- [<span data-ttu-id="ac8b3-136">如何从客户端调用服务器方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-136">How to call server methods from the client</span></span>](#callserver)
- [<span data-ttu-id="ac8b3-137">如何处理连接生存期事件</span><span class="sxs-lookup"><span data-stu-id="ac8b3-137">How to handle connection lifetime events</span></span>](#connectionlifetime)
- [<span data-ttu-id="ac8b3-138">如何处理错误</span><span class="sxs-lookup"><span data-stu-id="ac8b3-138">How to handle errors</span></span>](#handleerrors)
- [<span data-ttu-id="ac8b3-139">如何启用客户端的日志记录</span><span class="sxs-lookup"><span data-stu-id="ac8b3-139">How to enable client-side logging</span></span>](#logging)

<span data-ttu-id="ac8b3-140">有关如何进行编程的服务器或.NET 客户端的文档，请参阅以下资源：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-140">For documentation on how to program the server or .NET clients, see the following resources:</span></span>

- [<span data-ttu-id="ac8b3-141">SignalR 中心 API 指南-服务器</span><span class="sxs-lookup"><span data-stu-id="ac8b3-141">SignalR Hubs API Guide - Server</span></span>](hubs-api-guide-server.md)
- [<span data-ttu-id="ac8b3-142">SignalR 中心 API 指南-.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="ac8b3-142">SignalR Hubs API Guide - .NET Client</span></span>](hubs-api-guide-net-client.md)

<span data-ttu-id="ac8b3-143">SignalR 2 服务器组件是仅可在.NET 4.5 上 （尽管没有 SignalR 2 上.NET 4.0 的.NET 客户端）。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-143">The SignalR 2 server component is only available on .NET 4.5 (though there is a .NET client for SignalR 2 on .NET 4.0).</span></span>

<a id="genproxy"></a>

## <a name="the-generated-proxy-and-what-it-does-for-you"></a><span data-ttu-id="ac8b3-144">生成的代理和它为您的作用</span><span class="sxs-lookup"><span data-stu-id="ac8b3-144">The generated proxy and what it does for you</span></span>

<span data-ttu-id="ac8b3-145">您可以编写 JavaScript 客户端与 SignalR 服务使用或不使用 SignalR 将为您生成的代理进行通信。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-145">You can program a JavaScript client to communicate with a SignalR service with or without a proxy that SignalR generates for you.</span></span> <span data-ttu-id="ac8b3-146">代理为你的用途是代码的简化的语法用于连接服务器调用，写入方法并在服务器上调用方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-146">What the proxy does for you is simplify the syntax of the code you use to connect, write methods that the server calls, and call methods on the server.</span></span>

<span data-ttu-id="ac8b3-147">生成的代理时编写代码来调用服务器方法，使您可以使用看起来像执行本地函数的语法： 您可以编写`serverMethod(arg1, arg2)`而不是`invoke('serverMethod', arg1, arg2)`。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-147">When you write code to call server methods, the generated proxy enables you to use syntax that looks as though you were executing a local function: you can write `serverMethod(arg1, arg2)` instead of `invoke('serverMethod', arg1, arg2)`.</span></span> <span data-ttu-id="ac8b3-148">如果您键入了错误的服务器方法名称，生成的代理语法还允许即时和可识别客户端的错误。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-148">The generated proxy syntax also enables an immediate and intelligible client-side error if you mistype a server method name.</span></span> <span data-ttu-id="ac8b3-149">如果您手动创建用于定义这些代理的文件，还可以用于编写调用服务器方法的代码获得 IntelliSense 支持。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-149">And if you manually create the file that defines the proxies, you can also get IntelliSense support for writing code that calls server methods.</span></span>

<span data-ttu-id="ac8b3-150">例如，假设在服务器上有以下 Hub 类：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-150">For example, suppose you have the following Hub class on the server:</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample1.cs?highlight=1,3,5)]

<span data-ttu-id="ac8b3-151">下面的代码示例显示用于调用什么如的 JavaScript 代码所示`NewContosoChatMessage`服务器和接收的调用的方法`addContosoChatMessageToPage`从服务器的方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-151">The following code examples show what JavaScript code looks like for invoking the `NewContosoChatMessage` method on the server and receiving invocations of the `addContosoChatMessageToPage` method from the server.</span></span>

<span data-ttu-id="ac8b3-152">**使用生成代理**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-152">**With the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample2.js?highlight=1-2,8)]

<span data-ttu-id="ac8b3-153">**不生成代理**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-153">**Without the generated proxy**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample3.js?highlight=2-3,9)]

<a id="cantusegenproxy"></a>

### <a name="when-to-use-the-generated-proxy"></a><span data-ttu-id="ac8b3-154">何时使用生成的代理</span><span class="sxs-lookup"><span data-stu-id="ac8b3-154">When to use the generated proxy</span></span>

<span data-ttu-id="ac8b3-155">如果你想要注册的服务器调用的客户端方法的多个事件处理程序，则无法使用生成的代理。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-155">If you want to register multiple event handlers for a client method that the server calls, you can't use the generated proxy.</span></span> <span data-ttu-id="ac8b3-156">否则为您可以选择使用生成的代理，或不基于您编码的首选项。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-156">Otherwise, you can choose to use the generated proxy or not based on your coding preference.</span></span> <span data-ttu-id="ac8b3-157">如果您选择不使用它，无需引用中的"signalr/中心"URL`script`在客户端代码中的元素。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-157">If you choose not to use it, you don't have to reference the "signalr/hubs" URL in a `script` element in your client code.</span></span>

<a id="clientsetup"></a>

## <a name="client-setup"></a><span data-ttu-id="ac8b3-158">客户端安装程序</span><span class="sxs-lookup"><span data-stu-id="ac8b3-158">Client setup</span></span>

<span data-ttu-id="ac8b3-159">JavaScript 客户端需要对 jQuery 和 SignalR core JavaScript 文件的引用。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-159">A JavaScript client requires references to jQuery and the SignalR core JavaScript file.</span></span> <span data-ttu-id="ac8b3-160">JQuery 版本必须是 1.6.4 或主要的更高版本，如 1.7.2、 1.8.2 或 1.9.1。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-160">The jQuery version must be 1.6.4 or major later versions, such as 1.7.2, 1.8.2, or 1.9.1.</span></span> <span data-ttu-id="ac8b3-161">如果您决定使用生成的代理，则还需要对 SignalR 生成代理的 JavaScript 文件的引用。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-161">If you decide to use the generated proxy, you also need a reference to the SignalR generated proxy JavaScript file.</span></span> <span data-ttu-id="ac8b3-162">下面的示例显示了哪些引用可能如下所示使用生成的代理的 HTML 页面中。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-162">The following example shows what the references might look like in an HTML page that uses the generated proxy.</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample4.html)]

<span data-ttu-id="ac8b3-163">必须按以下顺序包含这些引用： jQuery 在此之后，SignalR core SignalR 代理 first、 last。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-163">These references must be included in this order: jQuery first, SignalR core after that, and SignalR proxies last.</span></span>

<a id="dynamicproxy"></a>

### <a name="how-to-reference-the-dynamically-generated-proxy"></a><span data-ttu-id="ac8b3-164">如何引用动态生成的代理</span><span class="sxs-lookup"><span data-stu-id="ac8b3-164">How to reference the dynamically generated proxy</span></span>

<span data-ttu-id="ac8b3-165">在前面的示例中，对 SignalR 生成代理的引用是动态生成的 JavaScript 代码，不是指向物理文件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-165">In the preceding example, the reference to the SignalR generated proxy is to dynamically generated JavaScript code, not to a physical file.</span></span> <span data-ttu-id="ac8b3-166">SignalR 为动态代理创建的 JavaScript 代码，并提供给客户端以响应"/ signalr/中心"URL。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-166">SignalR creates the JavaScript code for the proxy on the fly and serves it to the client in response to the "/signalr/hubs" URL.</span></span> <span data-ttu-id="ac8b3-167">如果指定的 SignalR 连接不同的基 URL 中的服务器上你`MapSignalR`方法中，动态生成的代理文件的 URL 是你使用的自定义 URL"/ 中心"追加到它。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-167">If you specified a different base URL for SignalR connections on the server in your `MapSignalR` method, the URL for the dynamically generated proxy file is your custom URL with "/hubs" appended to it.</span></span>

> [!NOTE]
> <span data-ttu-id="ac8b3-168">对于 Windows 8 （Windows 应用商店） JavaScript 客户端，而不是动态生成一个使用物理代理文件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-168">For Windows 8 (Windows Store) JavaScript clients, use the physical proxy file instead of the dynamically generated one.</span></span> <span data-ttu-id="ac8b3-169">有关详细信息，请参阅[How to create for SignalR 的物理文件生成代理](#manualproxy)本主题中更高版本。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-169">For more information, see [How to create a physical file for the SignalR generated proxy](#manualproxy) later in this topic.</span></span>


<span data-ttu-id="ac8b3-170">在 ASP.NET MVC 4 或 5 Razor 视图中，使用波形符来指代将代理文件引用中的应用程序根目录：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-170">In an ASP.NET MVC 4 or 5 Razor view, use the tilde to refer to the application root in your proxy file reference:</span></span>

[!code-html[Main](hubs-api-guide-javascript-client/samples/sample5.html)]

<span data-ttu-id="ac8b3-171">有关在 MVC 5 中使用 SignalR 的详细信息，请参阅[SignalR 和 MVC 5 入门](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-171">For more information about using SignalR in MVC 5, see [Getting Started with SignalR and MVC 5](../getting-started/tutorial-getting-started-with-signalr-and-mvc.md).</span></span>

<span data-ttu-id="ac8b3-172">在 ASP.NET MVC 3 Razor 视图中，使用`Url.Content`以供代理文件参考：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-172">In an ASP.NET MVC 3 Razor view, use `Url.Content` for your proxy file reference:</span></span>

[!code-cshtml[Main](hubs-api-guide-javascript-client/samples/sample6.cshtml)]

<span data-ttu-id="ac8b3-173">在 ASP.NET Web 窗体应用程序，使用`ResolveClientUrl`代理文件引用或通过使用应用程序根目录相对路径 （开头波形符） ScriptManager 注册：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-173">In an ASP.NET Web Forms application, use `ResolveClientUrl` for your proxies file reference or register it via the ScriptManager using an app root relative path (starting with a tilde):</span></span>

[!code-aspx[Main](hubs-api-guide-javascript-client/samples/sample7.aspx)]

<span data-ttu-id="ac8b3-174">作为一般规则，用于指定将用于 CSS 或 JavaScript 文件"/ signalr/中心"URL 中使用相同的方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-174">As a general rule, use the same method for specifying the "/signalr/hubs" URL that you use for CSS or JavaScript files.</span></span> <span data-ttu-id="ac8b3-175">如果不使用波形符指定 URL，在某些情况下你的应用程序将正常工作时在 Visual Studio 中使用 IIS Express 测试，但部署到完整 IIS 时将失败并显示 404 错误。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-175">If you specify a URL without using a tilde, in some scenarios your application will work correctly when you test in Visual Studio using IIS Express but will fail with a 404 error when you deploy to full IIS.</span></span> <span data-ttu-id="ac8b3-176">有关详细信息，请参阅**解析对根级资源的引用**中[对于 ASP.NET Web 项目的 Visual Studio 中的 Web 服务器](https://msdn.microsoft.com/library/58wxa9w5.aspx)MSDN 站点上。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-176">For more information, see **Resolving References to Root-Level Resources** in [Web Servers in Visual Studio for ASP.NET Web Projects](https://msdn.microsoft.com/library/58wxa9w5.aspx) on the MSDN site.</span></span>

<span data-ttu-id="ac8b3-177">当在调试模式下，Visual Studio 2017 中运行 web 项目，如果你使用 Internet Explorer 为您的浏览器，可以看到中的代理文件**解决方案资源管理器**下**脚本**。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-177">When you run a web project in Visual Studio 2017 in debug mode, and if you use Internet Explorer as your browser, you can see the proxy file in **Solution Explorer** under **Scripts**.</span></span>

<span data-ttu-id="ac8b3-178">若要查看该文件的内容，请双击**中心**。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-178">To see the contents of the file, double-click **hubs**.</span></span> <span data-ttu-id="ac8b3-179">如果没有使用 Visual Studio 2012 或 2013年和 Internet Explorer 中，或者你不在调试模式下，还可以通过浏览到"/ signalR/中心"URL 来获取文件的内容。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-179">If you are not using Visual Studio 2012 or 2013 and Internet Explorer, or if you are not in debug mode, you can also get the contents of the file by browsing to the "/signalR/hubs" URL.</span></span> <span data-ttu-id="ac8b3-180">例如，如果您的网站在运行时`http://localhost:56699`，请转到`http://localhost:56699/SignalR/hubs`在浏览器中。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-180">For example, if your site is running at `http://localhost:56699`, go to `http://localhost:56699/SignalR/hubs` in your browser.</span></span>

<a id="manualproxy"></a>

### <a name="how-to-create-a-physical-file-for-the-signalr-generated-proxy"></a><span data-ttu-id="ac8b3-181">如何为 SignalR 创建物理文件生成代理</span><span class="sxs-lookup"><span data-stu-id="ac8b3-181">How to create a physical file for the SignalR generated proxy</span></span>

<span data-ttu-id="ac8b3-182">作为动态生成的代理的替代方法，可以创建具有代理代码的物理文件并引用该文件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-182">As an alternative to the dynamically generated proxy, you can create a physical file that has the proxy code and reference that file.</span></span> <span data-ttu-id="ac8b3-183">可能想要执行该操作，控制缓存，或者将行为，或若要获取 IntelliSense，在编码时对服务器方法的调用。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-183">You might want to do that for control over caching or bundling behavior, or to get IntelliSense when you are coding calls to server methods.</span></span>

<span data-ttu-id="ac8b3-184">若要创建代理文件，请执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-184">To create a proxy file, perform the following steps:</span></span>

1. <span data-ttu-id="ac8b3-185">安装[Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-185">Install the [Microsoft.AspNet.SignalR.Utils](https://nuget.org/packages/Microsoft.AspNet.SignalR.Utils/) NuGet package.</span></span>
2. <span data-ttu-id="ac8b3-186">打开命令提示符并浏览到*工具*SignalR.exe 文件所在的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-186">Open a command prompt and browse to the *tools* folder that contains the SignalR.exe file.</span></span> <span data-ttu-id="ac8b3-187">工具文件夹位于以下位置：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-187">The tools folder is at the following location:</span></span>

    `[your solution folder]\packages\Microsoft.AspNet.SignalR.Utils.2.1.0\tools`
3. <span data-ttu-id="ac8b3-188">输入以下命令：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-188">Enter the following command:</span></span>

    `signalr ghp /path:[path to the .dll that contains your Hub class]`

    <span data-ttu-id="ac8b3-189">通往你 *.dll*通常*bin*项目文件夹中的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-189">The path to your *.dll* is typically the *bin* folder in your project folder.</span></span>

    <span data-ttu-id="ac8b3-190">此命令创建名为的文件*server.js*所在的同一文件夹中*signalr.exe*。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-190">This command creates a file named *server.js* in the same folder as *signalr.exe*.</span></span>
4. <span data-ttu-id="ac8b3-191">将放*server.js*文件在项目中的相应文件夹中，根据应用程序中，将其重命名，并添加对它的引用来代替"signalr/中心"引用。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-191">Put the *server.js* file in an appropriate folder in your project, rename it as appropriate for your application, and add a reference to it in place of the "signalr/hubs" reference.</span></span>

<a id="establishconnection"></a>

## <a name="how-to-establish-a-connection"></a><span data-ttu-id="ac8b3-192">如何建立连接</span><span class="sxs-lookup"><span data-stu-id="ac8b3-192">How to establish a connection</span></span>

<span data-ttu-id="ac8b3-193">您可以建立连接之前，必须创建连接对象，创建代理，并注册事件处理程序可以从服务器调用的方法的。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-193">Before you can establish a connection, you have to create a connection object, create a proxy, and register event handlers for methods that can be called from the server.</span></span> <span data-ttu-id="ac8b3-194">当安装的代理和事件处理程序，通过调用建立连接`start`方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-194">When the proxy and event handlers are set up, establish the connection by calling the `start` method.</span></span>

<span data-ttu-id="ac8b3-195">如果使用生成的代理，你无需因为生成的代理代码会为你自己的代码中创建连接对象。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-195">If you are using the generated proxy, you don't have to create the connection object in your own code because the generated proxy code does it for you.</span></span>

<a id="nogenconnection"></a>

<span data-ttu-id="ac8b3-196">**（与生成的代理） 建立连接**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-196">**Establish a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample8.js?highlight=5)]

<span data-ttu-id="ac8b3-197">**建立连接 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-197">**Establish a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample9.js?highlight=1,6)]

<span data-ttu-id="ac8b3-198">示例代码使用默认值"/ signalr"连接到 SignalR 服务的 URL。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-198">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="ac8b3-199">有关如何指定不同的基 URL 的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-/signalr URL](hubs-api-guide-server.md#signalrurl)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-199">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>

<span data-ttu-id="ac8b3-200">默认情况下的中心位置是当前的服务器;如果要连接到另一台服务器，指定的 URL，然后才能调用`start`方法，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-200">By default, the hub location is the current server; if you are connecting to a different server, specify the URL before calling the `start` method, as shown in the following example:</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample10.js)]

> [!NOTE]
> <span data-ttu-id="ac8b3-201">通常情况下注册事件处理程序之前调用`start`方法以建立连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-201">Normally you register event handlers before calling the `start` method to establish the connection.</span></span> <span data-ttu-id="ac8b3-202">如果你想要建立连接后注册一些事件处理程序，您可以这样做，但您必须注册之前，调用在事件处理程序中至少一个`start`方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-202">If you want to register some event handlers after establishing the connection, you can do that, but you must register at least one of your event handler(s) before calling the `start` method.</span></span> <span data-ttu-id="ac8b3-203">原因之一就是在应用程序，可以有多个中心，但您可能不希望触发`OnConnected`如果仅要使用到其中的每个中心上的事件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-203">One reason for this is that there can be many Hubs in an application, but you wouldn't want to trigger the `OnConnected` event on every Hub if you are only going to use to one of them.</span></span> <span data-ttu-id="ac8b3-204">建立连接后，在中心的代理服务器上的客户端方法的存在是告知 SignalR 来触发`OnConnected`事件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-204">When the connection is established, the presence of a client method on a Hub's proxy is what tells SignalR to trigger the `OnConnected` event.</span></span> <span data-ttu-id="ac8b3-205">如果你未注册任何事件处理程序之前调用`start`方法中，你将能够在中心，但集线器上调用方法`OnConnected`不会调用方法并没有客户端的方法将调用从服务器。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-205">If you don't register any event handlers before calling the `start` method, you will be able to invoke methods on the Hub, but the Hub's `OnConnected` method won't be called and no client methods will be invoked from the server.</span></span>


<a id="connequivalence"></a>

### <a name="connectionhub-is-the-same-object-that-hubconnection-creates"></a><span data-ttu-id="ac8b3-206">$。 connection.hub 是相同对象的 $.hubConnection() 创建</span><span class="sxs-lookup"><span data-stu-id="ac8b3-206">$.connection.hub is the same object that $.hubConnection() creates</span></span>

<span data-ttu-id="ac8b3-207">使用生成的代理时，可以看到示例中，从`$.connection.hub`引用的连接对象。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-207">As you can see from the examples, when you use the generated proxy, `$.connection.hub` refers to the connection object.</span></span> <span data-ttu-id="ac8b3-208">这是通过调用获取的相同对象`$.hubConnection()`时不使用生成的代理。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-208">This is the same object that you get by calling `$.hubConnection()` when you aren't using the generated proxy.</span></span> <span data-ttu-id="ac8b3-209">生成的代理代码创建您的连接通过执行以下语句：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-209">The generated proxy code creates the connection for you by executing the following statement:</span></span>

![在生成的代理文件中创建的连接](hubs-api-guide-javascript-client/_static/image3.png)

<span data-ttu-id="ac8b3-211">当您使用生成的代理时，您可以执行任何操作`$.connection.hub`不使用时生成的代理可以执行与连接对象。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-211">When you're using the generated proxy, you can do anything with `$.connection.hub` that you can do with a connection object when you're not using the generated proxy.</span></span>

<a id="asyncstart"></a>

### <a name="asynchronous-execution-of-the-start-method"></a><span data-ttu-id="ac8b3-212">异步执行的 start 方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-212">Asynchronous execution of the start method</span></span>

<span data-ttu-id="ac8b3-213">`start`方法以异步方式执行。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-213">The `start` method executes asynchronously.</span></span> <span data-ttu-id="ac8b3-214">它将返回[jQuery 延迟对象](http://api.jquery.com/category/deferred-object/)，这意味着您可以通过调用方法，如添加回调函数`pipe`， `done`，和`fail`。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-214">It returns a [jQuery Deferred object](http://api.jquery.com/category/deferred-object/), which means that you can add callback functions by calling methods such as `pipe`, `done`, and `fail`.</span></span> <span data-ttu-id="ac8b3-215">如果你想要在建立连接后，执行的代码，如服务器方法调用，该代码放在回调函数中或从回调函数中调用它。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-215">If you have code that you want to execute after the connection is established, such as a call to a server method, put that code in a callback function or call it from a callback function.</span></span> <span data-ttu-id="ac8b3-216">`.done`后已建立的连接，并且任何代码，之后必须执行回调方法在`OnConnected`事件处理程序方法在服务器上的完成执行。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-216">The `.done` callback method is executed after the connection has been established, and after any code that you have in your `OnConnected` event handler method on the server finishes executing.</span></span>

<span data-ttu-id="ac8b3-217">如果将"现已连接"语句从前面的示例作为代码后的下一行`start`方法调用 (不在`.done`回调)，则`console.log`行将按如下所示执行才能建立连接，示例：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-217">If you put the "Now connected" statement from the preceding example as the next line of code after the `start` method call (not in a `.done` callback), the `console.log` line will execute before the connection is established, as shown in the following example:</span></span>

![错误的处理方式写入运行建立连接后的代码](hubs-api-guide-javascript-client/_static/image5.png)

<a id="crossdomain"></a>

## <a name="how-to-establish-a-cross-domain-connection"></a><span data-ttu-id="ac8b3-219">如何建立跨域连接</span><span class="sxs-lookup"><span data-stu-id="ac8b3-219">How to establish a cross-domain connection</span></span>

<span data-ttu-id="ac8b3-220">通常如果浏览器加载来自的页面`http://contoso.com`，在同一个域中，是在的 SignalR 连接`http://contoso.com/signalr`。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-220">Typically if the browser loads a page from `http://contoso.com`, the SignalR connection is in the same domain, at `http://contoso.com/signalr`.</span></span> <span data-ttu-id="ac8b3-221">如果从页`http://contoso.com`建立到`http://fabrikam.com/signalr`，即跨域连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-221">If the page from `http://contoso.com` makes a connection to `http://fabrikam.com/signalr`, that is a cross-domain connection.</span></span> <span data-ttu-id="ac8b3-222">出于安全原因，默认情况下将禁用跨域连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-222">For security reasons, cross-domain connections are disabled by default.</span></span>

<span data-ttu-id="ac8b3-223">在 SignalR 1.x 中的，跨域请求已由单个 EnableCrossDomain 标志控制。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-223">In SignalR 1.x, cross domain requests were controlled by a single EnableCrossDomain flag.</span></span> <span data-ttu-id="ac8b3-224">此标志控制 JSONP 和 CORS 请求。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-224">This flag controlled both JSONP and CORS requests.</span></span> <span data-ttu-id="ac8b3-225">对于更大的灵活性，所有的 CORS 支持已从 SignalR 的服务器组件 （JavaScript 客户端仍使用 CORS 通常如果检测到由浏览器支持它），以及新的 OWIN 中间件变得可用于支持这些方案。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-225">For greater flexibility, all CORS support has been removed from the server component of SignalR (JavaScript clients still use CORS normally if it is detected that the browser supports it), and new OWIN middleware has been made available to support these scenarios.</span></span>

<span data-ttu-id="ac8b3-226">JSONP 需要在客户端 （以支持旧版浏览器中的跨域请求） 上，它将需要通过设置显式启用`EnableJSONP`上`HubConfiguration`对象传递给`true`，如下所示。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-226">If JSONP is required on the client (to support cross-domain requests in older browsers), it will need to be enabled explicitly by setting `EnableJSONP` on the `HubConfiguration` object to `true`, as shown below.</span></span> <span data-ttu-id="ac8b3-227">JSONP 是默认禁用，因为它比 CORS 不太安全。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-227">JSONP is disabled by default, as it is less secure than CORS.</span></span>

<span data-ttu-id="ac8b3-228">**向项目添加 Microsoft.Owin.Cors:** 若要安装此库，请在包管理器控制台中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-228">**Adding Microsoft.Owin.Cors to your project:** To install this library, run the following command in the Package Manager Console:</span></span>

`Install-Package Microsoft.Owin.Cors`

<span data-ttu-id="ac8b3-229">此命令会将 2.1.0 添加到你的项目包的版本。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-229">This command will add the 2.1.0 version of the package to your project.</span></span>

### <a name="calling-usecors"></a><span data-ttu-id="ac8b3-230">调用 UseCors</span><span class="sxs-lookup"><span data-stu-id="ac8b3-230">Calling UseCors</span></span>

 <span data-ttu-id="ac8b3-231">下面的代码段演示如何在 SignalR 2 实现跨域的连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-231">The following code snippet demonstrates how to implement cross-domain connections in SignalR 2.</span></span>

<span data-ttu-id="ac8b3-232">**在 SignalR 2 实现跨域请求**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-232">**Implementing cross-domain requests in SignalR 2**</span></span>

<span data-ttu-id="ac8b3-233">下面的代码演示如何在 SignalR 2 项目中启用 CORS 或 JSONP。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-233">The following code demonstrates how to enable CORS or JSONP in a SignalR 2 project.</span></span> <span data-ttu-id="ac8b3-234">此代码示例使用`Map`并`RunSignalR`而不是`MapSignalR`，以便将 CORS 中间件运行仅针对需要 CORS 支持 SignalR 请求 (而不是对中指定的路径上的所有流量`MapSignalR`。)映射还用于运行针对特定的 URL 前缀，而不是整个应用程序所需的任何其他中间件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-234">This code sample uses `Map` and `RunSignalR` instead of `MapSignalR`, so that the CORS middleware runs only for the SignalR requests that require CORS support (rather than for all traffic at the path specified in `MapSignalR`.) Map can also be used for any other middleware that needs to run for a specific URL prefix, rather than for the entire application.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample11.cs)]

> [!NOTE]
>
> - <span data-ttu-id="ac8b3-235">未设置`jQuery.support.cors`为 true，在代码中。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-235">Don't set `jQuery.support.cors` to true in your code.</span></span>
>
>     ![未将 jQuery.support.cors 设置为 true](hubs-api-guide-javascript-client/_static/image7.png)
>
>     <span data-ttu-id="ac8b3-237">SignalR 处理 CORS 的使用。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-237">SignalR handles the use of CORS.</span></span> <span data-ttu-id="ac8b3-238">设置`jQuery.support.cors`以 true 禁用 JSONP，因为它会导致 SignalR 假设浏览器支持 CORS。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-238">Setting `jQuery.support.cors` to true disables JSONP because it causes SignalR to assume the browser supports CORS.</span></span>
> - <span data-ttu-id="ac8b3-239">时要连接到本地主机 URL，Internet Explorer 10 不会考虑它的跨域连接，因此应用程序将在本地处理与 IE 10 即使尚未启用跨域服务器上的连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-239">When you're connecting to a localhost URL, Internet Explorer 10 won't consider it a cross-domain connection, so the application will work locally with IE 10 even if you haven't enabled cross-domain connections on the server.</span></span>
> - <span data-ttu-id="ac8b3-240">使用的 Internet Explorer 9 的跨域连接的信息，请参阅[此 StackOverflow 线程](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-240">For information about using cross-domain connections with Internet Explorer 9, see [this StackOverflow thread](http://stackoverflow.com/questions/13573397/siganlr-ie9-cross-domain-request-dont-work).</span></span>
> - <span data-ttu-id="ac8b3-241">有关与 Chrome 使用跨域连接的信息，请参阅[此 StackOverflow 线程](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-241">For information about using cross-domain connections with Chrome, see [this StackOverflow thread](http://stackoverflow.com/questions/15467373/signalr-1-0-1-cross-domain-request-cors-with-chrome).</span></span>
> - <span data-ttu-id="ac8b3-242">示例代码使用默认值"/ signalr"连接到 SignalR 服务的 URL。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-242">The sample code uses the default "/signalr" URL to connect to your SignalR service.</span></span> <span data-ttu-id="ac8b3-243">有关如何指定不同的基 URL 的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-/signalr URL](hubs-api-guide-server.md#signalrurl)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-243">For information about how to specify a different base URL, see [ASP.NET SignalR Hubs API Guide - Server - The /signalr URL](hubs-api-guide-server.md#signalrurl).</span></span>


<a id="configureconnection"></a>

## <a name="how-to-configure-the-connection"></a><span data-ttu-id="ac8b3-244">如何配置连接</span><span class="sxs-lookup"><span data-stu-id="ac8b3-244">How to configure the connection</span></span>

<span data-ttu-id="ac8b3-245">建立连接之前，您可以指定查询字符串参数或指定的传输方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-245">Before you establish a connection, you can specify query string parameters or specify the transport method.</span></span>

<a id="querystring"></a>

### <a name="how-to-specify-query-string-parameters"></a><span data-ttu-id="ac8b3-246">如何指定查询字符串参数</span><span class="sxs-lookup"><span data-stu-id="ac8b3-246">How to specify query string parameters</span></span>

<span data-ttu-id="ac8b3-247">如果你想要将数据发送到服务器，客户端连接时，您可以将查询字符串参数添加到连接对象。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-247">If you want to send data to the server when the client connects, you can add query string parameters to the connection object.</span></span> <span data-ttu-id="ac8b3-248">以下示例演示如何在客户端代码中设置的查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-248">The following examples show how to set a query string parameter in client code.</span></span>

<span data-ttu-id="ac8b3-249">**（使用生成的代理） 调用 start 方法之前设置查询字符串值**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-249">**Set a query string value before calling the start method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample12.js?highlight=1)]

<span data-ttu-id="ac8b3-250">**在调用 start 方法 （而不生成的代理） 之前设置查询字符串值**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-250">**Set a query string value before calling the start method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample13.js?highlight=2)]

<span data-ttu-id="ac8b3-251">下面的示例演示如何读取服务器代码中的查询字符串参数。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-251">The following example shows how to read a query string parameter in server code.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample14.cs?highlight=5)]

<a id="transport"></a>

### <a name="how-to-specify-the-transport-method"></a><span data-ttu-id="ac8b3-252">如何指定传输方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-252">How to specify the transport method</span></span>

<span data-ttu-id="ac8b3-253">作为连接的过程的一部分，SignalR 客户端通常与服务器协商以确定支持的最佳传输服务器和客户端。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-253">As part of the process of connecting, a SignalR client normally negotiates with the server to determine the best transport that is supported by both server and client.</span></span> <span data-ttu-id="ac8b3-254">如果您已经知道你想要使用哪种的传输，您可以通过在调用时指定的传输方法来绕过这个协商过程`start`方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-254">If you already know which transport you want to use, you can bypass this negotiation process by specifying the transport method when you call the `start` method.</span></span>

<span data-ttu-id="ac8b3-255">**指定的传输方法 （具有生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-255">**Client code that specifies the transport method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample15.js?highlight=1)]

<span data-ttu-id="ac8b3-256">**指定的传输方法 （而不生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-256">**Client code that specifies the transport method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample16.js?highlight=2)]

<span data-ttu-id="ac8b3-257">或者，可以指定要在其中 SignalR 尝试它们的顺序多个传输方法：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-257">As an alternative, you can specify multiple transport methods in the order in which you want SignalR to try them:</span></span>

<span data-ttu-id="ac8b3-258">**指定自定义传输的回退方案 （具有生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-258">**Client code that specifies a custom transport fallback scheme (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample17.js?highlight=1)]

<span data-ttu-id="ac8b3-259">**指定自定义传输的回退方案 （而不生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-259">**Client code that specifies a custom transport fallback scheme (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample18.js?highlight=2)]

<span data-ttu-id="ac8b3-260">用于指定的传输方法，可以使用以下值：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-260">You can use the following values for specifying the transport method:</span></span>

- <span data-ttu-id="ac8b3-261">"webSockets"</span><span class="sxs-lookup"><span data-stu-id="ac8b3-261">"webSockets"</span></span>
- <span data-ttu-id="ac8b3-262">"foreverFrame"</span><span class="sxs-lookup"><span data-stu-id="ac8b3-262">"foreverFrame"</span></span>
- <span data-ttu-id="ac8b3-263">"serverSentEvents"</span><span class="sxs-lookup"><span data-stu-id="ac8b3-263">"serverSentEvents"</span></span>
- <span data-ttu-id="ac8b3-264">"longPolling"</span><span class="sxs-lookup"><span data-stu-id="ac8b3-264">"longPolling"</span></span>

<span data-ttu-id="ac8b3-265">以下示例演示如何确定哪种传输方法使用建立的连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-265">The following examples show how to find out which transport method is being used by a connection.</span></span>

<span data-ttu-id="ac8b3-266">**显示使用 （与生成的代理） 的连接的传输方法的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-266">**Client code that displays the transport method used by a connection (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample19.js?highlight=2)]

<span data-ttu-id="ac8b3-267">**显示连接 （而不生成的代理） 使用的传输方法的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-267">**Client code that displays the transport method used by a connection (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample20.js?highlight=3)]

<span data-ttu-id="ac8b3-268">有关如何检查服务器代码中的传输方法的信息，请参阅[ASP.NET SignalR 中心 API 指南-服务器-如何从上下文属性中获取有关客户端信息](hubs-api-guide-server.md#contextproperty)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-268">For information about how to check the transport method in server code, see [ASP.NET SignalR Hubs API Guide - Server - How to get information about the client from the Context property](hubs-api-guide-server.md#contextproperty).</span></span> <span data-ttu-id="ac8b3-269">有关传输和回退的详细信息，请参阅[简介 SignalR 的传输和回退](../getting-started/introduction-to-signalr.md#transports)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-269">For more information about transports and fallbacks, see [Introduction to SignalR - Transports and Fallbacks](../getting-started/introduction-to-signalr.md#transports).</span></span>

<a id="getproxy"></a>

## <a name="how-to-get-a-proxy-for-a-hub-class"></a><span data-ttu-id="ac8b3-270">如何获取代理的 Hub 类</span><span class="sxs-lookup"><span data-stu-id="ac8b3-270">How to get a proxy for a Hub class</span></span>

<span data-ttu-id="ac8b3-271">创建每个连接对象封装有关与包含一个或多个 Hub 类的 SignalR 服务的连接信息。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-271">Each connection object that you create encapsulates information about a connection to a SignalR service that contains one or more Hub classes.</span></span> <span data-ttu-id="ac8b3-272">若要与 Hub 类进行通信，你使用代理对象 （如果不使用的生成的代理），会创建您自己或为您生成。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-272">To communicate with a Hub class, you use a proxy object which you create yourself (if you're not using the generated proxy) or which is generated for you.</span></span>

<span data-ttu-id="ac8b3-273">在客户端上的代理服务器名称是中心类名称的 camel 大小写版本。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-273">On the client the proxy name is a camel-cased version of the Hub class name.</span></span> <span data-ttu-id="ac8b3-274">SignalR 自动进行此更改，以便 JavaScript 代码可以遵循 JavaScript 约定。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-274">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="ac8b3-275">**在服务器上的 hub 类**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-275">**Hub class on server**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample21.cs?highlight=1)]

<span data-ttu-id="ac8b3-276">**获取对生成的客户端代理的引用为中心**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-276">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample22.js?highlight=1)]

<span data-ttu-id="ac8b3-277">**创建 Hub 类 （而不生成的代理） 的客户端代理**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-277">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample23.cs?highlight=1)]

<span data-ttu-id="ac8b3-278">如果修饰在中心类`HubName`属性，而无需更改情况下使用的确切名称。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-278">If you decorate your Hub class with a `HubName` attribute, use the exact name without changing case.</span></span>

<span data-ttu-id="ac8b3-279">**HubName 属性与服务器上的 hub 类**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-279">**Hub class on server with HubName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample24.cs?highlight=1)]

<span data-ttu-id="ac8b3-280">**获取对生成的客户端代理的引用为中心**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-280">**Get a reference to the generated client proxy for the Hub**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample25.js?highlight=1)]

<span data-ttu-id="ac8b3-281">**创建 Hub 类 （而不生成的代理） 的客户端代理**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-281">**Create client proxy for the Hub class (without generated proxy)**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample26.cs?highlight=1)]

<a id="callclient"></a>

## <a name="how-to-define-methods-on-the-client-that-the-server-can-call"></a><span data-ttu-id="ac8b3-282">如何定义服务器可以调用的客户端上的方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-282">How to define methods on the client that the server can call</span></span>

<span data-ttu-id="ac8b3-283">若要定义服务器可以从集线器调用的方法，将添加一个事件处理程序到集线器代理通过使用`client`属性生成的代理，或调用`on`方法，如果不使用生成的代理。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-283">To define a method that the server can call from a Hub, add an event handler to the Hub proxy by using the `client` property of the generated proxy, or call the `on` method if you aren't using the generated proxy.</span></span> <span data-ttu-id="ac8b3-284">参数可以是复杂对象。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-284">The parameters can be complex objects.</span></span>

<span data-ttu-id="ac8b3-285">添加事件处理程序，然后再调用`start`方法以建立连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-285">Add the event handler before you call the `start` method to establish the connection.</span></span> <span data-ttu-id="ac8b3-286">(如果你想要添加事件处理程序之后调用`start`方法，请参阅中的说明[如何建立连接](#establishconnection)先前在此文档，并使用用于定义一种方法，而无需使用生成的代理所示的语法。)</span><span class="sxs-lookup"><span data-stu-id="ac8b3-286">(If you want to add event handlers after calling the `start` method, see the note in [How to establish a connection](#establishconnection) earlier in this document, and use the syntax shown for defining a method without using the generated proxy.)</span></span>

<span data-ttu-id="ac8b3-287">方法名称匹配不区分大小写。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-287">Method name matching is case-insensitive.</span></span> <span data-ttu-id="ac8b3-288">例如，`Clients.All.addContosoChatMessageToPage`在服务器上将执行`AddContosoChatMessageToPage`， `addContosoChatMessageToPage`，或`addcontosochatmessagetopage`客户端上。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-288">For example, `Clients.All.addContosoChatMessageToPage` on the server will execute `AddContosoChatMessageToPage`, `addContosoChatMessageToPage`, or `addcontosochatmessagetopage` on the client.</span></span>

<span data-ttu-id="ac8b3-289">**（具有生成的代理） 的客户端上定义方法**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-289">**Define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample27.js?highlight=2)]

<span data-ttu-id="ac8b3-290">**另一种方法 （具有生成的代理） 的客户端上定义方法**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-290">**Alternate way to define method on client (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample28.js?highlight=1-2)]

<span data-ttu-id="ac8b3-291">**客户端 （而无需生成的代理，或在调用 start 方法之后添加时） 上定义方法**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-291">**Define method on client (without the generated proxy, or when adding after calling the start method)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample29.js?highlight=3)]

<span data-ttu-id="ac8b3-292">**调用客户端方法的服务器代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-292">**Server code that calls the client method**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample30.cs?highlight=5)]

<span data-ttu-id="ac8b3-293">下面的示例包括复杂对象作为方法参数。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-293">The following examples include a complex object as a method parameter.</span></span>

<span data-ttu-id="ac8b3-294">**采用 （具有生成的代理） 的复杂对象的客户端上定义方法**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-294">**Define method on client that takes a complex object (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample31.js?highlight=2-3)]

<span data-ttu-id="ac8b3-295">**使用复杂对象 （而不生成的代理） 的客户端上定义方法**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-295">**Define method on client that takes a complex object (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample32.js?highlight=3-4)]

<span data-ttu-id="ac8b3-296">**定义的复杂对象的服务器代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-296">**Server code that defines the complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample33.cs?highlight=1)]

<span data-ttu-id="ac8b3-297">**使用复杂对象的客户端方法调用的服务器代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-297">**Server code that calls the client method using a complex object**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample34.cs?highlight=3)]

<a id="callserver"></a>

## <a name="how-to-call-server-methods-from-the-client"></a><span data-ttu-id="ac8b3-298">如何从客户端调用服务器方法</span><span class="sxs-lookup"><span data-stu-id="ac8b3-298">How to call server methods from the client</span></span>

<span data-ttu-id="ac8b3-299">若要从客户端调用服务器方法，使用`server`属性生成的代理或`invoke`中心代理，如果不使用生成的代理上的方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-299">To call a server method from the client, use the `server` property of the generated proxy or the `invoke` method on the Hub proxy if you aren't using the generated proxy.</span></span> <span data-ttu-id="ac8b3-300">返回值或参数可能是复杂对象。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-300">The return value or parameters can be complex objects.</span></span>

<span data-ttu-id="ac8b3-301">在该中心传入方法名称的 camel 大小写格式版本。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-301">Pass in a camel-case version of the method name on the Hub.</span></span> <span data-ttu-id="ac8b3-302">SignalR 自动进行此更改，以便 JavaScript 代码可以遵循 JavaScript 约定。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-302">SignalR automatically makes this change so that JavaScript code can conform to JavaScript conventions.</span></span>

<span data-ttu-id="ac8b3-303">以下示例演示如何调用服务器方法没有返回值以及如何调用服务器方法没有返回值。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-303">The following examples show how to call a server method that doesn't have a return value and how to call a server method that does have a return value.</span></span>

<span data-ttu-id="ac8b3-304">**服务器没有 HubMethodName 属性方法**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-304">**Server method with no HubMethodName attribute**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample35.cs?highlight=3)]

<span data-ttu-id="ac8b3-305">**在参数中传递的定义的复杂对象的服务器代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-305">**Server code that defines the complex object passed in a parameter**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample36.cs)]

<span data-ttu-id="ac8b3-306">**调用服务器方法 （包含生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-306">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample37.js?highlight=1)]

<span data-ttu-id="ac8b3-307">**客户端代码调用服务器方法 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-307">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample38.js?highlight=1)]

<span data-ttu-id="ac8b3-308">如果修饰中心方法替换`HubMethodName`属性，而无需更改情况下使用该名称。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-308">If you decorated the Hub method with a `HubMethodName` attribute, use that name without changing case.</span></span>

<span data-ttu-id="ac8b3-309">**服务器方法**HubMethodName 属性</span><span class="sxs-lookup"><span data-stu-id="ac8b3-309">**Server method** with a HubMethodName attribute</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample39.cs?highlight=3)]

<span data-ttu-id="ac8b3-310">**调用服务器方法 （包含生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-310">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample40.js?highlight=1)]

<span data-ttu-id="ac8b3-311">**客户端代码调用服务器方法 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-311">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample41.js?highlight=1)]

<span data-ttu-id="ac8b3-312">前面的示例说明如何调用没有返回值的服务器方法。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-312">The preceding examples show how to call a server method that has no return value.</span></span> <span data-ttu-id="ac8b3-313">以下示例演示如何调用服务器方法具有返回值。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-313">The following examples show how to call a server method that has a return value.</span></span>

<span data-ttu-id="ac8b3-314">**服务器代码的方法的返回值**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-314">**Server code for a method that has a return value**</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample42.cs?highlight=3)]

<span data-ttu-id="ac8b3-315">**Stock 类用于**返回值</span><span class="sxs-lookup"><span data-stu-id="ac8b3-315">**The Stock class used for the** return value</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample43.cs?highlight=1)]

<span data-ttu-id="ac8b3-316">**调用服务器方法 （包含生成的代理） 的客户端代码**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-316">**Client code that invokes the server method (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample44.js?highlight=2,4-5)]

<span data-ttu-id="ac8b3-317">**客户端代码调用服务器方法 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-317">**Client code that invokes the server method (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample45.js?highlight=2,4-5)]

<a id="connectionlifetime"></a>

## <a name="how-to-handle-connection-lifetime-events"></a><span data-ttu-id="ac8b3-318">如何处理连接生存期事件</span><span class="sxs-lookup"><span data-stu-id="ac8b3-318">How to handle connection lifetime events</span></span>

<span data-ttu-id="ac8b3-319">SignalR 提供了以下连接可以处理的生存期事件：</span><span class="sxs-lookup"><span data-stu-id="ac8b3-319">SignalR provides the following connection lifetime events that you can handle:</span></span>

- <span data-ttu-id="ac8b3-320">`starting`：通过连接发送任何数据之前引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-320">`starting`: Raised before any data is sent over the connection.</span></span>
- <span data-ttu-id="ac8b3-321">`received`：在连接上接收到任何数据时引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-321">`received`: Raised when any data is received on the connection.</span></span> <span data-ttu-id="ac8b3-322">提供接收到的数据。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-322">Provides the received data.</span></span>
- <span data-ttu-id="ac8b3-323">`connectionSlow`：当客户端检测到慢速或频繁删除连接时引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-323">`connectionSlow`: Raised when the client detects a slow or frequently dropping connection.</span></span>
- <span data-ttu-id="ac8b3-324">`reconnecting`：基础传输开始重新连接时引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-324">`reconnecting`: Raised when the underlying transport begins reconnecting.</span></span>
- <span data-ttu-id="ac8b3-325">`reconnected`：当基础传输已重新连接时引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-325">`reconnected`: Raised when the underlying transport has reconnected.</span></span>
- <span data-ttu-id="ac8b3-326">`stateChanged`：连接状态更改时引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-326">`stateChanged`: Raised when the connection state changes.</span></span> <span data-ttu-id="ac8b3-327">提供的旧状态和新的状态 （连接、 已连接、 正在重新连接或已断开连接）。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-327">Provides the old state and the new state (Connecting, Connected, Reconnecting, or Disconnected).</span></span>
- <span data-ttu-id="ac8b3-328">`disconnected`：当连接已断开连接时引发。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-328">`disconnected`: Raised when the connection has disconnected.</span></span>

<span data-ttu-id="ac8b3-329">例如，如果你想要有可能会导致明显延迟的连接问题时显示警告消息，处理`connectionSlow`事件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-329">For example, if you want to display warning messages when there are connection problems that might cause noticeable delays, handle the `connectionSlow` event.</span></span>

<span data-ttu-id="ac8b3-330">**句柄 （具有生成的代理） 的 connectionSlow 事件**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-330">**Handle the connectionSlow event (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample46.js?highlight=1)]

<span data-ttu-id="ac8b3-331">**处理 connectionSlow 事件 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-331">**Handle the connectionSlow event (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample47.js?highlight=2)]

<span data-ttu-id="ac8b3-332">有关详细信息，请参阅[理解和 SignalR 中的处理连接生存期事件](handling-connection-lifetime-events.md)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-332">For more information, see [Understanding and Handling Connection Lifetime Events in SignalR](handling-connection-lifetime-events.md).</span></span>

<a id="handleerrors"></a>

## <a name="how-to-handle-errors"></a><span data-ttu-id="ac8b3-333">如何处理错误</span><span class="sxs-lookup"><span data-stu-id="ac8b3-333">How to handle errors</span></span>

<span data-ttu-id="ac8b3-334">SignalR JavaScript 客户端提供了`error`可以添加的处理程序的事件。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-334">The SignalR JavaScript client provides an `error` event that you can add a handler for.</span></span> <span data-ttu-id="ac8b3-335">失败方法还可用于添加处理程序从服务器方法调用导致的错误。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-335">You can also use the fail method to add a handler for errors that result from a server method invocation.</span></span>

<span data-ttu-id="ac8b3-336">如果未显式启用服务器上的详细的错误消息，SignalR 返回出现错误后的异常对象包含有关错误的最少信息。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-336">If you don't explicitly enable detailed error messages on the server, the exception object that SignalR returns after an error contains minimal information about the error.</span></span> <span data-ttu-id="ac8b3-337">例如，如果调用`newContosoChatMessage`失败，错误对象中的错误消息包含"`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`"发送到在生产环境中的客户端的详细的错误消息不建议出于安全原因，但如果你想要启用详细的错误消息在服务器上进行故障排除，使用下面的代码。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-337">For example, if a call to `newContosoChatMessage` fails, the error message in the error object contains "`There was an error invoking Hub method 'contosoChatHub.newContosoChatMessage'.`" Sending detailed error messages to clients in production is not recommended for security reasons, but if you want to enable detailed error messages for troubleshooting purposes, use the following code on the server.</span></span>

[!code-csharp[Main](hubs-api-guide-javascript-client/samples/sample48.cs?highlight=2)]

<span data-ttu-id="ac8b3-338">下面的示例演示如何添加错误事件的处理程序。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-338">The following example shows how to add a handler for the error event.</span></span>

<span data-ttu-id="ac8b3-339">**添加错误处理程序 （具有生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-339">**Add an error handler (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample49.js?highlight=1)]

<span data-ttu-id="ac8b3-340">**添加错误处理程序 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-340">**Add an error handler (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample50.js?highlight=2)]

<span data-ttu-id="ac8b3-341">下面的示例演示如何处理来自方法调用的错误。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-341">The following example shows how to handle an error from a method invocation.</span></span>

<span data-ttu-id="ac8b3-342">**处理来自方法调用 （与生成的代理） 的错误**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-342">**Handle an error from a method invocation (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample51.js?highlight=2)]

<span data-ttu-id="ac8b3-343">**处理从方法调用 （而不生成的代理） 错误**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-343">**Handle an error from a method invocation (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample52.js?highlight=2)]

<span data-ttu-id="ac8b3-344">如果方法调用失败，`error`也会引发事件，因此你的代码中`error`方法处理程序并在`.fail`方法回调会执行。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-344">If a method invocation fails, the `error` event is also raised, so your code in the `error` method handler and in the `.fail` method callback would execute.</span></span>

<a id="logging"></a>

## <a name="how-to-enable-client-side-logging"></a><span data-ttu-id="ac8b3-345">如何启用客户端的日志记录</span><span class="sxs-lookup"><span data-stu-id="ac8b3-345">How to enable client-side logging</span></span>

<span data-ttu-id="ac8b3-346">若要启用客户端的日志记录的连接上，设置`logging`属性对 connection 对象，然后再调用`start`方法以建立连接。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-346">To enable client-side logging on a connection, set the `logging` property on the connection object before you call the `start` method to establish the connection.</span></span>

<span data-ttu-id="ac8b3-347">**（使用生成的代理） 启用日志记录**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-347">**Enable logging (with the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample53.js?highlight=1)]

<span data-ttu-id="ac8b3-348">**启用日志记录 （而不生成的代理）**</span><span class="sxs-lookup"><span data-stu-id="ac8b3-348">**Enable logging (without the generated proxy)**</span></span>

[!code-javascript[Main](hubs-api-guide-javascript-client/samples/sample54.js?highlight=2)]

<span data-ttu-id="ac8b3-349">若要查看日志，请打开浏览器的开发人员工具，并转到控制台选项卡。本教程介绍的分步说明和屏幕截图显示如何执行此操作，请参阅[服务器与 ASP.NET Signalr-启用日志记录广播](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging)。</span><span class="sxs-lookup"><span data-stu-id="ac8b3-349">To see the logs, open your browser's developer tools and go to the Console tab. For a tutorial that shows step-by-step instructions and screen shots that show how to do this, see [Server Broadcast with ASP.NET Signalr - Enable Logging](../getting-started/tutorial-server-broadcast-with-signalr.md#enable-logging).</span></span>
