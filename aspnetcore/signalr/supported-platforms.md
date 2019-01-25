---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR 支持的平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 11/12/2018
uid: signalr/supported-platforms
ms.openlocfilehash: e4e84baf0120036b473eac256107b46a4accfe37
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54837710"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="5e353-103">ASP.NET Core SignalR 支持的平台</span><span class="sxs-lookup"><span data-stu-id="5e353-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="5e353-104">服务器系统要求</span><span class="sxs-lookup"><span data-stu-id="5e353-104">Server system requirements</span></span>

<span data-ttu-id="5e353-105">ASP.NET Core SignalR 适用于 ASP.NET Core 支持的任何服务器平台。</span><span class="sxs-lookup"><span data-stu-id="5e353-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="5e353-106">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="5e353-106">JavaScript client</span></span>

<span data-ttu-id="5e353-107">[JavaScript 客户端](https://www.npmjs.com/package/@aspnet/signalr)需要NodeJS 8或更高版本和以下浏览器上运行：</span><span class="sxs-lookup"><span data-stu-id="5e353-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="5e353-108">浏览者</span><span class="sxs-lookup"><span data-stu-id="5e353-108">Browser</span></span>                         | <span data-ttu-id="5e353-109">版本</span><span class="sxs-lookup"><span data-stu-id="5e353-109">Version</span></span> |
| ------------------------------- | ------- |
| <span data-ttu-id="5e353-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="5e353-110">Microsoft Edge</span></span>                  | <span data-ttu-id="5e353-111">当前</span><span class="sxs-lookup"><span data-stu-id="5e353-111">current</span></span> |
| <span data-ttu-id="5e353-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="5e353-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="5e353-113">当前</span><span class="sxs-lookup"><span data-stu-id="5e353-113">current</span></span> |
| <span data-ttu-id="5e353-114">Google Chrome;包括 Android</span><span class="sxs-lookup"><span data-stu-id="5e353-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="5e353-115">当前</span><span class="sxs-lookup"><span data-stu-id="5e353-115">current</span></span> |
| <span data-ttu-id="5e353-116">Safari;包含 iOS</span><span class="sxs-lookup"><span data-stu-id="5e353-116">Safari; includes iOS</span></span>            | <span data-ttu-id="5e353-117">当前</span><span class="sxs-lookup"><span data-stu-id="5e353-117">current</span></span> |
| <span data-ttu-id="5e353-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="5e353-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="5e353-119">11</span><span class="sxs-lookup"><span data-stu-id="5e353-119">11</span></span>      |
 
## <a name="net-client"></a><span data-ttu-id="5e353-120">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="5e353-120">.NET client</span></span>

<span data-ttu-id="5e353-121">[.NET 客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)可以在 ASP.NET Core 支持的任何平台上运行。</span><span class="sxs-lookup"><span data-stu-id="5e353-121">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="5e353-122">例如， [Xamarin 开发人员可以使用 SignalR](https://github.com/aspnet/Announcements/issues/305)用于构建 Android 应用程序使用 Xamarin.Android 8.4.0.1 或更高版本和 iOS 应用程序使用 Xamarin.iOS 11.14.0.4 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5e353-122">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="5e353-123">如果服务器运行 IIS，Websocket 传输要求安装 IIS 8.0 或更高版本 Windows Server 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5e353-123">If the server runs IIS, the WebSockets transport requires IIS 8.0 or higher on Windows Server 2012 or higher.</span></span> <span data-ttu-id="5e353-124">其他传输在所有平台上都受支持。</span><span class="sxs-lookup"><span data-stu-id="5e353-124">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="5e353-125">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="5e353-125">Java client</span></span>

<span data-ttu-id="5e353-126">[Java 客户端](https://search.maven.org/artifact/com.microsoft.aspnet/signalr)支持 Java 8 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="5e353-126">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="5e353-127">不受支持的客户端</span><span class="sxs-lookup"><span data-stu-id="5e353-127">Unsupported clients</span></span>

<span data-ttu-id="5e353-128">以下客户端可用，但是是实验性的或非官方。</span><span class="sxs-lookup"><span data-stu-id="5e353-128">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="5e353-129">它们目前不支持，可能永远不会。</span><span class="sxs-lookup"><span data-stu-id="5e353-129">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="5e353-130">C + + 客户端</span><span class="sxs-lookup"><span data-stu-id="5e353-130">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="5e353-131">Swift 的客户端</span><span class="sxs-lookup"><span data-stu-id="5e353-131">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
