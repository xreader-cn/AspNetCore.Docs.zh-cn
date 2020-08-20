---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 支持的平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/19/2020
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
uid: blazor/supported-platforms
ms.openlocfilehash: 692ab63bb48dbfa29021d59cdf035e9549d3039c
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88625942"
---
# <a name="aspnet-core-no-locblazor-supported-platforms"></a><span data-ttu-id="0eb29-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="0eb29-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="0eb29-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0eb29-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="0eb29-105">浏览器要求</span><span class="sxs-lookup"><span data-stu-id="0eb29-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="0eb29-106">浏览者</span><span class="sxs-lookup"><span data-stu-id="0eb29-106">Browser</span></span>                          | <span data-ttu-id="0eb29-107">Version</span><span class="sxs-lookup"><span data-stu-id="0eb29-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="0eb29-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0eb29-108">Microsoft Edge</span></span>                   | <span data-ttu-id="0eb29-109">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-109">Current</span></span>               |
| <span data-ttu-id="0eb29-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="0eb29-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="0eb29-111">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-111">Current</span></span>               |
| <span data-ttu-id="0eb29-112">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="0eb29-112">Google Chrome, including Android</span></span> | <span data-ttu-id="0eb29-113">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-113">Current</span></span>               |
| <span data-ttu-id="0eb29-114">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="0eb29-114">Safari, including iOS</span></span>            | <span data-ttu-id="0eb29-115">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-115">Current</span></span>               |
| <span data-ttu-id="0eb29-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="0eb29-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="0eb29-117">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="0eb29-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="0eb29-118">&dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="0eb29-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="0eb29-119">浏览者</span><span class="sxs-lookup"><span data-stu-id="0eb29-119">Browser</span></span>                          | <span data-ttu-id="0eb29-120">Version</span><span class="sxs-lookup"><span data-stu-id="0eb29-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="0eb29-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="0eb29-121">Microsoft Edge</span></span>                   | <span data-ttu-id="0eb29-122">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-122">Current</span></span>    |
| <span data-ttu-id="0eb29-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="0eb29-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="0eb29-124">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-124">Current</span></span>    |
| <span data-ttu-id="0eb29-125">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="0eb29-125">Google Chrome, including Android</span></span> | <span data-ttu-id="0eb29-126">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-126">Current</span></span>    |
| <span data-ttu-id="0eb29-127">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="0eb29-127">Safari, including iOS</span></span>            | <span data-ttu-id="0eb29-128">当前</span><span class="sxs-lookup"><span data-stu-id="0eb29-128">Current</span></span>    |
| <span data-ttu-id="0eb29-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="0eb29-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="0eb29-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="0eb29-130">11&dagger;</span></span> |

<span data-ttu-id="0eb29-131">&dagger;需要其他填充代码（例如，可通过 [`Polyfill.io`](https://polyfill.io/v3/) 捆绑包添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="0eb29-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0eb29-132">其他资源</span><span class="sxs-lookup"><span data-stu-id="0eb29-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
