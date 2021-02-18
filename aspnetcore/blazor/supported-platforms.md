---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 948c3e3f66da4727731b37491ae5c5470cfb36fe
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100280725"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="9cc36-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="9cc36-103">ASP.NET Core Blazor supported platforms</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="9cc36-104">下表中所示的浏览器支持 Blazor WebAssembly 和 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="9cc36-104">Blazor WebAssembly and Blazor Server are supported in the browsers shown in the following table.</span></span>

| <span data-ttu-id="9cc36-105">浏览者</span><span class="sxs-lookup"><span data-stu-id="9cc36-105">Browser</span></span>                          | <span data-ttu-id="9cc36-106">Version</span><span class="sxs-lookup"><span data-stu-id="9cc36-106">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="9cc36-107">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="9cc36-107">Apple Safari, including iOS</span></span>      | <span data-ttu-id="9cc36-108">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-108">Current&dagger;</span></span> |
| <span data-ttu-id="9cc36-109">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="9cc36-109">Google Chrome, including Android</span></span> | <span data-ttu-id="9cc36-110">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-110">Current&dagger;</span></span> |
| <span data-ttu-id="9cc36-111">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9cc36-111">Microsoft Edge</span></span>                   | <span data-ttu-id="9cc36-112">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-112">Current&dagger;</span></span> |
| <span data-ttu-id="9cc36-113">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="9cc36-113">Mozilla Firefox</span></span>                  | <span data-ttu-id="9cc36-114">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-114">Current&dagger;</span></span> |  

<span data-ttu-id="9cc36-115">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="9cc36-115">&dagger;*Current* refers to the latest version of the browser.</span></span>  

::: moniker-end

::: moniker range="< aspnetcore-5.0"

## Blazor WebAssembly

| <span data-ttu-id="9cc36-116">浏览者</span><span class="sxs-lookup"><span data-stu-id="9cc36-116">Browser</span></span>                          | <span data-ttu-id="9cc36-117">Version</span><span class="sxs-lookup"><span data-stu-id="9cc36-117">Version</span></span>               |
| -------------------------------- | --------------------- |
| <span data-ttu-id="9cc36-118">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="9cc36-118">Apple Safari, including iOS</span></span>      | <span data-ttu-id="9cc36-119">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-119">Current&dagger;</span></span>       |
| <span data-ttu-id="9cc36-120">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="9cc36-120">Google Chrome, including Android</span></span> | <span data-ttu-id="9cc36-121">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-121">Current&dagger;</span></span>       |
| <span data-ttu-id="9cc36-122">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9cc36-122">Microsoft Edge</span></span>                   | <span data-ttu-id="9cc36-123">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-123">Current&dagger;</span></span>       |
| <span data-ttu-id="9cc36-124">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="9cc36-124">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="9cc36-125">不支持&Dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-125">Not Supported&Dagger;</span></span> |
| <span data-ttu-id="9cc36-126">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="9cc36-126">Mozilla Firefox</span></span>                  | <span data-ttu-id="9cc36-127">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-127">Current&dagger;</span></span>       |  

<span data-ttu-id="9cc36-128">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="9cc36-128">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="9cc36-129">&Dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="9cc36-129">&Dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

## Blazor Server

| <span data-ttu-id="9cc36-130">浏览者</span><span class="sxs-lookup"><span data-stu-id="9cc36-130">Browser</span></span>                          | <span data-ttu-id="9cc36-131">Version</span><span class="sxs-lookup"><span data-stu-id="9cc36-131">Version</span></span>         |
| -------------------------------- | --------------- |
| <span data-ttu-id="9cc36-132">Apple Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="9cc36-132">Apple Safari, including iOS</span></span>      | <span data-ttu-id="9cc36-133">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-133">Current&dagger;</span></span> |
| <span data-ttu-id="9cc36-134">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="9cc36-134">Google Chrome, including Android</span></span> | <span data-ttu-id="9cc36-135">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-135">Current&dagger;</span></span> |
| <span data-ttu-id="9cc36-136">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="9cc36-136">Microsoft Edge</span></span>                   | <span data-ttu-id="9cc36-137">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-137">Current&dagger;</span></span> |
| <span data-ttu-id="9cc36-138">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="9cc36-138">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="9cc36-139">11&Dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-139">11&Dagger;</span></span>      |
| <span data-ttu-id="9cc36-140">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="9cc36-140">Mozilla Firefox</span></span>                  | <span data-ttu-id="9cc36-141">当前&dagger;</span><span class="sxs-lookup"><span data-stu-id="9cc36-141">Current&dagger;</span></span> |

<span data-ttu-id="9cc36-142">&dagger;最新指的是浏览器的最新版本。</span><span class="sxs-lookup"><span data-stu-id="9cc36-142">&dagger;*Current* refers to the latest version of the browser.</span></span>  
<span data-ttu-id="9cc36-143">&Dagger;需要其他填充代码。</span><span class="sxs-lookup"><span data-stu-id="9cc36-143">&Dagger;Additional polyfills are required.</span></span> <span data-ttu-id="9cc36-144">例如，可通过 [`Polyfill.io`](https://polyfill.io/v3/) 捆绑包添加承诺。</span><span class="sxs-lookup"><span data-stu-id="9cc36-144">For example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle.</span></span>

::: moniker-end

## <a name="additional-resources"></a><span data-ttu-id="9cc36-145">其他资源</span><span class="sxs-lookup"><span data-stu-id="9cc36-145">Additional resources</span></span>

* <xref:blazor/hosting-models>
* <xref:signalr/supported-platforms>
