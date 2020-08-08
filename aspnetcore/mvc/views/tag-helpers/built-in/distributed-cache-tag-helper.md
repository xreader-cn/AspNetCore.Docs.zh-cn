---
title: ASP.NET Core 中的分布式缓存标记帮助程序
author: pkellner
description: 了解如何使用分布式缓存标记帮助程序。
ms.author: riande
ms.custom: mvc
ms.date: 01/24/2020
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper
ms.openlocfilehash: a7cc45e1bcc0d0d2bdd09c4ba1f0ec891e4accef
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88018671"
---
# <a name="distributed-cache-tag-helper-in-aspnet-core"></a><span data-ttu-id="172d9-103">ASP.NET Core 中的分布式缓存标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="172d9-103">Distributed Cache Tag Helper in ASP.NET Core</span></span>

<span data-ttu-id="172d9-104">作者：[Peter Kellner](https://peterkellner.net)</span><span class="sxs-lookup"><span data-stu-id="172d9-104">By [Peter Kellner](https://peterkellner.net)</span></span>

<span data-ttu-id="172d9-105">分布式缓存标记帮助程序将其内容缓存到分布式缓存源，从而大幅提高 ASP.NET Core 应用的性能。</span><span class="sxs-lookup"><span data-stu-id="172d9-105">The Distributed Cache Tag Helper provides the ability to dramatically improve the performance of your ASP.NET Core app by caching its content to a distributed cache source.</span></span>

<span data-ttu-id="172d9-106">有关标记帮助程序的概述，请参阅 <xref:mvc/views/tag-helpers/intro>。</span><span class="sxs-lookup"><span data-stu-id="172d9-106">For an overview of Tag Helpers, see <xref:mvc/views/tag-helpers/intro>.</span></span>

<span data-ttu-id="172d9-107">分布式缓存标记帮助程序与缓存标记帮助程序继承自相同的基类。</span><span class="sxs-lookup"><span data-stu-id="172d9-107">The Distributed Cache Tag Helper inherits from the same base class as the Cache Tag Helper.</span></span> <span data-ttu-id="172d9-108">分布式标记帮助程序可以使用所有[缓存标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)属性。</span><span class="sxs-lookup"><span data-stu-id="172d9-108">All of the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper) attributes are available to the Distributed Tag Helper.</span></span>

<span data-ttu-id="172d9-109">分布式缓存标记帮助程序使用[构造函数注入](xref:fundamentals/dependency-injection#constructor-injection-behavior)。</span><span class="sxs-lookup"><span data-stu-id="172d9-109">The Distributed Cache Tag Helper uses [constructor injection](xref:fundamentals/dependency-injection#constructor-injection-behavior).</span></span> <span data-ttu-id="172d9-110"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口将传递到分布式缓存标记帮助程序的构造函数中。</span><span class="sxs-lookup"><span data-stu-id="172d9-110">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface is passed into the Distributed Cache Tag Helper's constructor.</span></span> <span data-ttu-id="172d9-111">如果在 `Startup.ConfigureServices`(Startup.cs) 中未创建 `IDistributedCache` 的具体实现，则分布式缓存标记帮助程序会使用与[缓存标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)用于存储缓存数据相同的内存中提供程序。\*\*</span><span class="sxs-lookup"><span data-stu-id="172d9-111">If no concrete implementation of `IDistributedCache` is created in `Startup.ConfigureServices` (*Startup.cs*), the Distributed Cache Tag Helper uses the same in-memory provider for storing cached data as the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

## <a name="distributed-cache-tag-helper-attributes"></a><span data-ttu-id="172d9-112">分布式缓存标记帮助程序属性</span><span class="sxs-lookup"><span data-stu-id="172d9-112">Distributed Cache Tag Helper Attributes</span></span>

### <a name="attributes-shared-with-the-cache-tag-helper"></a><span data-ttu-id="172d9-113">与缓存标记帮助程序共享的属性</span><span class="sxs-lookup"><span data-stu-id="172d9-113">Attributes shared with the Cache Tag Helper</span></span>

* `enabled`
* `expires-on`
* `expires-after`
* `expires-sliding`
* `vary-by-header`
* `vary-by-query`
* `vary-by-route`
* `vary-by-cookie`
* `vary-by-user`
* `vary-by priority`

<span data-ttu-id="172d9-114">分布式缓存标记帮助程序继承自与缓存标记帮助程序相同的类。</span><span class="sxs-lookup"><span data-stu-id="172d9-114">The Distributed Cache Tag Helper inherits from the same class as Cache Tag Helper.</span></span> <span data-ttu-id="172d9-115">有关这些属性的说明，请参阅[缓存标记帮助程序](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper)。</span><span class="sxs-lookup"><span data-stu-id="172d9-115">For descriptions of these attributes, see the [Cache Tag Helper](xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper).</span></span>

### <a name="name"></a><span data-ttu-id="172d9-116">name</span><span class="sxs-lookup"><span data-stu-id="172d9-116">name</span></span>

| <span data-ttu-id="172d9-117">属性类型</span><span class="sxs-lookup"><span data-stu-id="172d9-117">Attribute Type</span></span> | <span data-ttu-id="172d9-118">示例</span><span class="sxs-lookup"><span data-stu-id="172d9-118">Example</span></span>                               |
| -------------- | ------------------------------------- |
| <span data-ttu-id="172d9-119">字符串</span><span class="sxs-lookup"><span data-stu-id="172d9-119">String</span></span>         | `my-distributed-cache-unique-key-101` |

<span data-ttu-id="172d9-120">需要 `name`。</span><span class="sxs-lookup"><span data-stu-id="172d9-120">`name` is required.</span></span> <span data-ttu-id="172d9-121">`name` 属性用作每个存储的缓存实例的键。</span><span class="sxs-lookup"><span data-stu-id="172d9-121">The `name` attribute is used as a key for each stored cache instance.</span></span> <span data-ttu-id="172d9-122">与缓存标记帮助程序不同的是，基于页面名称和页面中的位置将缓存键分配给每个实例 Razor Razor ，分布式缓存标记帮助程序只将其密钥基于属性 `name` 。</span><span class="sxs-lookup"><span data-stu-id="172d9-122">Unlike the Cache Tag Helper that assigns a cache key to each instance based on the Razor page name and location in the Razor page, the Distributed Cache Tag Helper only bases its key on the attribute `name`.</span></span>

<span data-ttu-id="172d9-123">示例：</span><span class="sxs-lookup"><span data-stu-id="172d9-123">Example:</span></span>

```cshtml
<distributed-cache name="my-distributed-cache-unique-key-101">
    Time Inside Cache Tag Helper: @DateTime.Now
</distributed-cache>
```

## <a name="distributed-cache-tag-helper-idistributedcache-implementations"></a><span data-ttu-id="172d9-124">分布式缓存标记帮助程序 IDistributedCache 实现</span><span class="sxs-lookup"><span data-stu-id="172d9-124">Distributed Cache Tag Helper IDistributedCache implementations</span></span>

<span data-ttu-id="172d9-125">ASP.NET Core 中内置了 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的两个实现。</span><span class="sxs-lookup"><span data-stu-id="172d9-125">There are two implementations of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> built in to ASP.NET Core.</span></span> <span data-ttu-id="172d9-126">一个是基于 SQL Server，另一个是基于 Redis。</span><span class="sxs-lookup"><span data-stu-id="172d9-126">One is based on SQL Server, and the other is based on Redis.</span></span> <span data-ttu-id="172d9-127">还提供第三方实现，如 [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)。</span><span class="sxs-lookup"><span data-stu-id="172d9-127">Third-party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html).</span></span> <span data-ttu-id="172d9-128">有关这些实现的详细信息，请参阅 <xref:performance/caching/distributed>。</span><span class="sxs-lookup"><span data-stu-id="172d9-128">Details of these implementations can be found at <xref:performance/caching/distributed>.</span></span> <span data-ttu-id="172d9-129">这两种实现都需要在 `Startup` 中设置 `IDistributedCache` 的实例。</span><span class="sxs-lookup"><span data-stu-id="172d9-129">Both implementations involve setting an instance of `IDistributedCache` in `Startup`.</span></span>

<span data-ttu-id="172d9-130">没有专门与使用 `IDistributedCache` 的任何特定实现相关的标记属性。</span><span class="sxs-lookup"><span data-stu-id="172d9-130">There are no tag attributes specifically associated with using any specific implementation of `IDistributedCache`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="172d9-131">其他资源</span><span class="sxs-lookup"><span data-stu-id="172d9-131">Additional resources</span></span>

* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:fundamentals/dependency-injection>
* <xref:performance/caching/distributed>
* <xref:performance/caching/memory>
* <xref:security/authentication/identity>
