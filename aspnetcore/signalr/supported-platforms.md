---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR 支持的平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc, devx-track-js
ms.date: 01/21/2021
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 0a858de44f4a87b182a43a776154b782c7e96288
ms.sourcegitcommit: ebc5beccba5f3f7619de20baa58ad727d2a3d18c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/22/2021
ms.locfileid: "98689222"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="0bf60-103">ASP.NET Core SignalR 支持的平台</span><span class="sxs-lookup"><span data-stu-id="0bf60-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="0bf60-104">服务器系统要求</span><span class="sxs-lookup"><span data-stu-id="0bf60-104">Server system requirements</span></span>

<span data-ttu-id="0bf60-105">SignalR 对于 ASP.NET Core 支持 ASP.NET Core 支持的任何服务器平台。</span><span class="sxs-lookup"><span data-stu-id="0bf60-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="0bf60-106">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="0bf60-106">JavaScript client</span></span>

<span data-ttu-id="0bf60-107">[JavaScript 客户端](xref:signalr/javascript-client)在 NodeJS 8 及更高版本以及以下浏览器上运行：</span><span class="sxs-lookup"><span data-stu-id="0bf60-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="0bf60-108">浏览者</span><span class="sxs-lookup"><span data-stu-id="0bf60-108">Browser</span></span>                          | <span data-ttu-id="0bf60-109">Version</span><span class="sxs-lookup"><span data-stu-id="0bf60-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="0bf60-110">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="0bf60-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="0bf60-111">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="0bf60-111">Current&dagger;</span></span> |
| <span data-ttu-id="0bf60-112">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="0bf60-112">Google Chrome, including Android</span></span> | <span data-ttu-id="0bf60-113">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="0bf60-113">Current&dagger;</span></span> |
| <span data-ttu-id="0bf60-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0bf60-114">Microsoft Edge</span></span>                   | <span data-ttu-id="0bf60-115">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="0bf60-115">Current&dagger;</span></span> |
| <span data-ttu-id="0bf60-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="0bf60-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="0bf60-117">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="0bf60-117">Current&dagger;</span></span> |

<span data-ttu-id="0bf60-118">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="0bf60-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

<span data-ttu-id="0bf60-119">JavaScript 客户端不支持 Internet Explorer 和其他旧版浏览器。</span><span class="sxs-lookup"><span data-stu-id="0bf60-119">The JavaScript client doesn't support Internet Explorer and other older browsers.</span></span> <span data-ttu-id="0bf60-120">客户端在不支持的浏览器上可能出现意外行为和错误。</span><span class="sxs-lookup"><span data-stu-id="0bf60-120">The client might have unexpected behavior and errors on unsupported browsers.</span></span>

## <a name="net-client"></a><span data-ttu-id="0bf60-121">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="0bf60-121">.NET client</span></span>

<span data-ttu-id="0bf60-122">[.Net 客户端](xref:signalr/dotnet-client)在 ASP.NET Core 支持的任何平台上运行。</span><span class="sxs-lookup"><span data-stu-id="0bf60-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="0bf60-123">例如， [xamarin 开发人员可以使用 SignalR ](https://github.com/aspnet/Announcements/issues/305)使用 xamarin 8.4.0.1 和更高版本以及使用 xamarin 11.14.0.4 和更高版本的 ios 应用来构建 Android 应用程序。</span><span class="sxs-lookup"><span data-stu-id="0bf60-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="0bf60-124">如果服务器运行 IIS，则 Websocket 传输要求在 Windows Server 2012 或更高版本上安装 IIS 8.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="0bf60-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="0bf60-125">所有平台都支持其他传输。</span><span class="sxs-lookup"><span data-stu-id="0bf60-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="0bf60-126">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="0bf60-126">Java client</span></span>

<span data-ttu-id="0bf60-127">[Java 客户端](xref:signalr/java-client)支持 java 8 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="0bf60-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="0bf60-128">不受支持的客户端</span><span class="sxs-lookup"><span data-stu-id="0bf60-128">Unsupported clients</span></span>

<span data-ttu-id="0bf60-129">以下客户端可用，但为试验或非正式客户端。</span><span class="sxs-lookup"><span data-stu-id="0bf60-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="0bf60-130">它们目前不受支持，可能永远不会。</span><span class="sxs-lookup"><span data-stu-id="0bf60-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="0bf60-131">[C + + 客户端](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="0bf60-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="0bf60-132">[Swift 客户端](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="0bf60-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
