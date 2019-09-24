---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor 的受支持平台。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/23/2019
uid: blazor/supported-platforms
ms.openlocfilehash: b769ee175cde7c9a613d7fb70949de129ca428d3
ms.sourcegitcommit: 79eeb17604b536e8f34641d1e6b697fb9a2ee21f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2019
ms.locfileid: "71211565"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="03146-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="03146-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="03146-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="03146-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="03146-105">浏览器要求</span><span class="sxs-lookup"><span data-stu-id="03146-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="03146-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="03146-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="03146-107">浏览者</span><span class="sxs-lookup"><span data-stu-id="03146-107">Browser</span></span>                          | <span data-ttu-id="03146-108">Version</span><span class="sxs-lookup"><span data-stu-id="03146-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="03146-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="03146-109">Microsoft Edge</span></span>                   | <span data-ttu-id="03146-110">当前</span><span class="sxs-lookup"><span data-stu-id="03146-110">Current</span></span>               |
| <span data-ttu-id="03146-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="03146-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="03146-112">当前</span><span class="sxs-lookup"><span data-stu-id="03146-112">Current</span></span>               |
| <span data-ttu-id="03146-113">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="03146-113">Google Chrome, including Android</span></span> | <span data-ttu-id="03146-114">当前</span><span class="sxs-lookup"><span data-stu-id="03146-114">Current</span></span>               |
| <span data-ttu-id="03146-115">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="03146-115">Safari, including iOS</span></span>            | <span data-ttu-id="03146-116">当前</span><span class="sxs-lookup"><span data-stu-id="03146-116">Current</span></span>               |
| <span data-ttu-id="03146-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="03146-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="03146-118">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="03146-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="03146-119">&dagger;Microsoft Internet Explorer 不支持[WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="03146-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="03146-120">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="03146-120">Blazor Server</span></span>

| <span data-ttu-id="03146-121">浏览者</span><span class="sxs-lookup"><span data-stu-id="03146-121">Browser</span></span>                          | <span data-ttu-id="03146-122">Version</span><span class="sxs-lookup"><span data-stu-id="03146-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="03146-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="03146-123">Microsoft Edge</span></span>                   | <span data-ttu-id="03146-124">当前</span><span class="sxs-lookup"><span data-stu-id="03146-124">Current</span></span>    |
| <span data-ttu-id="03146-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="03146-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="03146-126">当前</span><span class="sxs-lookup"><span data-stu-id="03146-126">Current</span></span>    |
| <span data-ttu-id="03146-127">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="03146-127">Google Chrome, including Android</span></span> | <span data-ttu-id="03146-128">当前</span><span class="sxs-lookup"><span data-stu-id="03146-128">Current</span></span>    |
| <span data-ttu-id="03146-129">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="03146-129">Safari, including iOS</span></span>            | <span data-ttu-id="03146-130">当前</span><span class="sxs-lookup"><span data-stu-id="03146-130">Current</span></span>    |
| <span data-ttu-id="03146-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="03146-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="03146-132">11x17&dagger;</span><span class="sxs-lookup"><span data-stu-id="03146-132">11&dagger;</span></span> |

<span data-ttu-id="03146-133">&dagger;需要额外的填充代码（例如，可通过[Polyfill.io](https://polyfill.io/v3/)捆绑添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="03146-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="03146-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="03146-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
