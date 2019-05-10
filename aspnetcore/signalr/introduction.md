---
title: ASP.NET Core SignalR 简介
author: bradygaster
description: 了解如何 ASP.NET Core SignalR 库简化了将实时功能添加到应用。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/25/2018
uid: signalr/introduction
ms.openlocfilehash: 673efafce60dfa46cb99f9537fda2bca42bf9822
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892244"
---
# <a name="introduction-to-aspnet-core-signalr"></a><span data-ttu-id="53b75-103">ASP.NET Core SignalR 简介</span><span class="sxs-lookup"><span data-stu-id="53b75-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-signalr"></a><span data-ttu-id="53b75-104">SignalR 是什么？</span><span class="sxs-lookup"><span data-stu-id="53b75-104">What is SignalR?</span></span>

<span data-ttu-id="53b75-105">ASP.NET Core SignalR 是一个开源代码库，它简化了向应用添加实时 Web 功能的过程。</span><span class="sxs-lookup"><span data-stu-id="53b75-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="53b75-106">实时 Web 功能使服务器端代码能够即时将内容推送到客户端。</span><span class="sxs-lookup"><span data-stu-id="53b75-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="53b75-107">SignalR 的适用对象：</span><span class="sxs-lookup"><span data-stu-id="53b75-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="53b75-108">需要来自服务器的高频率更新的应用。</span><span class="sxs-lookup"><span data-stu-id="53b75-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="53b75-109">例如：游戏、社交网络、投票、拍卖、地图和 GPS 应用。</span><span class="sxs-lookup"><span data-stu-id="53b75-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="53b75-110">仪表板和监视应用。</span><span class="sxs-lookup"><span data-stu-id="53b75-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="53b75-111">示例包括公司仪表板、销售状态即时更新或行程警示。</span><span class="sxs-lookup"><span data-stu-id="53b75-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="53b75-112">协作应用。</span><span class="sxs-lookup"><span data-stu-id="53b75-112">Collaborative apps.</span></span> <span data-ttu-id="53b75-113">协作应用的示例包括白板应用和团队会议软件。</span><span class="sxs-lookup"><span data-stu-id="53b75-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="53b75-114">需要通知的应用。</span><span class="sxs-lookup"><span data-stu-id="53b75-114">Apps that require notifications.</span></span> <span data-ttu-id="53b75-115">社交网络、电子邮件、聊天、游戏、行程警示以及许多其他应用都使用通知。</span><span class="sxs-lookup"><span data-stu-id="53b75-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

<span data-ttu-id="53b75-116">SignalR 提供了一个用于创建服务器到客户端[远程过程调用（RPC）](https://wikipedia.org/wiki/Remote_procedure_call)的 API。</span><span class="sxs-lookup"><span data-stu-id="53b75-116">SignalR provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="53b75-117">RPC 通过服务器端 .NET Core 代码调用客户端上的 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="53b75-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="53b75-118">以下是 ASP.NET Core SignalR 的一些功能：</span><span class="sxs-lookup"><span data-stu-id="53b75-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="53b75-119">自动管理连接。</span><span class="sxs-lookup"><span data-stu-id="53b75-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="53b75-120">同时向所有连接的客户端发送消息。</span><span class="sxs-lookup"><span data-stu-id="53b75-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="53b75-121">例如，聊天室。</span><span class="sxs-lookup"><span data-stu-id="53b75-121">For example, a chat room.</span></span>
* <span data-ttu-id="53b75-122">将消息发送到特定的客户端或客户端组。</span><span class="sxs-lookup"><span data-stu-id="53b75-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="53b75-123">扩展以处理增加的流量。</span><span class="sxs-lookup"><span data-stu-id="53b75-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="53b75-124">源代码托管在 [GitHub 上的 SignalR 存储库](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)中。</span><span class="sxs-lookup"><span data-stu-id="53b75-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="53b75-125">传输</span><span class="sxs-lookup"><span data-stu-id="53b75-125">Transports</span></span>

<span data-ttu-id="53b75-126">SignalR 支持几种方法用于处理实时通信：</span><span class="sxs-lookup"><span data-stu-id="53b75-126">SignalR supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="53b75-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="53b75-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="53b75-128">服务器发送事件</span><span class="sxs-lookup"><span data-stu-id="53b75-128">Server-Sent Events</span></span>
* <span data-ttu-id="53b75-129">长轮询</span><span class="sxs-lookup"><span data-stu-id="53b75-129">Long Polling</span></span>

<span data-ttu-id="53b75-130">SignalR 会从服务器和客户端支持的功能中自动选择最佳传输方法</span><span class="sxs-lookup"><span data-stu-id="53b75-130">SignalR automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="53b75-131">中心</span><span class="sxs-lookup"><span data-stu-id="53b75-131">Hubs</span></span>

<span data-ttu-id="53b75-132">SignalR 使用*中心*在客户端和服务器之间进行通信。</span><span class="sxs-lookup"><span data-stu-id="53b75-132">SignalR uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="53b75-133">“中心”是一种高级管道，允许客户端和服务器相互调用方法。</span><span class="sxs-lookup"><span data-stu-id="53b75-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> <span data-ttu-id="53b75-134">SignalR 自动处理跨计算机边界的调度，允许客户端和服务器相互调用方法。</span><span class="sxs-lookup"><span data-stu-id="53b75-134">SignalR handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="53b75-135">可以将强类型参数传递给方法，从而启用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="53b75-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> <span data-ttu-id="53b75-136">SignalR 提供两个内置中心协议：基于 JSON 的文本协议和基于 [MessagePack](https://msgpack.org/) 的二进制协议。</span><span class="sxs-lookup"><span data-stu-id="53b75-136">SignalR provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="53b75-137">与 JSON 相比，MessagePack 创建的消息通常比较小。</span><span class="sxs-lookup"><span data-stu-id="53b75-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="53b75-138">旧版浏览器必须支持 [XHR 2](https://caniuse.com/#feat=xhr2) 才能提供 MessagePack 协议支持。</span><span class="sxs-lookup"><span data-stu-id="53b75-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="53b75-139">中心通过发送包含客户端方法的名称和参数的消息来调用客户端代码。</span><span class="sxs-lookup"><span data-stu-id="53b75-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="53b75-140">使用配置的协议对作为方法参数发送的对象进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="53b75-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="53b75-141">客户端会尝试将方法名称与客户端代码中的方法匹配。</span><span class="sxs-lookup"><span data-stu-id="53b75-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="53b75-142">当客户端找到匹配项时，它会调用该方法并将反序列化的参数数据传递给它。</span><span class="sxs-lookup"><span data-stu-id="53b75-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="53b75-143">其他资源</span><span class="sxs-lookup"><span data-stu-id="53b75-143">Additional resources</span></span>

* [<span data-ttu-id="53b75-144">开始使用 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="53b75-144">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="53b75-145">支持的平台</span><span class="sxs-lookup"><span data-stu-id="53b75-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="53b75-146">中心</span><span class="sxs-lookup"><span data-stu-id="53b75-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="53b75-147">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="53b75-147">JavaScript client</span></span>](xref:signalr/javascript-client)
