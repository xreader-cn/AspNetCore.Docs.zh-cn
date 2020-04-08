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
- SignalR
uid: blazor/supported-platforms
ms.openlocfilehash: 505974280b5c96ec2bcae42c6e076ab67a15bb07
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78647106"
---
# <a name="aspnet-core-blazor-supported-platforms"></a><span data-ttu-id="54121-103">ASP.NET Core Blazor 支持的平台</span><span class="sxs-lookup"><span data-stu-id="54121-103">ASP.NET Core Blazor supported platforms</span></span>

<span data-ttu-id="54121-104">作者：[Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="54121-104">By [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

## <a name="browser-requirements"></a><span data-ttu-id="54121-105">浏览器要求</span><span class="sxs-lookup"><span data-stu-id="54121-105">Browser requirements</span></span>

### <a name="blazor-webassembly"></a><span data-ttu-id="54121-106">Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="54121-106">Blazor WebAssembly</span></span>

| <span data-ttu-id="54121-107">浏览器</span><span class="sxs-lookup"><span data-stu-id="54121-107">Browser</span></span>                          | <span data-ttu-id="54121-108">Version</span><span class="sxs-lookup"><span data-stu-id="54121-108">Version</span></span>               |
| -------------------------------- | :-------------------: |
| <span data-ttu-id="54121-109">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="54121-109">Microsoft Edge</span></span>                   | <span data-ttu-id="54121-110">当前</span><span class="sxs-lookup"><span data-stu-id="54121-110">Current</span></span>               |
| <span data-ttu-id="54121-111">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="54121-111">Mozilla Firefox</span></span>                  | <span data-ttu-id="54121-112">当前</span><span class="sxs-lookup"><span data-stu-id="54121-112">Current</span></span>               |
| <span data-ttu-id="54121-113">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="54121-113">Google Chrome, including Android</span></span> | <span data-ttu-id="54121-114">当前</span><span class="sxs-lookup"><span data-stu-id="54121-114">Current</span></span>               |
| <span data-ttu-id="54121-115">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="54121-115">Safari, including iOS</span></span>            | <span data-ttu-id="54121-116">当前</span><span class="sxs-lookup"><span data-stu-id="54121-116">Current</span></span>               |
| <span data-ttu-id="54121-117">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="54121-117">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="54121-118">不支持&dagger;</span><span class="sxs-lookup"><span data-stu-id="54121-118">Not Supported&dagger;</span></span> |

<span data-ttu-id="54121-119">&dagger;Microsoft Internet Explorer 不支持 [WebAssembly](https://webassembly.org)。</span><span class="sxs-lookup"><span data-stu-id="54121-119">&dagger;Microsoft Internet Explorer doesn't support [WebAssembly](https://webassembly.org).</span></span>

### <a name="blazor-server"></a><span data-ttu-id="54121-120">Blazor 服务器</span><span class="sxs-lookup"><span data-stu-id="54121-120">Blazor Server</span></span>

| <span data-ttu-id="54121-121">浏览器</span><span class="sxs-lookup"><span data-stu-id="54121-121">Browser</span></span>                          | <span data-ttu-id="54121-122">Version</span><span class="sxs-lookup"><span data-stu-id="54121-122">Version</span></span>    |
| -------------------------------- | :--------: |
| <span data-ttu-id="54121-123">Microsoft Edge</span><span class="sxs-lookup"><span data-stu-id="54121-123">Microsoft Edge</span></span>                   | <span data-ttu-id="54121-124">当前</span><span class="sxs-lookup"><span data-stu-id="54121-124">Current</span></span>    |
| <span data-ttu-id="54121-125">Mozilla Firefox</span><span class="sxs-lookup"><span data-stu-id="54121-125">Mozilla Firefox</span></span>                  | <span data-ttu-id="54121-126">当前</span><span class="sxs-lookup"><span data-stu-id="54121-126">Current</span></span>    |
| <span data-ttu-id="54121-127">Google Chrome，包括 Android</span><span class="sxs-lookup"><span data-stu-id="54121-127">Google Chrome, including Android</span></span> | <span data-ttu-id="54121-128">当前</span><span class="sxs-lookup"><span data-stu-id="54121-128">Current</span></span>    |
| <span data-ttu-id="54121-129">Safari，包括 iOS</span><span class="sxs-lookup"><span data-stu-id="54121-129">Safari, including iOS</span></span>            | <span data-ttu-id="54121-130">当前</span><span class="sxs-lookup"><span data-stu-id="54121-130">Current</span></span>    |
| <span data-ttu-id="54121-131">Microsoft Internet Explorer</span><span class="sxs-lookup"><span data-stu-id="54121-131">Microsoft Internet Explorer</span></span>      | <span data-ttu-id="54121-132">11&dagger;</span><span class="sxs-lookup"><span data-stu-id="54121-132">11&dagger;</span></span> |

<span data-ttu-id="54121-133">&dagger;需要其他填充代码（例如，可通过 [Polyfill.io](https://polyfill.io/v3/) 捆绑包添加承诺）。</span><span class="sxs-lookup"><span data-stu-id="54121-133">&dagger;Additional polyfills are required (for example, promises can be added via a [Polyfill.io](https://polyfill.io/v3/) bundle).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="54121-134">其他资源</span><span class="sxs-lookup"><span data-stu-id="54121-134">Additional resources</span></span>

* <xref:blazor/hosting-models>
