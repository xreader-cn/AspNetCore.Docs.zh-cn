---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: c10132c87c93346af89c548363e786967609f3da
ms.sourcegitcommit: d243fadeda20ad4f142ea60301ae5f5e0d41ed60
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/12/2020
ms.locfileid: "83607974"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="cf038-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="cf038-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="cf038-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="cf038-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="cf038-105">浏览器要求</span><span class="sxs-lookup"><span data-stu-id="cf038-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a>Blazor<span data-ttu-id="cf038-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="cf038-106"> WebAssembly</span></span>

| <span data-ttu-id="cf038-107">浏览者</span><span class="sxs-lookup"><span data-stu-id="cf038-107">Browser</span></span>                          | <span data-ttu-id="cf038-108">Version</span><span class="sxs-lookup"><span data-stu-id="cf038-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="cf038-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="cf038-109">Microsoft Edge</span></span>                   | <span data-ttu-id="cf038-110">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-110">Current</span></span>               |
| <span data-ttu-id="cf038-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="cf038-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="cf038-112">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-112">Current</span></span>               |
| <span data-ttu-id="cf038-113">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="cf038-113">Google Chrome, including Android</span></span> | <span data-ttu-id="cf038-114">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-114">Current</span></span>               |
| <span data-ttu-id="cf038-115">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="cf038-115">Safari, including iOS</span></span>            | <span data-ttu-id="cf038-116">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-116">Current</span></span>               |
| <span data-ttu-id="cf038-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="cf038-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="cf038-118">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="cf038-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="cf038-119">&dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="cf038-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a>Blazor<span data-ttu-id="cf038-120"> 服务器</span><span class="sxs-lookup"><span data-stu-id="cf038-120"> Server</span></span>

| <span data-ttu-id="cf038-121">浏览者</span><span class="sxs-lookup"><span data-stu-id="cf038-121">Browser</span></span>                          | <span data-ttu-id="cf038-122">Version</span><span class="sxs-lookup"><span data-stu-id="cf038-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="cf038-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="cf038-123">Microsoft Edge</span></span>                   | <span data-ttu-id="cf038-124">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-124">Current</span></span>    |
| <span data-ttu-id="cf038-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="cf038-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="cf038-126">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-126">Current</span></span>    |
| <span data-ttu-id="cf038-127">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="cf038-127">Google Chrome, including Android</span></span> | <span data-ttu-id="cf038-128">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-128">Current</span></span>    |
| <span data-ttu-id="cf038-129">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="cf038-129">Safari, including iOS</span></span>            | <span data-ttu-id="cf038-130">当前</span><span class="sxs-lookup"><span data-stu-id="cf038-130">Current</span></span>    |
| <span data-ttu-id="cf038-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="cf038-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="cf038-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="cf038-132">11&dagger;</span></span> |

<span data-ttu-id="cf038-133">&dagger;需要其他填充代码（例如，可通过 [Polyfill.io](https://polyfill.io/v3/) 捆绑包添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="cf038-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cf038-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="cf038-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
