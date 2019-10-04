---
title: SignalR 客户端功能
author: bradygaster
description: 了解各种 ASP.NET Core SignalR 客户端支持的功能。
ms.author: bradyg
ms.custom: mvc
ms.date: 09/18/2019
uid: signalr/client-features
ms.openlocfilehash: 6718722cdbcfae500026fcd429ca6b9de8f0f103
ms.sourcegitcommit: 73e255e846e414821b8cc20ffa3aec946735cd4e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2019
ms.locfileid: "71925357"
---
# <a name="aspnet-core-signalr-client-features"></a><span data-ttu-id="20d44-103">ASP.NET Core SignalR 客户端功能</span><span class="sxs-lookup"><span data-stu-id="20d44-103">ASP.NET Core SignalR client features</span></span>

## <a name="feature-distribution"></a><span data-ttu-id="20d44-104">功能分发</span><span class="sxs-lookup"><span data-stu-id="20d44-104">Feature distribution</span></span>

<span data-ttu-id="20d44-105">下表显示提供实时支持的客户端的功能和支持。</span><span class="sxs-lookup"><span data-stu-id="20d44-105">The table below shows the features and support for the clients that offer real-time support.</span></span> <span data-ttu-id="20d44-106">对于每项功能，将列出支持此功能的*最低*版本。</span><span class="sxs-lookup"><span data-stu-id="20d44-106">For each feature, the *minimum* version supporting this feature is listed.</span></span> <span data-ttu-id="20d44-107">如果未列出任何版本，则不支持此功能。</span><span class="sxs-lookup"><span data-stu-id="20d44-107">If no version is listed, the feature isn't supported.</span></span>

| <span data-ttu-id="20d44-108">功能</span><span class="sxs-lookup"><span data-stu-id="20d44-108">Feature</span></span> | <span data-ttu-id="20d44-109">.NET</span><span class="sxs-lookup"><span data-stu-id="20d44-109">.NET</span></span> | <span data-ttu-id="20d44-110">JavaScript</span><span class="sxs-lookup"><span data-stu-id="20d44-110">JavaScript</span></span> | <span data-ttu-id="20d44-111">Java</span><span class="sxs-lookup"><span data-stu-id="20d44-111">Java</span></span> |
| ---- | :-: | :-: | :-: |
| <span data-ttu-id="20d44-112">Azure SignalR 服务支持</span><span class="sxs-lookup"><span data-stu-id="20d44-112">Azure SignalR Service Support</span></span> |<span data-ttu-id="20d44-113">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-113">1.0.0</span></span>|<span data-ttu-id="20d44-114">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-114">1.0.0</span></span>|<span data-ttu-id="20d44-115">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-115">1.0.0</span></span>|
| [<span data-ttu-id="20d44-116">服务器到客户端流式处理</span><span class="sxs-lookup"><span data-stu-id="20d44-116">Server-to-client Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="20d44-117">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-117">1.0.0</span></span>|<span data-ttu-id="20d44-118">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-118">1.0.0</span></span>|<span data-ttu-id="20d44-119">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-119">1.0.0</span></span>|
| [<span data-ttu-id="20d44-120">客户端到服务器的流式处理</span><span class="sxs-lookup"><span data-stu-id="20d44-120">Client-to-server Streaming</span></span>](xref:signalr/streaming)          |<span data-ttu-id="20d44-121">3.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-121">3.0.0</span></span>|<span data-ttu-id="20d44-122">3.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-122">3.0.0</span></span>|<span data-ttu-id="20d44-123">3.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-123">3.0.0</span></span>|
| <span data-ttu-id="20d44-124">自动重新连接（[.net](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection)， [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients)）</span><span class="sxs-lookup"><span data-stu-id="20d44-124">Automatic Reconnection ([.NET](/aspnet/core/signalr/dotnet-client?view=aspnetcore-3.0&tabs=visual-studio#handle-lost-connection), [JavaScript](/aspnet/core/signalr/javascript-client?view=aspnetcore-3.0#reconnect-clients))</span></span>          |<span data-ttu-id="20d44-125">3.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-125">3.0.0</span></span>|<span data-ttu-id="20d44-126">3.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-126">3.0.0</span></span>|<span data-ttu-id="20d44-127">❌</span><span class="sxs-lookup"><span data-stu-id="20d44-127">❌</span></span>|
| <span data-ttu-id="20d44-128">Websocket 传输</span><span class="sxs-lookup"><span data-stu-id="20d44-128">WebSockets Transport</span></span> |<span data-ttu-id="20d44-129">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-129">1.0.0</span></span>|<span data-ttu-id="20d44-130">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-130">1.0.0</span></span>|<span data-ttu-id="20d44-131">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-131">1.0.0</span></span>|
| <span data-ttu-id="20d44-132">服务器发送的事件传输</span><span class="sxs-lookup"><span data-stu-id="20d44-132">Server-Sent Events Transport</span></span> |<span data-ttu-id="20d44-133">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-133">1.0.0</span></span>|<span data-ttu-id="20d44-134">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-134">1.0.0</span></span>|<span data-ttu-id="20d44-135">❌</span><span class="sxs-lookup"><span data-stu-id="20d44-135">❌</span></span>|
| <span data-ttu-id="20d44-136">长轮询传输</span><span class="sxs-lookup"><span data-stu-id="20d44-136">Long Polling Transport</span></span> |<span data-ttu-id="20d44-137">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-137">1.0.0</span></span>|<span data-ttu-id="20d44-138">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-138">1.0.0</span></span>|<span data-ttu-id="20d44-139">3.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-139">3.0.0</span></span>|
| <span data-ttu-id="20d44-140">JSON 集线器协议</span><span class="sxs-lookup"><span data-stu-id="20d44-140">JSON Hub Protocol</span></span> |<span data-ttu-id="20d44-141">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-141">1.0.0</span></span>|<span data-ttu-id="20d44-142">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-142">1.0.0</span></span>|<span data-ttu-id="20d44-143">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-143">1.0.0</span></span>|
| <span data-ttu-id="20d44-144">MessagePack 中心协议</span><span class="sxs-lookup"><span data-stu-id="20d44-144">MessagePack Hub Protocol</span></span> |<span data-ttu-id="20d44-145">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-145">1.0.0</span></span>|<span data-ttu-id="20d44-146">1.0.0</span><span class="sxs-lookup"><span data-stu-id="20d44-146">1.0.0</span></span>|<span data-ttu-id="20d44-147">❌</span><span class="sxs-lookup"><span data-stu-id="20d44-147">❌</span></span>|

<span data-ttu-id="20d44-148">在[问题跟踪](https://github.com/aspnet/AspNetCore/issues/8711)程序中跟踪了 Java 客户端中的自动重新连接支持。</span><span class="sxs-lookup"><span data-stu-id="20d44-148">Support for automatic reconnect in the Java client is tracked in [our issue tracker](https://github.com/aspnet/AspNetCore/issues/8711).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="20d44-149">其他资源</span><span class="sxs-lookup"><span data-stu-id="20d44-149">Additional resources</span></span>

* [<span data-ttu-id="20d44-150">开始使用 ASP.NET Core SignalR</span><span class="sxs-lookup"><span data-stu-id="20d44-150">Get started with SignalR for ASP.NET Core</span></span>](xref:tutorials/signalr)
* [<span data-ttu-id="20d44-151">支持的平台</span><span class="sxs-lookup"><span data-stu-id="20d44-151">Supported platforms</span></span>](xref:signalr/supported-platforms)
* [<span data-ttu-id="20d44-152">中心</span><span class="sxs-lookup"><span data-stu-id="20d44-152">Hubs</span></span>](xref:signalr/hubs)
* [<span data-ttu-id="20d44-153">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="20d44-153">JavaScript client</span></span>](xref:signalr/javascript-client)
