---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR 支持的平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
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
ms.openlocfilehash: a342dd787eceadd22ac26b57a3615a6b0b21f461
ms.sourcegitcommit: d60bfd52bfb559e805abd654b87a2a0c7eb69cf8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/06/2020
ms.locfileid: "91754497"
---
# <a name="aspnet-core-no-locsignalr-supported-platforms"></a><span data-ttu-id="b2dba-103">ASP.NET Core SignalR 支持的平台</span><span class="sxs-lookup"><span data-stu-id="b2dba-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="b2dba-104">服务器系统要求</span><span class="sxs-lookup"><span data-stu-id="b2dba-104">Server system requirements</span></span>

<span data-ttu-id="b2dba-105">SignalR 对于 ASP.NET Core 支持 ASP.NET Core 支持的任何服务器平台。</span><span class="sxs-lookup"><span data-stu-id="b2dba-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="b2dba-106">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="b2dba-106">JavaScript client</span></span>

<span data-ttu-id="b2dba-107">[JavaScript 客户端](xref:signalr/javascript-client)在 NodeJS 8 及更高版本以及以下浏览器上运行：</span><span class="sxs-lookup"><span data-stu-id="b2dba-107">The [JavaScript client](xref:signalr/javascript-client) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="b2dba-108">浏览者</span><span class="sxs-lookup"><span data-stu-id="b2dba-108">Browser</span></span>                          | <span data-ttu-id="b2dba-109">Version</span><span class="sxs-lookup"><span data-stu-id="b2dba-109">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="b2dba-110">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="b2dba-110">Apple Safari, including iOS</span></span>      | <span data-ttu-id="b2dba-111">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="b2dba-111">Current&dagger;</span></span> |
| <span data-ttu-id="b2dba-112">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="b2dba-112">Google Chrome, including Android</span></span> | <span data-ttu-id="b2dba-113">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="b2dba-113">Current&dagger;</span></span> |
| <span data-ttu-id="b2dba-114">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="b2dba-114">Microsoft Edge</span></span>                   | <span data-ttu-id="b2dba-115">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="b2dba-115">Current&dagger;</span></span> |
| <span data-ttu-id="b2dba-116">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="b2dba-116">Mozilla Firefox</span></span>                  | <span data-ttu-id="b2dba-117">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="b2dba-117">Current&dagger;</span></span> |

<span data-ttu-id="b2dba-118">&dagger;*当前* 是指浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="b2dba-118">&dagger;*Current* refers to the latest version of the browser.</span></span>

## <a name="net-client"></a><span data-ttu-id="b2dba-119">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="b2dba-119">.NET client</span></span>

<span data-ttu-id="b2dba-120">[.Net 客户端](xref:signalr/dotnet-client)在 ASP.NET Core 支持的任何平台上运行。</span><span class="sxs-lookup"><span data-stu-id="b2dba-120">The [.NET client](xref:signalr/dotnet-client) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="b2dba-121">例如， [xamarin 开发人员可以使用 SignalR ](https://github.com/aspnet/Announcements/issues/305)使用 xamarin 8.4.0.1 和更高版本以及使用 xamarin 11.14.0.4 和更高版本的 ios 应用来构建 Android 应用程序。</span><span class="sxs-lookup"><span data-stu-id="b2dba-121">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="b2dba-122">如果服务器运行 IIS，则 Websocket 传输要求在 Windows Server 2012 或更高版本上安装 IIS 8.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="b2dba-122">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="b2dba-123">所有平台都支持其他传输。</span><span class="sxs-lookup"><span data-stu-id="b2dba-123">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="b2dba-124">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="b2dba-124">Java client</span></span>

<span data-ttu-id="b2dba-125">[Java 客户端](xref:signalr/java-client)支持 java 8 及更高版本。</span><span class="sxs-lookup"><span data-stu-id="b2dba-125">The [Java client](xref:signalr/java-client) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="b2dba-126">不受支持的客户端</span><span class="sxs-lookup"><span data-stu-id="b2dba-126">Unsupported clients</span></span>

<span data-ttu-id="b2dba-127">以下客户端可用，但为试验或非正式客户端。</span><span class="sxs-lookup"><span data-stu-id="b2dba-127">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="b2dba-128">它们目前不受支持，可能永远不会。</span><span class="sxs-lookup"><span data-stu-id="b2dba-128">They aren't currently supported and may never be.</span></span>

* <span data-ttu-id="b2dba-129">[C + + 客户端](https://github.com/aspnet/SignalR-Client-Cpp)</span><span class="sxs-lookup"><span data-stu-id="b2dba-129">[C++ client](https://github.com/aspnet/SignalR-Client-Cpp)</span></span>

* <span data-ttu-id="b2dba-130">[Swift 客户端](https://github.com/moozzyk/SignalR-Client-Swift)</span><span class="sxs-lookup"><span data-stu-id="b2dba-130">[Swift client](https://github.com/moozzyk/SignalR-Client-Swift)</span></span>
