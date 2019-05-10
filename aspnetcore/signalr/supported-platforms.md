---
title: ASP.NET Core SignalR 支持的平台
author: bradygaster
description: 了解 ASP.NET Core SignalR 支持的平台。
monikerRange: '>= aspnetcore-2.1'
ms.author: bradyg
ms.custom: mvc
ms.date: 04/06/2019
uid: signalr/supported-platforms
ms.openlocfilehash: fefaaf97de3f1fabf8f3154bf5b4ccb37195ccff
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64892014"
---
# <a name="aspnet-core-signalr-supported-platforms"></a><span data-ttu-id="8601e-103">ASP.NET Core SignalR 支持的平台</span><span class="sxs-lookup"><span data-stu-id="8601e-103">ASP.NET Core SignalR supported platforms</span></span>

## <a name="server-system-requirements"></a><span data-ttu-id="8601e-104">服务器系统要求</span><span class="sxs-lookup"><span data-stu-id="8601e-104">Server system requirements</span></span>

<span data-ttu-id="8601e-105">ASP.NET Core SignalR 适用于 ASP.NET Core 支持的任何服务器平台。</span><span class="sxs-lookup"><span data-stu-id="8601e-105">SignalR for ASP.NET Core supports any server platform that ASP.NET Core supports.</span></span>

## <a name="javascript-client"></a><span data-ttu-id="8601e-106">JavaScript 客户端</span><span class="sxs-lookup"><span data-stu-id="8601e-106">JavaScript client</span></span>

<span data-ttu-id="8601e-107">[JavaScript 客户端](https://www.npmjs.com/package/@aspnet/signalr)需要NodeJS 8或更高版本和以下浏览器上运行：</span><span class="sxs-lookup"><span data-stu-id="8601e-107">The [JavaScript client](https://www.npmjs.com/package/@aspnet/signalr) runs on NodeJS 8 and later versions and the following browsers:</span></span>

| <span data-ttu-id="8601e-108">浏览者</span><span class="sxs-lookup"><span data-stu-id="8601e-108">Browser</span></span>                         | <span data-ttu-id="8601e-109">版本</span><span class="sxs-lookup"><span data-stu-id="8601e-109">Version</span></span> |
| ------------------------------- | ------- |
| <span data-ttu-id="8601e-110">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="8601e-110">Microsoft Edge</span></span>                  | <span data-ttu-id="8601e-111">当前</span><span class="sxs-lookup"><span data-stu-id="8601e-111">current</span></span> |
| <span data-ttu-id="8601e-112">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="8601e-112">Mozilla Firefox</span></span>                 | <span data-ttu-id="8601e-113">当前</span><span class="sxs-lookup"><span data-stu-id="8601e-113">current</span></span> |
| <span data-ttu-id="8601e-114">Google Chrome;包括 Android</span><span class="sxs-lookup"><span data-stu-id="8601e-114">Google Chrome; includes Android</span></span> | <span data-ttu-id="8601e-115">当前</span><span class="sxs-lookup"><span data-stu-id="8601e-115">current</span></span> |
| <span data-ttu-id="8601e-116">Safari;包含 iOS</span><span class="sxs-lookup"><span data-stu-id="8601e-116">Safari; includes iOS</span></span>            | <span data-ttu-id="8601e-117">当前</span><span class="sxs-lookup"><span data-stu-id="8601e-117">current</span></span> |
| <span data-ttu-id="8601e-118">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="8601e-118">Microsoft Internet Explorer</span></span>     | <span data-ttu-id="8601e-119">11</span><span class="sxs-lookup"><span data-stu-id="8601e-119">11</span></span>      |
 
## <a name="net-client"></a><span data-ttu-id="8601e-120">.NET 客户端</span><span class="sxs-lookup"><span data-stu-id="8601e-120">.NET client</span></span>

<span data-ttu-id="8601e-121">[.NET 客户端](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/)可以在 ASP.NET Core 支持的任何平台上运行。</span><span class="sxs-lookup"><span data-stu-id="8601e-121">The [.NET client](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR/) runs on any platform supported by ASP.NET Core.</span></span> <span data-ttu-id="8601e-122">例如， [Xamarin 开发人员可以使用 SignalR](https://github.com/aspnet/Announcements/issues/305)用于构建 Android 应用程序使用 Xamarin.Android 8.4.0.1 或更高版本和 iOS 应用程序使用 Xamarin.iOS 11.14.0.4 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8601e-122">For example, [Xamarin developers can use SignalR](https://github.com/aspnet/Announcements/issues/305) for building Android apps using Xamarin.Android 8.4.0.1 and later and iOS apps using Xamarin.iOS 11.14.0.4 and later.</span></span>

<span data-ttu-id="8601e-123">如果服务器运行 IIS，Websocket 传输要求安装 IIS 8.0 或更高版本在 Windows Server 2012 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8601e-123">If the server runs IIS, the WebSockets transport requires IIS 8.0 or later on Windows Server 2012 or later.</span></span> <span data-ttu-id="8601e-124">其他传输在所有平台上都受支持。</span><span class="sxs-lookup"><span data-stu-id="8601e-124">Other transports are supported on all platforms.</span></span>

## <a name="java-client"></a><span data-ttu-id="8601e-125">Java 客户端</span><span class="sxs-lookup"><span data-stu-id="8601e-125">Java client</span></span>

<span data-ttu-id="8601e-126">[Java 客户端](https://search.maven.org/artifact/com.microsoft.aspnet/signalr)支持 Java 8 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8601e-126">The [Java client](https://search.maven.org/artifact/com.microsoft.aspnet/signalr) supports Java 8 and later versions.</span></span>

## <a name="unsupported-clients"></a><span data-ttu-id="8601e-127">不受支持的客户端</span><span class="sxs-lookup"><span data-stu-id="8601e-127">Unsupported clients</span></span>

<span data-ttu-id="8601e-128">以下客户端可用，但是是实验性的或非官方。</span><span class="sxs-lookup"><span data-stu-id="8601e-128">The following clients are available but are experimental or unofficial.</span></span> <span data-ttu-id="8601e-129">它们目前不支持，可能永远不会。</span><span class="sxs-lookup"><span data-stu-id="8601e-129">They aren't currently supported and may never be.</span></span>

* [<span data-ttu-id="8601e-130">C++客户端</span><span class="sxs-lookup"><span data-stu-id="8601e-130">C++ client</span></span>](https://github.com/aspnet/SignalR/tree/master/clients/cpp)

* [<span data-ttu-id="8601e-131">Swift 的客户端</span><span class="sxs-lookup"><span data-stu-id="8601e-131">Swift client</span></span>](https://github.com/moozzyk/SignalR-Client-Swift)
