---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 1c78803e6468f924bf8c8e9403a34565b114006f
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82771111"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="884b6-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="884b6-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="884b6-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="884b6-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="884b6-105">浏览器要求</span><span class="sxs-lookup"><span data-stu-id="884b6-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="884b6-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="884b6-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="884b6-107">浏览者</span><span class="sxs-lookup"><span data-stu-id="884b6-107">Browser</span></span>                          | <span data-ttu-id="884b6-108">Version</span><span class="sxs-lookup"><span data-stu-id="884b6-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="884b6-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="884b6-109">Microsoft Edge</span></span>                   | <span data-ttu-id="884b6-110">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-110">Current</span></span>               |
| <span data-ttu-id="884b6-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="884b6-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="884b6-112">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-112">Current</span></span>               |
| <span data-ttu-id="884b6-113">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="884b6-113">Google Chrome, including Android</span></span> | <span data-ttu-id="884b6-114">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-114">Current</span></span>               |
| <span data-ttu-id="884b6-115">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="884b6-115">Safari, including iOS</span></span>            | <span data-ttu-id="884b6-116">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-116">Current</span></span>               |
| <span data-ttu-id="884b6-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="884b6-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="884b6-118">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="884b6-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="884b6-119">&dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="884b6-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="884b6-120">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="884b6-120">Blazor Server</span></span>

| <span data-ttu-id="884b6-121">浏览者</span><span class="sxs-lookup"><span data-stu-id="884b6-121">Browser</span></span>                          | <span data-ttu-id="884b6-122">Version</span><span class="sxs-lookup"><span data-stu-id="884b6-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="884b6-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="884b6-123">Microsoft Edge</span></span>                   | <span data-ttu-id="884b6-124">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-124">Current</span></span>    |
| <span data-ttu-id="884b6-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="884b6-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="884b6-126">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-126">Current</span></span>    |
| <span data-ttu-id="884b6-127">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="884b6-127">Google Chrome, including Android</span></span> | <span data-ttu-id="884b6-128">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-128">Current</span></span>    |
| <span data-ttu-id="884b6-129">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="884b6-129">Safari, including iOS</span></span>            | <span data-ttu-id="884b6-130">当前</span><span class="sxs-lookup"><span data-stu-id="884b6-130">Current</span></span>    |
| <span data-ttu-id="884b6-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="884b6-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="884b6-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="884b6-132">11&dagger;</span></span> |

<span data-ttu-id="884b6-133">&dagger;需要其他填充代码（例如，可通过 [Polyfill.io](https://polyfill.io/v3/) 捆绑包添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="884b6-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="884b6-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="884b6-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
