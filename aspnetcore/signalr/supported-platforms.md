---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR的支持平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- SignalR
uid: signalr/supported-platforms
ms.openlocfilehash: 054965921c87c1a9be27e5ddaa8a87b0fa1f4113
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78655146"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="97c8e-103">ASP.NET Core SignalR 支持的平台</span><span class="sxs-lookup"><span data-stu-id="97c8e-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="97c8e-104">服务器系统要求</span><span class="sxs-lookup"><span data-stu-id="97c8e-104">Server system requirements</span></span>

<span data-ttu-id="97c8e-105">ASP.NET Core SignalR 适用于 ASP.NET Core 支持的任何服务器平台。</span><span class="sxs-lookup"><span data-stu-id="97c8e-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="97c8e-106">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="97c8e-106">JavaScript client</span></span>

<span data-ttu-id="97c8e-107">[JavaScript 客户端](xref:signalr/javascript-client)在 NodeJS 8 及更高版本以及以下浏览器上运行：</span><span class="sxs-lookup"><span data-stu-id="97c8e-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="97c8e-108">浏览者</span><span class="sxs-lookup"><span data-stu-id="97c8e-108">Browser</span></span>                         | <span data-ttu-id="97c8e-109">版本</span><span class="sxs-lookup"><span data-stu-id="97c8e-109">Version</span></span>         |
| ------------------------------- | --------------- |
| <span data-ttu-id="97c8e-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="97c8e-110">Microsoft Edge</span></span>                  | <span data-ttu-id="97c8e-111">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="97c8e-111">Current&dagger;</span></span> |
| <span data-ttu-id="97c8e-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="97c8e-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="97c8e-113">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="97c8e-113">Current&dagger;</span></span> |
| <span data-ttu-id="97c8e-114">Google Chrome;包括 Android</span><span class="sxs-lookup"><span data-stu-id="97c8e-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="97c8e-115">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="97c8e-115">Current&dagger;</span></span> |
| <span data-ttu-id="97c8e-116">免费包括 iOS</span><span class="sxs-lookup"><span data-stu-id="97c8e-116">Safari; includes iOS</span></span>            | <span data-ttu-id="97c8e-117">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="97c8e-117">Current&dagger;</span></span> |
| <span data-ttu-id="97c8e-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="97c8e-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="97c8e-119">11</span><span class="sxs-lookup"><span data-stu-id="97c8e-119">11</span></span>              |

<span data-ttu-id="97c8e-120">&dagger;*当前*指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="97c8e-120">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="97c8e-121">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="97c8e-121">.NET client</span></span>

<span data-ttu-id="97c8e-122">[.Net 客户端](xref:signalr/dotnet-client)在 ASP.NET Core 支持的任何平台上运行。</span><span class="sxs-lookup"><span data-stu-id="97c8e-122">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="97c8e-123">例如， [xamarin 开发人员可以使用 SignalR](https://github.com/aspnet/Announcements/issues/305)来使用 xamarin 8.4.0.1 和更高版本以及使用 xamarin 11.14.0.4 和更高版本的 ios 应用程序构建 Android 应用程序。</span><span class="sxs-lookup"><span data-stu-id="97c8e-123">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="97c8e-124">如果服务器运行 IIS，则 Websocket 传输要求在 Windows Server 2012 或更高版本上安装 IIS 8.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="97c8e-124">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="97c8e-125">其他传输在所有平台上都受支持。</span><span class="sxs-lookup"><span data-stu-id="97c8e-125">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="97c8e-126">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="97c8e-126">Java client</span></span>

<span data-ttu-id="97c8e-127">[Java 客户端](xref:signalr/java-client)支持 java 8 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="97c8e-127">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="97c8e-128">不受支持的客户端</span><span class="sxs-lookup"><span data-stu-id="97c8e-128">Unsupported clients</span></span>

<span data-ttu-id="97c8e-129">以下客户端可用，但是是实验性的或非官方。</span><span class="sxs-lookup"><span data-stu-id="97c8e-129">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="97c8e-130">它们目前不支持，可能永远不会。</span><span class="sxs-lookup"><span data-stu-id="97c8e-130">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="97c8e-131">[C++机](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="97c8e-131">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="97c8e-132">[Swift 客户端](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="97c8e-132">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
