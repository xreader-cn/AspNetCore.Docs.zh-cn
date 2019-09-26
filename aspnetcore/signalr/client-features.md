---
title: SignalR 客户端功能
author: bradygaster
description: 了解各种 ASP.NET Core SignalR 客户端支持的功能。
monikerRange: '>= aspnetcore-3.0'
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 2d6759a5484c37aee6db3d22b3127414231605ae
ms.sourcegitcommit: 14b25156e34c82ed0495b4aff5776ac5b1950b5e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301191"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="94153-103">ASP.NET Core SignalR 客户端功能</span><span class="sxs-lookup"><span data-stu-id="94153-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="94153-104">功能分发</span><span class="sxs-lookup"><span data-stu-id="94153-104">Feature distribution</span></span>

<span data-ttu-id="94153-105">下表显示提供实时支持的客户端的功能和支持。</span><span class="sxs-lookup"><span data-stu-id="94153-105">The table below shows the features and support for the clients that offer real-time support.</span></span>

| <span data-ttu-id="94153-106">功能</span><span class="sxs-lookup"><span data-stu-id="94153-106">Feature</span></span> | <span data-ttu-id="94153-107">.NET</span><span class="sxs-lookup"><span data-stu-id="94153-107">.NET</span></span> | <span data-ttu-id="94153-108">JavaScript</span><span class="sxs-lookup"><span data-stu-id="94153-108">JavaScript</span></span> | <span data-ttu-id="94153-109">Java</span><span class="sxs-lookup"><span data-stu-id="94153-109">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="94153-110">Azure SignalR 服务支持</span><span class="sxs-lookup"><span data-stu-id="94153-110">Azure SignalR Service Support</span></span> |<span data-ttu-id="94153-111">✔</span><span class="sxs-lookup"><span data-stu-id="94153-111">✔</span></span>|<span data-ttu-id="94153-112">✔</span><span class="sxs-lookup"><span data-stu-id="94153-112">✔</span></span>|<span data-ttu-id="94153-113">✔</span><span class="sxs-lookup"><span data-stu-id="94153-113">✔</span></span>|
| [<span data-ttu-id="94153-114">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="94153-114">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="94153-115">✔</span><span class="sxs-lookup"><span data-stu-id="94153-115">✔</span></span>|<span data-ttu-id="94153-116">✔</span><span class="sxs-lookup"><span data-stu-id="94153-116">✔</span></span>|<span data-ttu-id="94153-117">✔</span><span class="sxs-lookup"><span data-stu-id="94153-117">✔</span></span>|
| [<span data-ttu-id="94153-118">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="94153-118">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="94153-119">✔</span><span class="sxs-lookup"><span data-stu-id="94153-119">✔</span></span>|<span data-ttu-id="94153-120">✔</span><span class="sxs-lookup"><span data-stu-id="94153-120">✔</span></span>|<span data-ttu-id="94153-121">✔</span><span class="sxs-lookup"><span data-stu-id="94153-121">✔</span></span>|
| <span data-ttu-id="94153-122">自动重新连接（[.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)， [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients)）</span><span class="sxs-lookup"><span data-stu-id="94153-122">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="94153-123">✔</span><span class="sxs-lookup"><span data-stu-id="94153-123">✔</span></span>|<span data-ttu-id="94153-124">✔</span><span class="sxs-lookup"><span data-stu-id="94153-124">✔</span></span>| |
| <span data-ttu-id="94153-125">Websocket 传输</span><span class="sxs-lookup"><span data-stu-id="94153-125">WebSockets Transport</span></span> |<span data-ttu-id="94153-126">✔</span><span class="sxs-lookup"><span data-stu-id="94153-126">✔</span></span>|<span data-ttu-id="94153-127">✔</span><span class="sxs-lookup"><span data-stu-id="94153-127">✔</span></span>|<span data-ttu-id="94153-128">✔</span><span class="sxs-lookup"><span data-stu-id="94153-128">✔</span></span>|
| <span data-ttu-id="94153-129">服务器发送的事件传输</span><span class="sxs-lookup"><span data-stu-id="94153-129">Server-Sent Events Transport</span></span> |<span data-ttu-id="94153-130">✔</span><span class="sxs-lookup"><span data-stu-id="94153-130">✔</span></span>|<span data-ttu-id="94153-131">✔</span><span class="sxs-lookup"><span data-stu-id="94153-131">✔</span></span>| |
| <span data-ttu-id="94153-132">长轮询传输</span><span class="sxs-lookup"><span data-stu-id="94153-132">Long Polling Transport</span></span> |<span data-ttu-id="94153-133">✔</span><span class="sxs-lookup"><span data-stu-id="94153-133">✔</span></span>|<span data-ttu-id="94153-134">✔</span><span class="sxs-lookup"><span data-stu-id="94153-134">✔</span></span>|<span data-ttu-id="94153-135">✔</span><span class="sxs-lookup"><span data-stu-id="94153-135">✔</span></span>|
| <span data-ttu-id="94153-136">JSON 集线器协议</span><span class="sxs-lookup"><span data-stu-id="94153-136">JSON Hub Protocol</span></span> |<span data-ttu-id="94153-137">✔</span><span class="sxs-lookup"><span data-stu-id="94153-137">✔</span></span>|<span data-ttu-id="94153-138">✔</span><span class="sxs-lookup"><span data-stu-id="94153-138">✔</span></span>|<span data-ttu-id="94153-139">✔</span><span class="sxs-lookup"><span data-stu-id="94153-139">✔</span></span>|
| <span data-ttu-id="94153-140">MessagePack 中心协议</span><span class="sxs-lookup"><span data-stu-id="94153-140">MessagePack Hub Protocol</span></span> |<span data-ttu-id="94153-141">✔</span><span class="sxs-lookup"><span data-stu-id="94153-141">✔</span></span>|<span data-ttu-id="94153-142">✔</span><span class="sxs-lookup"><span data-stu-id="94153-142">✔</span></span>| |

<span data-ttu-id="94153-143">在[问题跟踪](https://github.com/aspnet/AspNetCore/issues/8711)程序中跟踪了 Java 客户端中的自动重新连接支持。</span><span class="sxs-lookup"><span data-stu-id="94153-143">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="94153-144">其他资源</span><span class="sxs-lookup"><span data-stu-id="94153-144">Additional resources</span></span>

* [<span data-ttu-id="94153-145">开始使用 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="94153-145">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="94153-146">支持的平台</span><span class="sxs-lookup"><span data-stu-id="94153-146">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="94153-147">中心</span><span class="sxs-lookup"><span data-stu-id="94153-147">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="94153-148">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="94153-148">JavaScript client</span></span>](xref:signalr/javascript-client)
