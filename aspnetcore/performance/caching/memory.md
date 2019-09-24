---
title: 缓存在内存中 ASP.NET Core
author: rick-anderson
description: 了解如何在 ASP.NET Core 中的内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 8/22/2019
uid: performance/caching/memory
ms.openlocfilehash: 0479df14dd5d757710e361fce43782a8137588cb
ms.sourcegitcommit: 0365af91518004c4a44a30dc3a8ac324558a399b
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/23/2019
ms.locfileid: "71198905"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="d8bce-103">缓存在内存中 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="d8bce-103">Cache in-memory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d8bce-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d8bce-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d8bce-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d8bce-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="d8bce-106">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="d8bce-106">Caching basics</span></span>

<span data-ttu-id="d8bce-107">通过减少生成内容所需的工作，缓存可以显著提高应用的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="d8bce-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="d8bce-108">缓存最适用于不经常更改的**数据，生成**成本很高。</span><span class="sxs-lookup"><span data-stu-id="d8bce-108">Caching works best with data that changes infrequently **and** is expensive to generate.</span></span> <span data-ttu-id="d8bce-109">通过缓存，可以比从数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="d8bce-109">Caching makes a copy of data that can be returned much faster than from the source.</span></span> <span data-ttu-id="d8bce-110">应该对应用进行编写和测试，使其**永不**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bce-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="d8bce-111">ASP.NET Core 支持多种不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="d8bce-112">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="d8bce-113">`IMemoryCache`表示存储在 web 服务器的内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-113">`IMemoryCache` represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="d8bce-114">使用内存中缓存时，在服务器场（多台服务器）上运行的应用应确保会话是粘滞的。</span><span class="sxs-lookup"><span data-stu-id="d8bce-114">Apps running on a server farm (multiple servers) should ensure sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="d8bce-115">粘性会话可确保来自客户端的后续请求都转到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="d8bce-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="d8bce-116">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="d8bce-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="d8bce-117">Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="d8bce-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="d8bce-118">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="d8bce-118">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="d8bce-119">使用分布式缓存可将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="d8bce-119">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="d8bce-120">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="d8bce-120">The in-memory cache can store any object.</span></span> <span data-ttu-id="d8bce-121">分布式缓存接口仅限`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="d8bce-121">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="d8bce-122">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="d8bce-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="d8bce-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="d8bce-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="d8bce-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>（[NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)）可用于：</span><span class="sxs-lookup"><span data-stu-id="d8bce-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="d8bce-125">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8bce-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="d8bce-126">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="d8bce-127">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8bce-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="d8bce-128">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8bce-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="d8bce-129">建议对 `System.Runtime.Caching`/`MemoryCache` 使用 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (本文中所述), 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="d8bce-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="d8bce-130">例如， `IMemoryCache`使用 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="d8bce-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d8bce-131">将`System.Runtime.Caching` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，请使用/ `MemoryCache`作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="d8bce-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="d8bce-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="d8bce-132">Cache guidelines</span></span>

* <span data-ttu-id="d8bce-133">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="d8bce-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="d8bce-134">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="d8bce-135">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="d8bce-135">Limit cache growth:</span></span>
  * <span data-ttu-id="d8bce-136">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="d8bce-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="d8bce-137">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="d8bce-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="d8bce-138">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="d8bce-139">ASP.NET Core 运行时不会根据内存**压力限制缓存**大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-139">The ASP.NET Core runtime does **not** limit cache size based on memory pressure.</span></span> <span data-ttu-id="d8bce-140">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-140">It's up to the developer to limit cache size.</span></span>

## <a name="use-imemorycache"></a><span data-ttu-id="d8bce-141">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="d8bce-141">Use IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="d8bce-142">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存， `SetSize`并`Size`调用、 `SizeLimit`或以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="d8bce-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="d8bce-143">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="d8bce-144">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="d8bce-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="d8bce-145">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="d8bce-146">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发`InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="d8bce-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="d8bce-147">当使用`SetSize`、 `Size`或`SizeLimit`来限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="d8bce-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="d8bce-148">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="d8bce-149">内存中缓存是从应用程序中使用[依赖关系注入](xref:fundamentals/dependency-injection)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="d8bce-149">In-memory caching is a *service* that's referenced from an app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="d8bce-150">在构造函数中请求 `IMemoryCache`实例：</span><span class="sxs-lookup"><span data-stu-id="d8bce-150">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="d8bce-151">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="d8bce-151">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="d8bce-152">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="d8bce-152">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span> <span data-ttu-id="d8bce-153">`CacheKeys`类是下载示例的一部分。</span><span class="sxs-lookup"><span data-stu-id="d8bce-153">The `CacheKeys` class is part of the download sample.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="d8bce-154">将会显示当前时间和缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="d8bce-154">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

<span data-ttu-id="d8bce-155">如果在`DateTime`超时期限内存在请求，则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="d8bce-155">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span>

<span data-ttu-id="d8bce-156">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="d8bce-156">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="d8bce-157">以下代码调用[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)来提取缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="d8bce-157">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="d8bce-158">下面的代码获取或创建具有绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="d8bce-158">The following code gets or creates a cached item with absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

<span data-ttu-id="d8bce-159">只有具有可调过期的缓存项集存在过时的风险。</span><span class="sxs-lookup"><span data-stu-id="d8bce-159">A cached item set with a sliding expiration only is at risk of becoming stale.</span></span> <span data-ttu-id="d8bce-160">如果访问的时间比滑动过期时间间隔更频繁，则该项将永不过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-160">If it's accessed more frequently than the sliding expiration interval, the item will never expire.</span></span> <span data-ttu-id="d8bce-161">将弹性过期与绝对过期组合在一起，以保证项目在其绝对过期时间通过后过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-161">Combine a sliding expiration with an absolute expiration to guarantee that the item expires once its absolute expiration time passes.</span></span> <span data-ttu-id="d8bce-162">绝对过期会将项的上限设置为可缓存项的时间，同时仍允许项在可调整过期时间间隔内未请求时提前过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-162">The absolute expiration sets an upper bound to how long the item can be cached while still allowing the item to expire earlier if it isn't requested within the sliding expiration interval.</span></span> <span data-ttu-id="d8bce-163">如果同时指定了绝对过期和可调过期时间，则过期时间以逻辑方式运算。</span><span class="sxs-lookup"><span data-stu-id="d8bce-163">When both absolute and sliding expiration are specified, the expirations are logically ORed.</span></span> <span data-ttu-id="d8bce-164">如果滑动过期时间间隔*或*绝对过期时间通过，则从缓存中逐出该项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-164">If either the sliding expiration interval *or* the absolute expiration time pass, the item is evicted from the cache.</span></span>

<span data-ttu-id="d8bce-165">下面的代码获取或创建具有可调*和*绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="d8bce-165">The following code gets or creates a cached item with both sliding *and* absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

<span data-ttu-id="d8bce-166">前面的代码保证数据的缓存时间不超过绝对时间。</span><span class="sxs-lookup"><span data-stu-id="d8bce-166">The preceding code guarantees the data will not be cached longer than the absolute time.</span></span>

<span data-ttu-id="d8bce-167"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>和<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 是类中<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions>的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="d8bce-167"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> are extension methods in the <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> class.</span></span> <span data-ttu-id="d8bce-168">这些方法扩展了的<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>功能。</span><span class="sxs-lookup"><span data-stu-id="d8bce-168">These methods extend the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="d8bce-169">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="d8bce-169">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="d8bce-170">下面的示例执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d8bce-170">The following sample:</span></span>

* <span data-ttu-id="d8bce-171">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="d8bce-171">Sets a sliding expiration time.</span></span> <span data-ttu-id="d8bce-172">访问此缓存项的请求将重置可调过期时钟。</span><span class="sxs-lookup"><span data-stu-id="d8bce-172">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="d8bce-173">将缓存优先级设置为[CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-173">Sets the cache priority to [CacheItemPriority.NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).</span></span>
* <span data-ttu-id="d8bce-174">设置一个[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)它将在条目从缓存中清除后调用。</span><span class="sxs-lookup"><span data-stu-id="d8bce-174">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="d8bce-175">在代码中运行该回调的线程不同于从缓存中移除条目的线程。</span><span class="sxs-lookup"><span data-stu-id="d8bce-175">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="d8bce-176">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="d8bce-176">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="d8bce-177">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="d8bce-177">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="d8bce-178">内存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="d8bce-178">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="d8bce-179">如果设置了缓存内存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="d8bce-179">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="d8bce-180">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-180">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="d8bce-181">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-181">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="d8bce-182">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="d8bce-182">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="d8bce-183">例如:</span><span class="sxs-lookup"><span data-stu-id="d8bce-183">For example:</span></span>

* <span data-ttu-id="d8bce-184">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="d8bce-184">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="d8bce-185">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="d8bce-185">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="d8bce-186">如果<xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>未设置，则缓存的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="d8bce-186">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="d8bce-187">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-187">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="d8bce-188">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="d8bce-188">Apps much be architected to:</span></span>

* <span data-ttu-id="d8bce-189">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="d8bce-189">Limit cache growth.</span></span>
* <span data-ttu-id="d8bce-190">当<xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*>可用<xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*>内存有限时调用或：</span><span class="sxs-lookup"><span data-stu-id="d8bce-190">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="d8bce-191">以下代码创建了[依赖关系注入](xref:fundamentals/dependency-injection)可<xref:Microsoft.Extensions.Caching.Memory.MemoryCache>访问的无大小固定大小：</span><span class="sxs-lookup"><span data-stu-id="d8bce-191">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="d8bce-192">`SizeLimit`没有单位。</span><span class="sxs-lookup"><span data-stu-id="d8bce-192">`SizeLimit` does not have units.</span></span> <span data-ttu-id="d8bce-193">如果已设置缓存内存大小，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-193">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="d8bce-194">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="d8bce-194">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="d8bce-195">如果缓存条目大小之和超过指定`SizeLimit`的值，则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="d8bce-195">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="d8bce-196">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-196">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="d8bce-197">以下代码将注册`MyMemoryCache`到[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="d8bce-197">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

<span data-ttu-id="d8bce-198">`MyMemoryCache`对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件，将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-198">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="d8bce-199">以下代码使用`MyMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="d8bce-199">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

<span data-ttu-id="d8bce-200">缓存项的大小可以通过<xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*>或扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="d8bce-200">The size of the cache entry can be set by <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> or the <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> extension methods:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="d8bce-201">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="d8bce-201">MemoryCache.Compact</span></span>

<span data-ttu-id="d8bce-202">`MemoryCache.Compact`尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="d8bce-202">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="d8bce-203">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-203">All expired items.</span></span>
* <span data-ttu-id="d8bce-204">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="d8bce-204">Items by priority.</span></span> <span data-ttu-id="d8bce-205">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-205">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="d8bce-206">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="d8bce-206">Least recently used objects.</span></span>
* <span data-ttu-id="d8bce-207">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-207">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="d8bce-208">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-208">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="d8bce-209">永远不会删除<xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove>具有优先级的固定项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-209">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span> <span data-ttu-id="d8bce-210">以下代码将删除缓存项并调用`Compact`：</span><span class="sxs-lookup"><span data-stu-id="d8bce-210">The following code removes a cache item and calls `Compact`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="d8bce-211">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-211">See [Compact source on GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="d8bce-212">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="d8bce-212">Cache dependencies</span></span>

<span data-ttu-id="d8bce-213">以下示例演示在依赖项过期时如何使缓存项过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-213">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="d8bce-214">会将 `CancellationChangeToken`添加到缓存项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-214">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="d8bce-215">在 `Cancel`上调用 `CancellationTokenSource`，时，这两个缓存条目都将被清除。</span><span class="sxs-lookup"><span data-stu-id="d8bce-215">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="d8bce-216">使用`CancellationTokenSource`可以将多个缓存条目作为一个组来清除。</span><span class="sxs-lookup"><span data-stu-id="d8bce-216">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="d8bce-217">使用上面代码中的 `using`模式时`using`块中创建的缓存条目会继承触发器和过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="d8bce-217">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="d8bce-218">附加说明</span><span class="sxs-lookup"><span data-stu-id="d8bce-218">Additional notes</span></span>

* <span data-ttu-id="d8bce-219">不会在后台进行过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-219">Expiration doesn't happen in the background.</span></span> <span data-ttu-id="d8bce-220">没有计时器可主动扫描过期项目的缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-220">There is no timer that actively scans the cache for expired items.</span></span> <span data-ttu-id="d8bce-221">缓存中的任何活动（`Get`、 `Set`、 `Remove`）都可以触发过期项的后台扫描。</span><span class="sxs-lookup"><span data-stu-id="d8bce-221">Any activity on the cache (`Get`, `Set`, `Remove`) can trigger a background scan for expired items.</span></span> <span data-ttu-id="d8bce-222">`CancellationTokenSource` （`CancelAfter`）上的计时器还会删除该条目，并触发扫描过期的项目。</span><span class="sxs-lookup"><span data-stu-id="d8bce-222">A timer on the `CancellationTokenSource` (`CancelAfter`) would also remove the entry and trigger a scan for expired items.</span></span> <span data-ttu-id="d8bce-223">例如，不使用`SetAbsoluteExpiration(TimeSpan.FromHours(1))`，而是将`CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))`用于注册的令牌。</span><span class="sxs-lookup"><span data-stu-id="d8bce-223">For example, rather than using `SetAbsoluteExpiration(TimeSpan.FromHours(1))`, use `CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))` for the registered token.</span></span> <span data-ttu-id="d8bce-224">此令牌激发后，会立即删除该条目，并激发逐出回调。</span><span class="sxs-lookup"><span data-stu-id="d8bce-224">When this token fires it removes the entry immediately and fires the eviction callbacks.</span></span> <span data-ttu-id="d8bce-225">有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Caching/issues/248)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-225">For more information, see [this GitHub issue](https://github.com/aspnet/Caching/issues/248).</span></span>
* <span data-ttu-id="d8bce-226">使用回调重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="d8bce-226">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="d8bce-227">多个请求可能会发现缓存的键值为空，因为回调尚未完成。</span><span class="sxs-lookup"><span data-stu-id="d8bce-227">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="d8bce-228">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-228">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="d8bce-229">使用一个缓存条目创建另一个缓存条目时，子条目会复制父条目的过期令牌以及基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="d8bce-229">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="d8bce-230">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-230">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="d8bce-231">用于<xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks>设置在从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="d8bce-231">Use <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>
* <span data-ttu-id="d8bce-232">对于大多数应用， `IMemoryCache`启用。</span><span class="sxs-lookup"><span data-stu-id="d8bce-232">For most apps, `IMemoryCache` is enabled.</span></span> <span data-ttu-id="d8bce-233">例如，在中`AddMvc` `AddControllersWithViews` `AddRazorPages` `Add{Service}`调用、 `AddMvcCore().AddRazorViewEngine`、、和许多其他方法将启用`IMemoryCache`。 `ConfigureServices`</span><span class="sxs-lookup"><span data-stu-id="d8bce-233">For example, calling `AddMvc`, `AddControllersWithViews`, `AddRazorPages`, `AddMvcCore().AddRazorViewEngine`, and many other `Add{Service}` methods in `ConfigureServices`, enables `IMemoryCache`.</span></span> <span data-ttu-id="d8bce-234">对于未调用上述`Add{Service}`方法之一的应用程序，可能需要在中`ConfigureServices`调用<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> 。</span><span class="sxs-lookup"><span data-stu-id="d8bce-234">For apps that are not calling one of the preceding `Add{Service}` methods, it may be necessary to call <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> in `ConfigureServices`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8bce-235">其他资源</span><span class="sxs-lookup"><span data-stu-id="d8bce-235">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
<span data-ttu-id="d8bce-236">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d8bce-236">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d8bce-237">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="d8bce-237">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="d8bce-238">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="d8bce-238">Caching basics</span></span>

<span data-ttu-id="d8bce-239">通过减少生成内容所需的工作，缓存可以显著提高应用的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="d8bce-239">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="d8bce-240">缓存对不经常更改的数据效果最佳。</span><span class="sxs-lookup"><span data-stu-id="d8bce-240">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="d8bce-241">缓存生成的数据副本的返回速度可以比从原始源返回更快。</span><span class="sxs-lookup"><span data-stu-id="d8bce-241">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="d8bce-242">应编写并测试代码，使其**绝不会**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="d8bce-242">Code should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="d8bce-243">ASP.NET Core 支持多种不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-243">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="d8bce-244">最简单的缓存基于 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在 Web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-244">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="d8bce-245">在服务器场中运行的应用（多台服务器）应确保会话在使用内存中缓存时处于粘滞。</span><span class="sxs-lookup"><span data-stu-id="d8bce-245">Apps that run on a server farm (multiple servers) should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="d8bce-246">粘滞会话确保以后来自客户端的请求都发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="d8bce-246">Sticky sessions ensure that later requests from a client all go to the same server.</span></span> <span data-ttu-id="d8bce-247">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)（ARR）将来自用户代理的所有请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="d8bce-247">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all requests from a user agent to the same server.</span></span>

<span data-ttu-id="d8bce-248">Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="d8bce-248">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="d8bce-249">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="d8bce-249">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="d8bce-250">使用分布式缓存可将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="d8bce-250">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="d8bce-251">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="d8bce-251">The in-memory cache can store any object.</span></span> <span data-ttu-id="d8bce-252">分布式缓存接口仅限`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="d8bce-252">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="d8bce-253">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="d8bce-253">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="d8bce-254">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="d8bce-254">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="d8bce-255"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>（[NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)）可用于：</span><span class="sxs-lookup"><span data-stu-id="d8bce-255"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="d8bce-256">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8bce-256">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="d8bce-257">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-257">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="d8bce-258">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8bce-258">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="d8bce-259">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="d8bce-259">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="d8bce-260">建议对 `System.Runtime.Caching`/`MemoryCache` 使用 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (本文中所述), 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="d8bce-260">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="d8bce-261">例如， `IMemoryCache`使用 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="d8bce-261">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="d8bce-262">将`System.Runtime.Caching` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，请使用/ `MemoryCache`作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="d8bce-262">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="d8bce-263">缓存指南</span><span class="sxs-lookup"><span data-stu-id="d8bce-263">Cache guidelines</span></span>

* <span data-ttu-id="d8bce-264">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="d8bce-264">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="d8bce-265">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-265">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="d8bce-266">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="d8bce-266">Limit cache growth:</span></span>
  * <span data-ttu-id="d8bce-267">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="d8bce-267">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="d8bce-268">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="d8bce-268">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="d8bce-269">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-269">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="d8bce-270">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-270">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="d8bce-271">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-271">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="d8bce-272">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="d8bce-272">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="d8bce-273">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存， `SetSize`并`Size`调用、 `SizeLimit`或以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="d8bce-273">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="d8bce-274">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-274">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="d8bce-275">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="d8bce-275">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="d8bce-276">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-276">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="d8bce-277">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发`InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="d8bce-277">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="d8bce-278">当使用`SetSize`、 `Size`或`SizeLimit`来限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="d8bce-278">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="d8bce-279">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-279">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="d8bce-280">内存中缓存是使用[依赖关系注入](../../fundamentals/dependency-injection.md)从应用中引用的服务。</span><span class="sxs-lookup"><span data-stu-id="d8bce-280">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="d8bce-281">请在`ConfigureServices`中调用`AddMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="d8bce-281">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="d8bce-282">在构造函数中请求 `IMemoryCache`实例：</span><span class="sxs-lookup"><span data-stu-id="d8bce-282">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="d8bce-283">`IMemoryCache`需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), 此包可在[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)中找到的。</span><span class="sxs-lookup"><span data-stu-id="d8bce-283">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="d8bce-284">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="d8bce-284">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="d8bce-285">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="d8bce-285">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

<span data-ttu-id="d8bce-286">[！ code-csharp [] （memory/sample/WebCache/CacheKeys） [](memory/sample/WebCache/CacheKeys.cs)]</span><span class="sxs-lookup"><span data-stu-id="d8bce-286">[!code-csharp [](memory/sample/WebCache/CacheKeys.cs) [](memory/sample/WebCache/CacheKeys.cs)]</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="d8bce-287">将会显示当前时间和缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="d8bce-287">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="d8bce-288">如果在`DateTime`超时期限内存在请求，则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="d8bce-288">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="d8bce-289">下图显示当前时间以及从缓存中检索的较早时间：</span><span class="sxs-lookup"><span data-stu-id="d8bce-289">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示了两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="d8bce-291">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="d8bce-291">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="d8bce-292">以下代码调用[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)来提取缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="d8bce-292">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="d8bce-293"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>和[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)是[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>类的扩展方法的一部分，它扩展了的功能。</span><span class="sxs-lookup"><span data-stu-id="d8bce-293"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="d8bce-294">有关其他缓存方法的说明，请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-294">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="d8bce-295">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="d8bce-295">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="d8bce-296">下面的示例执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="d8bce-296">The following sample:</span></span>

* <span data-ttu-id="d8bce-297">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="d8bce-297">Sets a sliding expiration time.</span></span> <span data-ttu-id="d8bce-298">访问此缓存项的请求将重置可调过期时钟。</span><span class="sxs-lookup"><span data-stu-id="d8bce-298">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="d8bce-299">将缓存优先级设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="d8bce-299">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="d8bce-300">设置一个[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)它将在条目从缓存中清除后调用。</span><span class="sxs-lookup"><span data-stu-id="d8bce-300">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="d8bce-301">在代码中运行该回调的线程不同于从缓存中移除条目的线程。</span><span class="sxs-lookup"><span data-stu-id="d8bce-301">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="d8bce-302">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="d8bce-302">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="d8bce-303">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="d8bce-303">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="d8bce-304">内存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="d8bce-304">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="d8bce-305">如果设置了缓存内存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="d8bce-305">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="d8bce-306">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-306">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="d8bce-307">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-307">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="d8bce-308">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="d8bce-308">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="d8bce-309">例如:</span><span class="sxs-lookup"><span data-stu-id="d8bce-309">For example:</span></span>

* <span data-ttu-id="d8bce-310">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="d8bce-310">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="d8bce-311">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="d8bce-311">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="d8bce-312">如果<xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>未设置，则缓存的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="d8bce-312">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="d8bce-313">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-313">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="d8bce-314">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="d8bce-314">Apps much be architected to:</span></span>

* <span data-ttu-id="d8bce-315">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="d8bce-315">Limit cache growth.</span></span>
* <span data-ttu-id="d8bce-316">当<xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*>可用<xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*>内存有限时调用或：</span><span class="sxs-lookup"><span data-stu-id="d8bce-316">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="d8bce-317">以下代码创建了[依赖关系注入](xref:fundamentals/dependency-injection)可<xref:Microsoft.Extensions.Caching.Memory.MemoryCache>访问的无大小固定大小：</span><span class="sxs-lookup"><span data-stu-id="d8bce-317">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="d8bce-318">`SizeLimit`没有单位。</span><span class="sxs-lookup"><span data-stu-id="d8bce-318">`SizeLimit` does not have units.</span></span> <span data-ttu-id="d8bce-319">如果已设置缓存内存大小，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-319">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="d8bce-320">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="d8bce-320">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="d8bce-321">如果缓存条目大小之和超过指定`SizeLimit`的值，则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="d8bce-321">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="d8bce-322">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="d8bce-322">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="d8bce-323">以下代码将注册`MyMemoryCache`到[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="d8bce-323">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="d8bce-324">`MyMemoryCache`对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件，将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="d8bce-324">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="d8bce-325">以下代码使用`MyMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="d8bce-325">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="d8bce-326">缓存项的大小可以通过[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="d8bce-326">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="d8bce-327">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="d8bce-327">MemoryCache.Compact</span></span>

<span data-ttu-id="d8bce-328">`MemoryCache.Compact`尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="d8bce-328">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="d8bce-329">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-329">All expired items.</span></span>
* <span data-ttu-id="d8bce-330">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="d8bce-330">Items by priority.</span></span> <span data-ttu-id="d8bce-331">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-331">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="d8bce-332">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="d8bce-332">Least recently used objects.</span></span>
* <span data-ttu-id="d8bce-333">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-333">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="d8bce-334">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-334">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="d8bce-335">永远不会删除<xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove>具有优先级的固定项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-335">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="d8bce-336">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="d8bce-336">See [Compact source on GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="d8bce-337">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="d8bce-337">Cache dependencies</span></span>

<span data-ttu-id="d8bce-338">以下示例演示在依赖项过期时如何使缓存项过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-338">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="d8bce-339">会将 `CancellationChangeToken`添加到缓存项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-339">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="d8bce-340">在 `Cancel`上调用 `CancellationTokenSource`，时，这两个缓存条目都将被清除。</span><span class="sxs-lookup"><span data-stu-id="d8bce-340">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="d8bce-341">使用`CancellationTokenSource`可以将多个缓存条目作为一个组来清除。</span><span class="sxs-lookup"><span data-stu-id="d8bce-341">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="d8bce-342">使用上面代码中的 `using`模式时`using`块中创建的缓存条目会继承触发器和过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="d8bce-342">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="d8bce-343">附加说明</span><span class="sxs-lookup"><span data-stu-id="d8bce-343">Additional notes</span></span>

* <span data-ttu-id="d8bce-344">使用回调重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="d8bce-344">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="d8bce-345">多个请求可能会发现缓存的键值为空，因为回调尚未完成。</span><span class="sxs-lookup"><span data-stu-id="d8bce-345">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="d8bce-346">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="d8bce-346">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="d8bce-347">使用一个缓存条目创建另一个缓存条目时，子条目会复制父条目的过期令牌以及基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="d8bce-347">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="d8bce-348">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="d8bce-348">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="d8bce-349">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="d8bce-349">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="d8bce-350">其他资源</span><span class="sxs-lookup"><span data-stu-id="d8bce-350">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
