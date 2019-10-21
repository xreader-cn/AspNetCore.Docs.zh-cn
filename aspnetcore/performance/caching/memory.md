---
title: ASP.NET Core 中的缓存内存
author: rick-anderson
description: 了解如何在 ASP.NET Core 内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 8/22/2019
uid: performance/caching/memory
ms.openlocfilehash: aa39503f034cf46fa4317a1f3cbb8d130afd1b8c
ms.sourcegitcommit: 07d98ada57f2a5f6d809d44bdad7a15013109549
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72333740"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="cff6c-103">ASP.NET Core 中的缓存内存</span><span class="sxs-lookup"><span data-stu-id="cff6c-103">Cache in-memory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="cff6c-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cff6c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cff6c-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cff6c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="cff6c-106">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="cff6c-106">Caching basics</span></span>

<span data-ttu-id="cff6c-107">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="cff6c-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="cff6c-108">缓存最适用于不经常更改的**数据，生成**成本很高。</span><span class="sxs-lookup"><span data-stu-id="cff6c-108">Caching works best with data that changes infrequently **and** is expensive to generate.</span></span> <span data-ttu-id="cff6c-109">通过缓存，可以比从数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="cff6c-109">Caching makes a copy of data that can be returned much faster than from the source.</span></span> <span data-ttu-id="cff6c-110">应该对应用进行编写和测试，使其**永不**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="cff6c-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="cff6c-111">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="cff6c-112">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="cff6c-113">`IMemoryCache` 表示存储在 web 服务器的内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-113">`IMemoryCache` represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="cff6c-114">使用内存中缓存时，在服务器场（多台服务器）上运行的应用应确保会话是粘滞的。</span><span class="sxs-lookup"><span data-stu-id="cff6c-114">Apps running on a server farm (multiple servers) should ensure sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="cff6c-115">粘滞会话确保来自客户端的后续请求都将发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="cff6c-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="cff6c-116">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)（ARR）将所有后续请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="cff6c-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="cff6c-117">Web 场中的非粘滞会话需要[分布式缓存](distributed.md)，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="cff6c-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="cff6c-118">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="cff6c-118">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="cff6c-119">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="cff6c-119">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="cff6c-120">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="cff6c-120">The in-memory cache can store any object.</span></span> <span data-ttu-id="cff6c-121">分布式缓存接口仅限 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-121">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="cff6c-122">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="cff6c-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="cff6c-123">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="cff6c-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="cff6c-124"><xref:System.Runtime.Caching> / <xref:System.Runtime.Caching.MemoryCache> （[NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)）可用于：</span><span class="sxs-lookup"><span data-stu-id="cff6c-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="cff6c-125">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cff6c-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="cff6c-126">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="cff6c-127">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cff6c-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="cff6c-128">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cff6c-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="cff6c-129">@No__t_4 `System.Runtime.Caching` 建议将 / `IMemoryCache` （本文所述）的（本文中介绍），因为它更好地集成[到 `MemoryCache` 中](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="cff6c-130">例如，`IMemoryCache` 与 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)一起使用。</span><span class="sxs-lookup"><span data-stu-id="cff6c-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cff6c-131">将 ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，使用 `System.Runtime.Caching` / `MemoryCache` 作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="cff6c-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="cff6c-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="cff6c-132">Cache guidelines</span></span>

* <span data-ttu-id="cff6c-133">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="cff6c-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="cff6c-134">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="cff6c-135">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="cff6c-135">Limit cache growth:</span></span>
  * <span data-ttu-id="cff6c-136">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="cff6c-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="cff6c-137">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="cff6c-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="cff6c-138">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="cff6c-139">ASP.NET Core 运行时不会根据内存**压力限制缓存**大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-139">The ASP.NET Core runtime does **not** limit cache size based on memory pressure.</span></span> <span data-ttu-id="cff6c-140">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-140">It's up to the developer to limit cache size.</span></span>

## <a name="use-imemorycache"></a><span data-ttu-id="cff6c-141">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="cff6c-141">Use IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="cff6c-142">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存并调用 `SetSize`、`Size` 或 `SizeLimit` 来限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="cff6c-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="cff6c-143">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="cff6c-144">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="cff6c-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="cff6c-145">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="cff6c-146">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="cff6c-147">使用 `SetSize`、`Size` 或 `SizeLimit` 限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="cff6c-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="cff6c-148">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="cff6c-149">内存中缓存是从应用程序中使用[依赖关系注入](xref:fundamentals/dependency-injection)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="cff6c-149">In-memory caching is a *service* that's referenced from an app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="cff6c-150">请求构造函数中的 `IMemoryCache` 实例：</span><span class="sxs-lookup"><span data-stu-id="cff6c-150">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="cff6c-151">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-151">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="cff6c-152">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="cff6c-152">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span> <span data-ttu-id="cff6c-153">@No__t_0 类是下载示例的一部分。</span><span class="sxs-lookup"><span data-stu-id="cff6c-153">The `CacheKeys` class is part of the download sample.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="cff6c-154">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="cff6c-154">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

<span data-ttu-id="cff6c-155">如果在超时期限内存在请求，则缓存 `DateTime` 值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="cff6c-155">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span>

<span data-ttu-id="cff6c-156">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="cff6c-156">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="cff6c-157">以下代码调用[Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)缓存时间：</span><span class="sxs-lookup"><span data-stu-id="cff6c-157">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="cff6c-158">下面的代码获取或创建具有绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="cff6c-158">The following code gets or creates a cached item with absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

<span data-ttu-id="cff6c-159">只有具有可调过期的缓存项集存在过时的风险。</span><span class="sxs-lookup"><span data-stu-id="cff6c-159">A cached item set with a sliding expiration only is at risk of becoming stale.</span></span> <span data-ttu-id="cff6c-160">如果访问的时间比滑动过期时间间隔更频繁，则该项将永不过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-160">If it's accessed more frequently than the sliding expiration interval, the item will never expire.</span></span> <span data-ttu-id="cff6c-161">将弹性过期与绝对过期组合在一起，以保证项目在其绝对过期时间通过后过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-161">Combine a sliding expiration with an absolute expiration to guarantee that the item expires once its absolute expiration time passes.</span></span> <span data-ttu-id="cff6c-162">绝对过期会将项的上限设置为可缓存项的时间，同时仍允许项在可调整过期时间间隔内未请求时提前过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-162">The absolute expiration sets an upper bound to how long the item can be cached while still allowing the item to expire earlier if it isn't requested within the sliding expiration interval.</span></span> <span data-ttu-id="cff6c-163">如果同时指定了绝对过期和可调过期时间，则过期时间以逻辑方式运算。</span><span class="sxs-lookup"><span data-stu-id="cff6c-163">When both absolute and sliding expiration are specified, the expirations are logically ORed.</span></span> <span data-ttu-id="cff6c-164">如果滑动过期时间间隔*或*绝对过期时间通过，则从缓存中逐出该项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-164">If either the sliding expiration interval *or* the absolute expiration time pass, the item is evicted from the cache.</span></span>

<span data-ttu-id="cff6c-165">下面的代码获取或创建具有可调*和*绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="cff6c-165">The following code gets or creates a cached item with both sliding *and* absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

<span data-ttu-id="cff6c-166">前面的代码保证数据的缓存时间不超过绝对时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-166">The preceding code guarantees the data will not be cached longer than the absolute time.</span></span>

<span data-ttu-id="cff6c-167"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 是 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> 类中的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="cff6c-167"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> are extension methods in the <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> class.</span></span> <span data-ttu-id="cff6c-168">这些方法扩展了 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 的功能。</span><span class="sxs-lookup"><span data-stu-id="cff6c-168">These methods extend the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="cff6c-169">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="cff6c-169">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="cff6c-170">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="cff6c-170">The following sample:</span></span>

* <span data-ttu-id="cff6c-171">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-171">Sets a sliding expiration time.</span></span> <span data-ttu-id="cff6c-172">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-172">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="cff6c-173">将缓存优先级设置为[CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-173">Sets the cache priority to [CacheItemPriority.NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).</span></span>
* <span data-ttu-id="cff6c-174">设置在从缓存中逐出项后将调用的[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="cff6c-174">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="cff6c-175">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="cff6c-175">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="cff6c-176">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="cff6c-176">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="cff6c-177">@No__t_0 实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="cff6c-177">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="cff6c-178">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="cff6c-178">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="cff6c-179">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="cff6c-179">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="cff6c-180">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-180">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="cff6c-181">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-181">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="cff6c-182">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="cff6c-182">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="cff6c-183">例如:</span><span class="sxs-lookup"><span data-stu-id="cff6c-183">For example:</span></span>

* <span data-ttu-id="cff6c-184">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="cff6c-184">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="cff6c-185">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="cff6c-185">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="cff6c-186">如果未设置 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>，则缓存将不受限制。</span><span class="sxs-lookup"><span data-stu-id="cff6c-186">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="cff6c-187">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-187">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="cff6c-188">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="cff6c-188">Apps much be architected to:</span></span>

* <span data-ttu-id="cff6c-189">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="cff6c-189">Limit cache growth.</span></span>
* <span data-ttu-id="cff6c-190">如果可用内存有限，请调用 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*>：</span><span class="sxs-lookup"><span data-stu-id="cff6c-190">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="cff6c-191">下面的代码创建一个无单位固定大小 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> 可通过[依赖关系注入](xref:fundamentals/dependency-injection)进行访问：</span><span class="sxs-lookup"><span data-stu-id="cff6c-191">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="cff6c-192">`SizeLimit` 没有单位。</span><span class="sxs-lookup"><span data-stu-id="cff6c-192">`SizeLimit` does not have units.</span></span> <span data-ttu-id="cff6c-193">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-193">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="cff6c-194">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="cff6c-194">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="cff6c-195">如果缓存条目大小的总和超出 `SizeLimit` 指定的值，则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-195">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="cff6c-196">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-196">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="cff6c-197">下面的代码向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册 `MyMemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-197">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

<span data-ttu-id="cff6c-198">对于识别此大小限制缓存并知道如何适当设置缓存条目大小的组件，`MyMemoryCache` 创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-198">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="cff6c-199">以下代码使用 `MyMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="cff6c-199">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

<span data-ttu-id="cff6c-200">缓存项的大小可以 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> 扩展方法进行设置：</span><span class="sxs-lookup"><span data-stu-id="cff6c-200">The size of the cache entry can be set by <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> or the <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> extension methods:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="cff6c-201">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="cff6c-201">MemoryCache.Compact</span></span>

<span data-ttu-id="cff6c-202">`MemoryCache.Compact` 尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="cff6c-202">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="cff6c-203">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-203">All expired items.</span></span>
* <span data-ttu-id="cff6c-204">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="cff6c-204">Items by priority.</span></span> <span data-ttu-id="cff6c-205">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-205">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="cff6c-206">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="cff6c-206">Least recently used objects.</span></span>
* <span data-ttu-id="cff6c-207">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-207">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="cff6c-208">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-208">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="cff6c-209">永远不会删除优先级为 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 的固定项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-209">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span> <span data-ttu-id="cff6c-210">以下代码将删除缓存项并调用 `Compact`：</span><span class="sxs-lookup"><span data-stu-id="cff6c-210">The following code removes a cache item and calls `Compact`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="cff6c-211">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-211">See [Compact source on GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="cff6c-212">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="cff6c-212">Cache dependencies</span></span>

<span data-ttu-id="cff6c-213">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-213">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="cff6c-214">将 `CancellationChangeToken` 添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-214">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="cff6c-215">如果对 `CancellationTokenSource` 调用 `Cancel`，则会逐出两个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-215">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="cff6c-216">使用 `CancellationTokenSource` 允许以组的形式逐出多个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-216">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="cff6c-217">在上面的代码中，在 `using` 模式下，在 `using` 块中创建的缓存条目将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="cff6c-217">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="cff6c-218">附加说明</span><span class="sxs-lookup"><span data-stu-id="cff6c-218">Additional notes</span></span>

* <span data-ttu-id="cff6c-219">不会在后台进行过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-219">Expiration doesn't happen in the background.</span></span> <span data-ttu-id="cff6c-220">没有计时器可主动扫描过期项目的缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-220">There is no timer that actively scans the cache for expired items.</span></span> <span data-ttu-id="cff6c-221">缓存中的任何活动（`Get`、`Set`、`Remove`）都可以触发过期项的后台扫描。</span><span class="sxs-lookup"><span data-stu-id="cff6c-221">Any activity on the cache (`Get`, `Set`, `Remove`) can trigger a background scan for expired items.</span></span> <span data-ttu-id="cff6c-222">@No__t_0 （`CancelAfter`）上的计时器还会删除该条目，并触发扫描过期的项目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-222">A timer on the `CancellationTokenSource` (`CancelAfter`) would also remove the entry and trigger a scan for expired items.</span></span> <span data-ttu-id="cff6c-223">例如，使用已注册令牌的 `CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))`，而不是使用 `SetAbsoluteExpiration(TimeSpan.FromHours(1))`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-223">For example, rather than using `SetAbsoluteExpiration(TimeSpan.FromHours(1))`, use `CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))` for the registered token.</span></span> <span data-ttu-id="cff6c-224">此令牌激发后，会立即删除该条目，并激发逐出回调。</span><span class="sxs-lookup"><span data-stu-id="cff6c-224">When this token fires it removes the entry immediately and fires the eviction callbacks.</span></span> <span data-ttu-id="cff6c-225">有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Caching/issues/248)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-225">For more information, see [this GitHub issue](https://github.com/aspnet/Caching/issues/248).</span></span>
* <span data-ttu-id="cff6c-226">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="cff6c-226">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="cff6c-227">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="cff6c-227">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="cff6c-228">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-228">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="cff6c-229">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="cff6c-229">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="cff6c-230">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-230">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="cff6c-231">使用 <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> 设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="cff6c-231">Use <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>
* <span data-ttu-id="cff6c-232">对于大多数应用，`IMemoryCache` 已启用。</span><span class="sxs-lookup"><span data-stu-id="cff6c-232">For most apps, `IMemoryCache` is enabled.</span></span> <span data-ttu-id="cff6c-233">例如，在 `Add{Service}` 中调用 `AddMvc`、`AddControllersWithViews`、`AddRazorPages`、`AddMvcCore().AddRazorViewEngine` 以及许多其他 `ConfigureServices` 方法会启用 `IMemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-233">For example, calling `AddMvc`, `AddControllersWithViews`, `AddRazorPages`, `AddMvcCore().AddRazorViewEngine`, and many other `Add{Service}` methods in `ConfigureServices`, enables `IMemoryCache`.</span></span> <span data-ttu-id="cff6c-234">对于未调用上述某个 `Add{Service}` 方法的应用，可能需要在 `ConfigureServices` 调用 <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*>。</span><span class="sxs-lookup"><span data-stu-id="cff6c-234">For apps that are not calling one of the preceding `Add{Service}` methods, it may be necessary to call <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> in `ConfigureServices`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cff6c-235">其他资源</span><span class="sxs-lookup"><span data-stu-id="cff6c-235">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
<span data-ttu-id="cff6c-236">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="cff6c-236">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="cff6c-237">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="cff6c-237">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="cff6c-238">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="cff6c-238">Caching basics</span></span>

<span data-ttu-id="cff6c-239">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="cff6c-239">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="cff6c-240">缓存最适用于不经常更改的数据。</span><span class="sxs-lookup"><span data-stu-id="cff6c-240">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="cff6c-241">通过缓存，可以比从原始数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="cff6c-241">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="cff6c-242">应编写并测试代码，使其**绝不会**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="cff6c-242">Code should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="cff6c-243">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-243">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="cff6c-244">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，这表示存储在 web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-244">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="cff6c-245">在服务器场中运行的应用（多台服务器）应确保会话在使用内存中缓存时处于粘滞。</span><span class="sxs-lookup"><span data-stu-id="cff6c-245">Apps that run on a server farm (multiple servers) should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="cff6c-246">粘滞会话确保以后来自客户端的请求都发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="cff6c-246">Sticky sessions ensure that later requests from a client all go to the same server.</span></span> <span data-ttu-id="cff6c-247">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)（ARR）将来自用户代理的所有请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="cff6c-247">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all requests from a user agent to the same server.</span></span>

<span data-ttu-id="cff6c-248">Web 场中的非粘滞会话需要[分布式缓存](distributed.md)，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="cff6c-248">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="cff6c-249">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="cff6c-249">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="cff6c-250">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="cff6c-250">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="cff6c-251">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="cff6c-251">The in-memory cache can store any object.</span></span> <span data-ttu-id="cff6c-252">分布式缓存接口仅限 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-252">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="cff6c-253">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="cff6c-253">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="cff6c-254">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="cff6c-254">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="cff6c-255"><xref:System.Runtime.Caching> / <xref:System.Runtime.Caching.MemoryCache> （[NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)）可用于：</span><span class="sxs-lookup"><span data-stu-id="cff6c-255"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="cff6c-256">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cff6c-256">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="cff6c-257">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-257">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="cff6c-258">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cff6c-258">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="cff6c-259">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="cff6c-259">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="cff6c-260">@No__t_4 `System.Runtime.Caching` 建议将 / `IMemoryCache` （本文所述）的（本文中介绍），因为它更好地集成[到 `MemoryCache` 中](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-260">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="cff6c-261">例如，`IMemoryCache` 与 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)一起使用。</span><span class="sxs-lookup"><span data-stu-id="cff6c-261">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="cff6c-262">将 ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，使用 `System.Runtime.Caching` / `MemoryCache` 作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="cff6c-262">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="cff6c-263">缓存指南</span><span class="sxs-lookup"><span data-stu-id="cff6c-263">Cache guidelines</span></span>

* <span data-ttu-id="cff6c-264">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="cff6c-264">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="cff6c-265">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-265">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="cff6c-266">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="cff6c-266">Limit cache growth:</span></span>
  * <span data-ttu-id="cff6c-267">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="cff6c-267">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="cff6c-268">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="cff6c-268">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="cff6c-269">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-269">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="cff6c-270">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-270">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="cff6c-271">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-271">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="cff6c-272">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="cff6c-272">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="cff6c-273">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存并调用 `SetSize`、`Size` 或 `SizeLimit` 来限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="cff6c-273">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="cff6c-274">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-274">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="cff6c-275">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="cff6c-275">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="cff6c-276">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-276">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="cff6c-277">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-277">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="cff6c-278">使用 `SetSize`、`Size` 或 `SizeLimit` 限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="cff6c-278">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="cff6c-279">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-279">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="cff6c-280">内存中缓存是从应用程序中使用[依赖关系注入](../../fundamentals/dependency-injection.md)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="cff6c-280">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="cff6c-281">@No__t_1 调用 `AddMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="cff6c-281">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="cff6c-282">请求构造函数中的 `IMemoryCache` 实例：</span><span class="sxs-lookup"><span data-stu-id="cff6c-282">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="cff6c-283">`IMemoryCache` 需要 NuGet 包 AspNetCore，此包可在[元包](xref:fundamentals/metapackage-app)中找到[的。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)</span><span class="sxs-lookup"><span data-stu-id="cff6c-283">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="cff6c-284">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-284">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="cff6c-285">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="cff6c-285">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="cff6c-286">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="cff6c-286">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="cff6c-287">如果在超时期限内存在请求，则缓存 `DateTime` 值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="cff6c-287">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="cff6c-288">下图显示了从缓存中检索到的当前时间和旧时间：</span><span class="sxs-lookup"><span data-stu-id="cff6c-288">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="cff6c-290">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="cff6c-290">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="cff6c-291">以下代码调用[Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)缓存时间：</span><span class="sxs-lookup"><span data-stu-id="cff6c-291">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="cff6c-292"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)是扩展方法的一部分，扩展方法是扩展 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 功能的[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)类的一部分。</span><span class="sxs-lookup"><span data-stu-id="cff6c-292"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="cff6c-293">有关其他缓存方法的说明，请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-293">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="cff6c-294">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="cff6c-294">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="cff6c-295">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="cff6c-295">The following sample:</span></span>

* <span data-ttu-id="cff6c-296">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-296">Sets a sliding expiration time.</span></span> <span data-ttu-id="cff6c-297">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="cff6c-297">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="cff6c-298">将缓存优先级设置为 `CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-298">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="cff6c-299">设置在从缓存中逐出项后将调用的[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="cff6c-299">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="cff6c-300">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="cff6c-300">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="cff6c-301">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="cff6c-301">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="cff6c-302">@No__t_0 实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="cff6c-302">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="cff6c-303">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="cff6c-303">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="cff6c-304">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="cff6c-304">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="cff6c-305">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-305">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="cff6c-306">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-306">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="cff6c-307">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="cff6c-307">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="cff6c-308">例如:</span><span class="sxs-lookup"><span data-stu-id="cff6c-308">For example:</span></span>

* <span data-ttu-id="cff6c-309">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="cff6c-309">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="cff6c-310">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="cff6c-310">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="cff6c-311">如果未设置 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>，则缓存将不受限制。</span><span class="sxs-lookup"><span data-stu-id="cff6c-311">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="cff6c-312">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-312">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="cff6c-313">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="cff6c-313">Apps much be architected to:</span></span>

* <span data-ttu-id="cff6c-314">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="cff6c-314">Limit cache growth.</span></span>
* <span data-ttu-id="cff6c-315">如果可用内存有限，请调用 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*>：</span><span class="sxs-lookup"><span data-stu-id="cff6c-315">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="cff6c-316">下面的代码创建一个无单位固定大小 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> 可通过[依赖关系注入](xref:fundamentals/dependency-injection)进行访问：</span><span class="sxs-lookup"><span data-stu-id="cff6c-316">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="cff6c-317">`SizeLimit` 没有单位。</span><span class="sxs-lookup"><span data-stu-id="cff6c-317">`SizeLimit` does not have units.</span></span> <span data-ttu-id="cff6c-318">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-318">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="cff6c-319">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="cff6c-319">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="cff6c-320">如果缓存条目大小的总和超出 `SizeLimit` 指定的值，则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-320">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="cff6c-321">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="cff6c-321">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="cff6c-322">下面的代码向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册 `MyMemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="cff6c-322">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="cff6c-323">对于识别此大小限制缓存并知道如何适当设置缓存条目大小的组件，`MyMemoryCache` 创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="cff6c-323">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="cff6c-324">以下代码使用 `MyMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="cff6c-324">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="cff6c-325">缓存项的大小可以通过[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="cff6c-325">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="cff6c-326">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="cff6c-326">MemoryCache.Compact</span></span>

<span data-ttu-id="cff6c-327">`MemoryCache.Compact` 尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="cff6c-327">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="cff6c-328">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-328">All expired items.</span></span>
* <span data-ttu-id="cff6c-329">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="cff6c-329">Items by priority.</span></span> <span data-ttu-id="cff6c-330">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-330">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="cff6c-331">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="cff6c-331">Least recently used objects.</span></span>
* <span data-ttu-id="cff6c-332">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-332">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="cff6c-333">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-333">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="cff6c-334">永远不会删除优先级为 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 的固定项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-334">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="cff6c-335">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="cff6c-335">See [Compact source on GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="cff6c-336">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="cff6c-336">Cache dependencies</span></span>

<span data-ttu-id="cff6c-337">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-337">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="cff6c-338">将 `CancellationChangeToken` 添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-338">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="cff6c-339">如果对 `CancellationTokenSource` 调用 `Cancel`，则会逐出两个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-339">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="cff6c-340">使用 `CancellationTokenSource` 允许以组的形式逐出多个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="cff6c-340">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="cff6c-341">在上面的代码中，在 `using` 模式下，在 `using` 块中创建的缓存条目将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="cff6c-341">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="cff6c-342">附加说明</span><span class="sxs-lookup"><span data-stu-id="cff6c-342">Additional notes</span></span>

* <span data-ttu-id="cff6c-343">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="cff6c-343">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="cff6c-344">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="cff6c-344">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="cff6c-345">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="cff6c-345">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="cff6c-346">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="cff6c-346">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="cff6c-347">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="cff6c-347">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="cff6c-348">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="cff6c-348">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="cff6c-349">其他资源</span><span class="sxs-lookup"><span data-stu-id="cff6c-349">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
