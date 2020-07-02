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
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: adf27fa84acb3929a1639b561c728c2db29723f6
ms.sourcegitcommit: d65a027e78bf0b83727f975235a18863e685d902
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/26/2020
ms.locfileid: "85401930"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="a34a2-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="a34a2-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="a34a2-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="a34a2-104">By [Luke Latham](https://github.com/guardrex)</span></span>

## <a name="browser-requirements"></a><span data-ttu-id="a34a2-105">浏览器要求</span><span class="sxs-lookup"><span data-stu-id="a34a2-105">Browser requirements</span></span>

### Blazor WebAssembly

| <span data-ttu-id="a34a2-106">浏览者</span><span class="sxs-lookup"><span data-stu-id="a34a2-106">Browser</span></span>                          | <span data-ttu-id="a34a2-107">Version</span><span class="sxs-lookup"><span data-stu-id="a34a2-107">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="a34a2-108">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a34a2-108">Microsoft Edge</span></span>                   | <span data-ttu-id="a34a2-109">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-109">Current</span></span>               |
| <span data-ttu-id="a34a2-110">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="a34a2-110">Mozilla Firefox</span></span>                  | <span data-ttu-id="a34a2-111">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-111">Current</span></span>               |
| <span data-ttu-id="a34a2-112">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="a34a2-112">Google Chrome, including Android</span></span> | <span data-ttu-id="a34a2-113">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-113">Current</span></span>               |
| <span data-ttu-id="a34a2-114">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="a34a2-114">Safari, including iOS</span></span>            | <span data-ttu-id="a34a2-115">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-115">Current</span></span>               |
| <span data-ttu-id="a34a2-116">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="a34a2-116">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="a34a2-117">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="a34a2-117">Not Supported&dagger;</span></span> |

<span data-ttu-id="a34a2-118">&dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="a34a2-118">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### Blazor Server

| <span data-ttu-id="a34a2-119">浏览者</span><span class="sxs-lookup"><span data-stu-id="a34a2-119">Browser</span></span>                          | <span data-ttu-id="a34a2-120">Version</span><span class="sxs-lookup"><span data-stu-id="a34a2-120">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="a34a2-121">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="a34a2-121">Microsoft Edge</span></span>                   | <span data-ttu-id="a34a2-122">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-122">Current</span></span>    |
| <span data-ttu-id="a34a2-123">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="a34a2-123">Mozilla Firefox</span></span>                  | <span data-ttu-id="a34a2-124">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-124">Current</span></span>    |
| <span data-ttu-id="a34a2-125">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="a34a2-125">Google Chrome, including Android</span></span> | <span data-ttu-id="a34a2-126">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-126">Current</span></span>    |
| <span data-ttu-id="a34a2-127">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="a34a2-127">Safari, including iOS</span></span>            | <span data-ttu-id="a34a2-128">当前</span><span class="sxs-lookup"><span data-stu-id="a34a2-128">Current</span></span>    |
| <span data-ttu-id="a34a2-129">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="a34a2-129">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="a34a2-130">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="a34a2-130">11&dagger;</span></span> |

<span data-ttu-id="a34a2-131">&dagger;需要其他填充代码（例如，可通过 [`Polyfill.io`](https://polyfill.io/v3/) 捆绑包添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="a34a2-131">&dagger;Additional polyfills are required (for example, promises can be added via a [`Polyfill.io`](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a34a2-132">其他资源</span><span class="sxs-lookup"><span data-stu-id="a34a2-132">Additional resources</span></span>

* <xref:blazor/hosting-models>
