---
title: ASP.NET Core Blazor 支持的平台
author: guardrex
description: 了解 ASP.NET Core Blazor的支持平台。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76160127"
---
# <a name="aspnet-core-opno-locblazor-supported-platforms"></a><span data-ttu-id="2b105-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="2b105-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="2b105-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="2b105-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="2b105-105">浏览器需求</span><span class="sxs-lookup"><span data-stu-id="2b105-105">Browser requirements</span></span>

### <a name="opno-locblazor-webassembly"></a>Blazor<span data-ttu-id="2b105-106"> WebAssembly</span><span class="sxs-lookup"><span data-stu-id="2b105-106"> WebAssembly</span></span>

| <span data-ttu-id="2b105-107">浏览器</span><span class="sxs-lookup"><span data-stu-id="2b105-107">Browser</span></span>                          | <span data-ttu-id="2b105-108">{2&gt;版本&lt;2}</span><span class="sxs-lookup"><span data-stu-id="2b105-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="2b105-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="2b105-109">Microsoft Edge</span></span>                   | <span data-ttu-id="2b105-110">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-110">Current</span></span>               |
| <span data-ttu-id="2b105-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="2b105-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="2b105-112">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-112">Current</span></span>               |
| <span data-ttu-id="2b105-113">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="2b105-113">Google Chrome, including Android</span></span> | <span data-ttu-id="2b105-114">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-114">Current</span></span>               |
| <span data-ttu-id="2b105-115">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="2b105-115">Safari, including iOS</span></span>            | <span data-ttu-id="2b105-116">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-116">Current</span></span>               |
| <span data-ttu-id="2b105-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="2b105-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="2b105-118">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b105-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="2b105-119">&dagger;Microsoft Internet Explorer 不支持[WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="2b105-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="opno-locblazor-server"></a>Blazor<span data-ttu-id="2b105-120"> 服务器</span><span class="sxs-lookup"><span data-stu-id="2b105-120"> Server</span></span>

| <span data-ttu-id="2b105-121">浏览器</span><span class="sxs-lookup"><span data-stu-id="2b105-121">Browser</span></span>                          | <span data-ttu-id="2b105-122">{2&gt;版本&lt;2}</span><span class="sxs-lookup"><span data-stu-id="2b105-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="2b105-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="2b105-123">Microsoft Edge</span></span>                   | <span data-ttu-id="2b105-124">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-124">Current</span></span>    |
| <span data-ttu-id="2b105-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="2b105-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="2b105-126">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-126">Current</span></span>    |
| <span data-ttu-id="2b105-127">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="2b105-127">Google Chrome, including Android</span></span> | <span data-ttu-id="2b105-128">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-128">Current</span></span>    |
| <span data-ttu-id="2b105-129">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="2b105-129">Safari, including iOS</span></span>            | <span data-ttu-id="2b105-130">当前</span><span class="sxs-lookup"><span data-stu-id="2b105-130">Current</span></span>    |
| <span data-ttu-id="2b105-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="2b105-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="2b105-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="2b105-132">11&dagger;</span></span> |

<span data-ttu-id="2b105-133">需要 &dagger;其他填充代码（例如，可通过[Polyfill.io](https://polyfill.io/v3/)捆绑添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="2b105-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2b105-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="2b105-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
