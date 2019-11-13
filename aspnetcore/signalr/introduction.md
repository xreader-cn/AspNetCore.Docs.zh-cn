---
title: ASP.NET Core 简介 SignalR
author: bradygaster
description: 了解 ASP.NET Core SignalR 库如何简化将实时功能添加到应用程序。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2019
no-loc:
- SignalR
uid: signalr/introduction
ms.openlocfilehash: 7108d9f223db78937dd1203a1cb4b890006b20ec
ms.sourcegitcommit: 3fc3020961e1289ee5bf5f3c365ce8304d8ebf19
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/12/2019
ms.locfileid: "73963941"
---
# <a name="introduction-to-aspnet-core-opno-locsignalr"></a><span data-ttu-id="338a1-103">ASP.NET Core 简介 SignalR</span><span class="sxs-lookup"><span data-stu-id="338a1-103">Introduction to ASP.NET Core SignalR</span></span>

## <a name="what-is-opno-locsignalr"></a><span data-ttu-id="338a1-104">什么是 SignalR？</span><span class="sxs-lookup"><span data-stu-id="338a1-104">What is SignalR?</span></span>

<span data-ttu-id="338a1-105">ASP.NET Core SignalR 是一个开源库，它简化了向应用程序添加实时 web 功能的功能。</span><span class="sxs-lookup"><span data-stu-id="338a1-105">ASP.NET Core SignalR is an open-source library that simplifies adding real-time web functionality to apps.</span></span> <span data-ttu-id="338a1-106">实时 web 功能使服务器端代码可以立即将内容推送到客户端。</span><span class="sxs-lookup"><span data-stu-id="338a1-106">Real-time web functionality enables server-side code to push content to clients instantly.</span></span>

<span data-ttu-id="338a1-107">适用于 SignalR的候选项：</span><span class="sxs-lookup"><span data-stu-id="338a1-107">Good candidates for SignalR:</span></span>

* <span data-ttu-id="338a1-108">需要从服务器进行高频率更新的应用。</span><span class="sxs-lookup"><span data-stu-id="338a1-108">Apps that require high frequency updates from the server.</span></span> <span data-ttu-id="338a1-109">例如，游戏、社交网络、投票、拍卖、地图和 GPS 应用。</span><span class="sxs-lookup"><span data-stu-id="338a1-109">Examples are gaming, social networks, voting, auction, maps, and GPS apps.</span></span>
* <span data-ttu-id="338a1-110">仪表板和监视应用。</span><span class="sxs-lookup"><span data-stu-id="338a1-110">Dashboards and monitoring apps.</span></span> <span data-ttu-id="338a1-111">示例包括公司仪表板、即时销售更新或旅行警报。</span><span class="sxs-lookup"><span data-stu-id="338a1-111">Examples include company dashboards, instant sales updates, or travel alerts.</span></span>
* <span data-ttu-id="338a1-112">协作应用。</span><span class="sxs-lookup"><span data-stu-id="338a1-112">Collaborative apps.</span></span> <span data-ttu-id="338a1-113">白板应用和团队会议软件是协作应用的示例。</span><span class="sxs-lookup"><span data-stu-id="338a1-113">Whiteboard apps and team meeting software are examples of collaborative apps.</span></span>
* <span data-ttu-id="338a1-114">需要通知的应用程序。</span><span class="sxs-lookup"><span data-stu-id="338a1-114">Apps that require notifications.</span></span> <span data-ttu-id="338a1-115">社交网络、电子邮件、聊天、游戏、旅行警报以及许多其他应用使用通知。</span><span class="sxs-lookup"><span data-stu-id="338a1-115">Social networks, email, chat, games, travel alerts, and many other apps use notifications.</span></span>

SignalR<span data-ttu-id="338a1-116"> 提供用于创建服务器到客户端[远程过程调用（RPC）](https://wikipedia.org/wiki/Remote_procedure_call)的 API。</span><span class="sxs-lookup"><span data-stu-id="338a1-116"> provides an API for creating server-to-client [remote procedure calls (RPC)](https://wikipedia.org/wiki/Remote_procedure_call).</span></span> <span data-ttu-id="338a1-117">Rpc 通过服务器端 .NET Core 代码从客户端调用 JavaScript 函数。</span><span class="sxs-lookup"><span data-stu-id="338a1-117">The RPCs call JavaScript functions on clients from server-side .NET Core code.</span></span>

<span data-ttu-id="338a1-118">下面是 ASP.NET Core SignalR 的一些功能：</span><span class="sxs-lookup"><span data-stu-id="338a1-118">Here are some features of SignalR for ASP.NET Core:</span></span>

* <span data-ttu-id="338a1-119">自动处理连接管理。</span><span class="sxs-lookup"><span data-stu-id="338a1-119">Handles connection management automatically.</span></span>
* <span data-ttu-id="338a1-120">将消息同时发送到所有连接的客户端。</span><span class="sxs-lookup"><span data-stu-id="338a1-120">Sends messages to all connected clients simultaneously.</span></span> <span data-ttu-id="338a1-121">例如，聊天室。</span><span class="sxs-lookup"><span data-stu-id="338a1-121">For example, a chat room.</span></span>
* <span data-ttu-id="338a1-122">向特定客户端或客户端组发送消息。</span><span class="sxs-lookup"><span data-stu-id="338a1-122">Sends messages to specific clients or groups of clients.</span></span>
* <span data-ttu-id="338a1-123">可缩放以处理不断增加的流量。</span><span class="sxs-lookup"><span data-stu-id="338a1-123">Scales to handle increasing traffic.</span></span>

<span data-ttu-id="338a1-124">源托管在[GitHub 上的SignalR 存储库](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR)中。</span><span class="sxs-lookup"><span data-stu-id="338a1-124">The source is hosted in a [SignalR repository on GitHub](https://github.com/aspnet/AspNetCore/tree/master/src/SignalR).</span></span>

## <a name="transports"></a><span data-ttu-id="338a1-125">传输</span><span class="sxs-lookup"><span data-stu-id="338a1-125">Transports</span></span>

SignalR<span data-ttu-id="338a1-126"> 支持多种用于处理实时通信的技术：</span><span class="sxs-lookup"><span data-stu-id="338a1-126"> supports several techniques for handling real-time communications:</span></span>

* [<span data-ttu-id="338a1-127">WebSockets</span><span class="sxs-lookup"><span data-stu-id="338a1-127">WebSockets</span></span>](https://tools.ietf.org/html/rfc7118)
* <span data-ttu-id="338a1-128">服务器发送的事件</span><span class="sxs-lookup"><span data-stu-id="338a1-128">Server-Sent Events</span></span>
* <span data-ttu-id="338a1-129">长轮询</span><span class="sxs-lookup"><span data-stu-id="338a1-129">Long Polling</span></span>

SignalR<span data-ttu-id="338a1-130"> 会自动选择服务器和客户端功能内的最佳传输方法。</span><span class="sxs-lookup"><span data-stu-id="338a1-130"> automatically chooses the best transport method that is within the capabilities of the server and client.</span></span>

## <a name="hubs"></a><span data-ttu-id="338a1-131">中心</span><span class="sxs-lookup"><span data-stu-id="338a1-131">Hubs</span></span>

SignalR<span data-ttu-id="338a1-132"> 使用*集线器*在客户端和服务器之间进行通信。</span><span class="sxs-lookup"><span data-stu-id="338a1-132"> uses *hubs* to communicate between clients and servers.</span></span>

<span data-ttu-id="338a1-133">中心是一种高级管道，它允许客户端和服务器分别调用方法。</span><span class="sxs-lookup"><span data-stu-id="338a1-133">A hub is a high-level pipeline that allows a client and server to call methods on each other.</span></span> SignalR<span data-ttu-id="338a1-134"> 会自动处理计算机边界中的调度，从而允许客户端在服务器上调用方法，反之亦然。</span><span class="sxs-lookup"><span data-stu-id="338a1-134"> handles the dispatching across machine boundaries automatically, allowing clients to call methods on the server and vice versa.</span></span> <span data-ttu-id="338a1-135">可以将强类型参数传递给方法，从而启用模型绑定。</span><span class="sxs-lookup"><span data-stu-id="338a1-135">You can pass strongly-typed parameters to methods, which enables model binding.</span></span> SignalR<span data-ttu-id="338a1-136"> 提供了两种内置的集线器协议：基于 JSON 的文本协议和基于[MessagePack](https://msgpack.org/)的二进制协议。</span><span class="sxs-lookup"><span data-stu-id="338a1-136"> provides two built-in hub protocols: a text protocol based on JSON and a binary protocol based on [MessagePack](https://msgpack.org/).</span></span>  <span data-ttu-id="338a1-137">与 JSON 相比，MessagePack 通常会创建较小的消息。</span><span class="sxs-lookup"><span data-stu-id="338a1-137">MessagePack generally creates smaller messages compared to JSON.</span></span> <span data-ttu-id="338a1-138">较早的浏览器必须支持[XHR 级别 2](https://caniuse.com/#feat=xhr2) ，才能提供 MessagePack 协议支持。</span><span class="sxs-lookup"><span data-stu-id="338a1-138">Older browsers must support [XHR level 2](https://caniuse.com/#feat=xhr2) to provide MessagePack protocol support.</span></span>

<span data-ttu-id="338a1-139">中心通过发送包含客户端方法的名称和参数的消息来调用客户端代码。</span><span class="sxs-lookup"><span data-stu-id="338a1-139">Hubs call client-side code by sending messages that contain the name and parameters of the client-side method.</span></span> <span data-ttu-id="338a1-140">作为方法参数发送的对象将使用配置的协议进行反序列化。</span><span class="sxs-lookup"><span data-stu-id="338a1-140">Objects sent as method parameters are deserialized using the configured protocol.</span></span> <span data-ttu-id="338a1-141">客户端尝试将名称与客户端代码中的方法匹配。</span><span class="sxs-lookup"><span data-stu-id="338a1-141">The client tries to match the name to a method in the client-side code.</span></span> <span data-ttu-id="338a1-142">当客户端找到匹配项时，它将调用方法并向其传递反序列化的参数数据。</span><span class="sxs-lookup"><span data-stu-id="338a1-142">When the client finds a match, it calls the method and passes to it the deserialized parameter data.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="338a1-143">其他资源</span><span class="sxs-lookup"><span data-stu-id="338a1-143">Additional resources</span></span>

* <span data-ttu-id="338a1-144">[ASP.NET Core 的 SignalR 入门](xref:tutorials/signalr)</span><span class="sxs-lookup"><span data-stu-id="338a1-144">[Get started with SignalR for ASP.NET Core](xref:tutorials/signalr)</span></span>
* [<span data-ttu-id="338a1-145">支持的平台</span><span class="sxs-lookup"><span data-stu-id="338a1-145">Supported Platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="338a1-146">中心</span><span class="sxs-lookup"><span data-stu-id="338a1-146">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="338a1-147">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="338a1-147">JavaScript client</span></span>](xref:signalr/javascript-client)
