---
title: ASP.NET Core 中的缓存内存
author: rick-anderson
description: 了解如何在 ASP.NET Core 内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 8/22/2019
uid: performance/caching/memory
ms.openlocfilehash: d6b2aa363c552fdbda7f6e9ec5d476768c17d8a5
ms.sourcegitcommit: 810d5831169770ee240d03207d6671dabea2486e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/22/2019
ms.locfileid: "72779191"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="8afcb-103">ASP.NET Core 中的缓存内存</span><span class="sxs-lookup"><span data-stu-id="8afcb-103">Cache in-memory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8afcb-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8afcb-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8afcb-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8afcb-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="8afcb-106">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="8afcb-106">Caching basics</span></span>

<span data-ttu-id="8afcb-107">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="8afcb-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="8afcb-108">缓存最适用于不经常更改的**数据，生成**成本很高。</span><span class="sxs-lookup"><span data-stu-id="8afcb-108">Caching works best with data that changes infrequently **and** is expensive to generate.</span></span> <span data-ttu-id="8afcb-109">通过缓存，可以比从数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="8afcb-109">Caching makes a copy of data that can be returned much faster than from the source.</span></span> <span data-ttu-id="8afcb-110">应该对应用进行编写和测试，使其**永不**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="8afcb-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="8afcb-111">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="8afcb-112">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="8afcb-113">`IMemoryCache` 表示存储在 web 服务器的内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-113">`IMemoryCache` represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="8afcb-114">使用内存中缓存时，在服务器场（多台服务器）上运行的应用应确保会话是粘滞的。</span><span class="sxs-lookup"><span data-stu-id="8afcb-114">Apps running on a server farm (multiple servers) should ensure sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="8afcb-115">粘滞会话确保来自客户端的后续请求都将发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="8afcb-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="8afcb-116">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)（ARR）将所有后续请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="8afcb-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="8afcb-117">Web 场中的非粘滞会话需要[分布式缓存](distributed.md)，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="8afcb-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="8afcb-118">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="8afcb-118">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="8afcb-119">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="8afcb-119">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="8afcb-120">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="8afcb-120">The in-memory cache can store any object.</span></span> <span data-ttu-id="8afcb-121">分布式缓存接口仅限 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-121">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="8afcb-122">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="8afcb-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="8afcb-123">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="8afcb-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="8afcb-124"><xref:System.Runtime.Caching> / <xref:System.Runtime.Caching.MemoryCache> （[NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)）可用于：</span><span class="sxs-lookup"><span data-stu-id="8afcb-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="8afcb-125">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8afcb-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="8afcb-126">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="8afcb-127">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8afcb-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="8afcb-128">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8afcb-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="8afcb-129">@No__t_4 `System.Runtime.Caching` 建议将 / `IMemoryCache` （本文所述）的（本文中介绍），因为它更好地集成[到 `MemoryCache` 中](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="8afcb-130">例如，`IMemoryCache` 与 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)一起使用。</span><span class="sxs-lookup"><span data-stu-id="8afcb-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8afcb-131">将 ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，使用 `System.Runtime.Caching` / `MemoryCache` 作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="8afcb-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="8afcb-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="8afcb-132">Cache guidelines</span></span>

* <span data-ttu-id="8afcb-133">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="8afcb-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="8afcb-134">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="8afcb-135">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="8afcb-135">Limit cache growth:</span></span>
  * <span data-ttu-id="8afcb-136">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="8afcb-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="8afcb-137">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="8afcb-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="8afcb-138">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="8afcb-139">ASP.NET Core 运行时不会根据内存**压力限制缓存**大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-139">The ASP.NET Core runtime does **not** limit cache size based on memory pressure.</span></span> <span data-ttu-id="8afcb-140">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-140">It's up to the developer to limit cache size.</span></span>

## <a name="use-imemorycache"></a><span data-ttu-id="8afcb-141">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="8afcb-141">Use IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="8afcb-142">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存并调用 `SetSize`、`Size` 或 `SizeLimit` 来限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="8afcb-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="8afcb-143">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="8afcb-144">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="8afcb-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="8afcb-145">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="8afcb-146">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="8afcb-147">使用 `SetSize`、`Size` 或 `SizeLimit` 限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="8afcb-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="8afcb-148">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>
> <span data-ttu-id="8afcb-149">共享缓存由其他框架或库共享。</span><span class="sxs-lookup"><span data-stu-id="8afcb-149">A shared cache is one shared by other frameworks or libraries.</span></span> <span data-ttu-id="8afcb-150">例如，EF Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-150">For example, EF Core uses the shared cache and does not specify a size.</span></span> 

<span data-ttu-id="8afcb-151">内存中缓存是从应用程序中使用[依赖关系注入](xref:fundamentals/dependency-injection)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="8afcb-151">In-memory caching is a *service* that's referenced from an app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="8afcb-152">请求构造函数中的 `IMemoryCache` 实例：</span><span class="sxs-lookup"><span data-stu-id="8afcb-152">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="8afcb-153">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-153">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="8afcb-154">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="8afcb-154">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span> <span data-ttu-id="8afcb-155">@No__t_0 类是下载示例的一部分。</span><span class="sxs-lookup"><span data-stu-id="8afcb-155">The `CacheKeys` class is part of the download sample.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="8afcb-156">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="8afcb-156">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

<span data-ttu-id="8afcb-157">如果在超时期限内存在请求，则缓存 `DateTime` 值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="8afcb-157">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span>

<span data-ttu-id="8afcb-158">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="8afcb-158">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="8afcb-159">以下代码调用[Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)缓存时间：</span><span class="sxs-lookup"><span data-stu-id="8afcb-159">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="8afcb-160">下面的代码获取或创建具有绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="8afcb-160">The following code gets or creates a cached item with absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

<span data-ttu-id="8afcb-161">只有具有可调过期的缓存项集存在过时的风险。</span><span class="sxs-lookup"><span data-stu-id="8afcb-161">A cached item set with a sliding expiration only is at risk of becoming stale.</span></span> <span data-ttu-id="8afcb-162">如果访问的时间比滑动过期时间间隔更频繁，则该项将永不过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-162">If it's accessed more frequently than the sliding expiration interval, the item will never expire.</span></span> <span data-ttu-id="8afcb-163">将弹性过期与绝对过期组合在一起，以保证项目在其绝对过期时间通过后过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-163">Combine a sliding expiration with an absolute expiration to guarantee that the item expires once its absolute expiration time passes.</span></span> <span data-ttu-id="8afcb-164">绝对过期会将项的上限设置为可缓存项的时间，同时仍允许项在可调整过期时间间隔内未请求时提前过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-164">The absolute expiration sets an upper bound to how long the item can be cached while still allowing the item to expire earlier if it isn't requested within the sliding expiration interval.</span></span> <span data-ttu-id="8afcb-165">如果同时指定了绝对过期和可调过期时间，则过期时间以逻辑方式运算。</span><span class="sxs-lookup"><span data-stu-id="8afcb-165">When both absolute and sliding expiration are specified, the expirations are logically ORed.</span></span> <span data-ttu-id="8afcb-166">如果滑动过期时间间隔*或*绝对过期时间通过，则从缓存中逐出该项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-166">If either the sliding expiration interval *or* the absolute expiration time pass, the item is evicted from the cache.</span></span>

<span data-ttu-id="8afcb-167">下面的代码获取或创建具有可调*和*绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="8afcb-167">The following code gets or creates a cached item with both sliding *and* absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

<span data-ttu-id="8afcb-168">前面的代码保证数据的缓存时间不超过绝对时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-168">The preceding code guarantees the data will not be cached longer than the absolute time.</span></span>

<span data-ttu-id="8afcb-169"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 是 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> 类中的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="8afcb-169"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> are extension methods in the <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> class.</span></span> <span data-ttu-id="8afcb-170">这些方法扩展了 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 的功能。</span><span class="sxs-lookup"><span data-stu-id="8afcb-170">These methods extend the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="8afcb-171">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="8afcb-171">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="8afcb-172">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="8afcb-172">The following sample:</span></span>

* <span data-ttu-id="8afcb-173">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-173">Sets a sliding expiration time.</span></span> <span data-ttu-id="8afcb-174">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-174">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="8afcb-175">将缓存优先级设置为[CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-175">Sets the cache priority to [CacheItemPriority.NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).</span></span>
* <span data-ttu-id="8afcb-176">设置在从缓存中逐出项后将调用的[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="8afcb-176">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="8afcb-177">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="8afcb-177">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="8afcb-178">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="8afcb-178">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="8afcb-179">@No__t_0 实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="8afcb-179">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="8afcb-180">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="8afcb-180">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="8afcb-181">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="8afcb-181">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="8afcb-182">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-182">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="8afcb-183">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-183">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="8afcb-184">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="8afcb-184">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="8afcb-185">例如:</span><span class="sxs-lookup"><span data-stu-id="8afcb-185">For example:</span></span>

* <span data-ttu-id="8afcb-186">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="8afcb-186">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="8afcb-187">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="8afcb-187">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="8afcb-188">如果未设置 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>，则缓存将不受限制。</span><span class="sxs-lookup"><span data-stu-id="8afcb-188">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="8afcb-189">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-189">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="8afcb-190">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="8afcb-190">Apps much be architected to:</span></span>

* <span data-ttu-id="8afcb-191">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="8afcb-191">Limit cache growth.</span></span>
* <span data-ttu-id="8afcb-192">如果可用内存有限，请调用 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*>：</span><span class="sxs-lookup"><span data-stu-id="8afcb-192">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="8afcb-193">下面的代码创建一个无单位固定大小 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> 可通过[依赖关系注入](xref:fundamentals/dependency-injection)进行访问：</span><span class="sxs-lookup"><span data-stu-id="8afcb-193">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="8afcb-194">`SizeLimit` 没有单位。</span><span class="sxs-lookup"><span data-stu-id="8afcb-194">`SizeLimit` does not have units.</span></span> <span data-ttu-id="8afcb-195">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-195">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="8afcb-196">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="8afcb-196">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="8afcb-197">如果缓存条目大小的总和超出 `SizeLimit` 指定的值，则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-197">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="8afcb-198">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-198">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="8afcb-199">下面的代码向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册 `MyMemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-199">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

<span data-ttu-id="8afcb-200">对于识别此大小限制缓存并知道如何适当设置缓存条目大小的组件，`MyMemoryCache` 创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-200">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="8afcb-201">以下代码使用 `MyMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="8afcb-201">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

<span data-ttu-id="8afcb-202">缓存项的大小可以 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> 扩展方法进行设置：</span><span class="sxs-lookup"><span data-stu-id="8afcb-202">The size of the cache entry can be set by <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> or the <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> extension methods:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="8afcb-203">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="8afcb-203">MemoryCache.Compact</span></span>

<span data-ttu-id="8afcb-204">`MemoryCache.Compact` 尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="8afcb-204">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="8afcb-205">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-205">All expired items.</span></span>
* <span data-ttu-id="8afcb-206">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="8afcb-206">Items by priority.</span></span> <span data-ttu-id="8afcb-207">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-207">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="8afcb-208">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="8afcb-208">Least recently used objects.</span></span>
* <span data-ttu-id="8afcb-209">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-209">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="8afcb-210">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-210">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="8afcb-211">永远不会删除优先级为 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 的固定项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-211">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span> <span data-ttu-id="8afcb-212">以下代码将删除缓存项并调用 `Compact`：</span><span class="sxs-lookup"><span data-stu-id="8afcb-212">The following code removes a cache item and calls `Compact`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="8afcb-213">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-213">See [Compact source on GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="8afcb-214">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="8afcb-214">Cache dependencies</span></span>

<span data-ttu-id="8afcb-215">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-215">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="8afcb-216">将 `CancellationChangeToken` 添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-216">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="8afcb-217">如果对 `CancellationTokenSource` 调用 `Cancel`，则会逐出两个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-217">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="8afcb-218">使用 `CancellationTokenSource` 允许以组的形式逐出多个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-218">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="8afcb-219">在上面的代码中，在 `using` 模式下，在 `using` 块中创建的缓存条目将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="8afcb-219">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="8afcb-220">附加说明</span><span class="sxs-lookup"><span data-stu-id="8afcb-220">Additional notes</span></span>

* <span data-ttu-id="8afcb-221">不会在后台进行过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-221">Expiration doesn't happen in the background.</span></span> <span data-ttu-id="8afcb-222">没有计时器可主动扫描过期项目的缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-222">There is no timer that actively scans the cache for expired items.</span></span> <span data-ttu-id="8afcb-223">缓存中的任何活动（`Get`、`Set`、`Remove`）都可以触发过期项的后台扫描。</span><span class="sxs-lookup"><span data-stu-id="8afcb-223">Any activity on the cache (`Get`, `Set`, `Remove`) can trigger a background scan for expired items.</span></span> <span data-ttu-id="8afcb-224">@No__t_0 （`CancelAfter`）上的计时器还会删除该条目，并触发扫描过期的项目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-224">A timer on the `CancellationTokenSource` (`CancelAfter`) would also remove the entry and trigger a scan for expired items.</span></span> <span data-ttu-id="8afcb-225">例如，使用已注册令牌的 `CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))`，而不是使用 `SetAbsoluteExpiration(TimeSpan.FromHours(1))`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-225">For example, rather than using `SetAbsoluteExpiration(TimeSpan.FromHours(1))`, use `CancellationTokenSource.CancelAfter(TimeSpan.FromHours(1))` for the registered token.</span></span> <span data-ttu-id="8afcb-226">此令牌激发后，会立即删除该条目，并激发逐出回调。</span><span class="sxs-lookup"><span data-stu-id="8afcb-226">When this token fires it removes the entry immediately and fires the eviction callbacks.</span></span> <span data-ttu-id="8afcb-227">有关详细信息，请参阅[此 GitHub 问题](https://github.com/aspnet/Caching/issues/248)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-227">For more information, see [this GitHub issue](https://github.com/aspnet/Caching/issues/248).</span></span>
* <span data-ttu-id="8afcb-228">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="8afcb-228">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="8afcb-229">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="8afcb-229">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="8afcb-230">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-230">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="8afcb-231">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="8afcb-231">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="8afcb-232">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-232">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="8afcb-233">使用 <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> 设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="8afcb-233">Use <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>
* <span data-ttu-id="8afcb-234">对于大多数应用，`IMemoryCache` 已启用。</span><span class="sxs-lookup"><span data-stu-id="8afcb-234">For most apps, `IMemoryCache` is enabled.</span></span> <span data-ttu-id="8afcb-235">例如，在 `Add{Service}` 中调用 `AddMvc`、`AddControllersWithViews`、`AddRazorPages`、`AddMvcCore().AddRazorViewEngine` 以及许多其他 `ConfigureServices` 方法会启用 `IMemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-235">For example, calling `AddMvc`, `AddControllersWithViews`, `AddRazorPages`, `AddMvcCore().AddRazorViewEngine`, and many other `Add{Service}` methods in `ConfigureServices`, enables `IMemoryCache`.</span></span> <span data-ttu-id="8afcb-236">对于未调用上述某个 `Add{Service}` 方法的应用，可能需要在 `ConfigureServices` 调用 <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*>。</span><span class="sxs-lookup"><span data-stu-id="8afcb-236">For apps that are not calling one of the preceding `Add{Service}` methods, it may be necessary to call <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> in `ConfigureServices`.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8afcb-237">其他资源</span><span class="sxs-lookup"><span data-stu-id="8afcb-237">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
<span data-ttu-id="8afcb-238">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8afcb-238">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8afcb-239">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8afcb-239">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="8afcb-240">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="8afcb-240">Caching basics</span></span>

<span data-ttu-id="8afcb-241">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="8afcb-241">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="8afcb-242">缓存最适用于不经常更改的数据。</span><span class="sxs-lookup"><span data-stu-id="8afcb-242">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="8afcb-243">通过缓存，可以比从原始数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="8afcb-243">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="8afcb-244">应编写并测试代码，使其**绝不会**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="8afcb-244">Code should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="8afcb-245">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-245">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="8afcb-246">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，这表示存储在 web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-246">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="8afcb-247">在服务器场中运行的应用（多台服务器）应确保会话在使用内存中缓存时处于粘滞。</span><span class="sxs-lookup"><span data-stu-id="8afcb-247">Apps that run on a server farm (multiple servers) should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="8afcb-248">粘滞会话确保以后来自客户端的请求都发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="8afcb-248">Sticky sessions ensure that later requests from a client all go to the same server.</span></span> <span data-ttu-id="8afcb-249">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)（ARR）将来自用户代理的所有请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="8afcb-249">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all requests from a user agent to the same server.</span></span>

<span data-ttu-id="8afcb-250">Web 场中的非粘滞会话需要[分布式缓存](distributed.md)，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="8afcb-250">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="8afcb-251">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="8afcb-251">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="8afcb-252">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="8afcb-252">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="8afcb-253">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="8afcb-253">The in-memory cache can store any object.</span></span> <span data-ttu-id="8afcb-254">分布式缓存接口仅限 `byte[]`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-254">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="8afcb-255">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="8afcb-255">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="8afcb-256">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="8afcb-256">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="8afcb-257"><xref:System.Runtime.Caching> / <xref:System.Runtime.Caching.MemoryCache> （[NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)）可用于：</span><span class="sxs-lookup"><span data-stu-id="8afcb-257"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="8afcb-258">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8afcb-258">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="8afcb-259">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-259">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="8afcb-260">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8afcb-260">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="8afcb-261">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="8afcb-261">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="8afcb-262">@No__t_4 `System.Runtime.Caching` 建议将 / `IMemoryCache` （本文所述）的（本文中介绍），因为它更好地集成[到 `MemoryCache` 中](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-262">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="8afcb-263">例如，`IMemoryCache` 与 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)一起使用。</span><span class="sxs-lookup"><span data-stu-id="8afcb-263">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="8afcb-264">将 ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，使用 `System.Runtime.Caching` / `MemoryCache` 作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="8afcb-264">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="8afcb-265">缓存指南</span><span class="sxs-lookup"><span data-stu-id="8afcb-265">Cache guidelines</span></span>

* <span data-ttu-id="8afcb-266">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="8afcb-266">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="8afcb-267">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-267">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="8afcb-268">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="8afcb-268">Limit cache growth:</span></span>
  * <span data-ttu-id="8afcb-269">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="8afcb-269">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="8afcb-270">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="8afcb-270">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="8afcb-271">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-271">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="8afcb-272">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-272">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="8afcb-273">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-273">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="8afcb-274">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="8afcb-274">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="8afcb-275">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存并调用 `SetSize`、`Size` 或 `SizeLimit` 来限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="8afcb-275">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="8afcb-276">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-276">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="8afcb-277">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="8afcb-277">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="8afcb-278">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-278">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="8afcb-279">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-279">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="8afcb-280">使用 `SetSize`、`Size` 或 `SizeLimit` 限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="8afcb-280">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="8afcb-281">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-281">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="8afcb-282">内存中缓存是从应用程序中使用[依赖关系注入](../../fundamentals/dependency-injection.md)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="8afcb-282">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="8afcb-283">@No__t_1 调用 `AddMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="8afcb-283">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="8afcb-284">请求构造函数中的 `IMemoryCache` 实例：</span><span class="sxs-lookup"><span data-stu-id="8afcb-284">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="8afcb-285">`IMemoryCache` 需要 NuGet 包 AspNetCore，此包可在[元包](xref:fundamentals/metapackage-app)中找到[的。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)</span><span class="sxs-lookup"><span data-stu-id="8afcb-285">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="8afcb-286">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-286">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="8afcb-287">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="8afcb-287">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="8afcb-288">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="8afcb-288">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="8afcb-289">如果在超时期限内存在请求，则缓存 `DateTime` 值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="8afcb-289">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="8afcb-290">下图显示了从缓存中检索到的当前时间和旧时间：</span><span class="sxs-lookup"><span data-stu-id="8afcb-290">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="8afcb-292">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="8afcb-292">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="8afcb-293">以下代码调用[Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)缓存时间：</span><span class="sxs-lookup"><span data-stu-id="8afcb-293">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="8afcb-294"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)是扩展方法的一部分，扩展方法是扩展 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 功能的[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)类的一部分。</span><span class="sxs-lookup"><span data-stu-id="8afcb-294"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="8afcb-295">有关其他缓存方法的说明，请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-295">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="8afcb-296">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="8afcb-296">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="8afcb-297">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="8afcb-297">The following sample:</span></span>

* <span data-ttu-id="8afcb-298">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-298">Sets a sliding expiration time.</span></span> <span data-ttu-id="8afcb-299">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="8afcb-299">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="8afcb-300">将缓存优先级设置为 `CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-300">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="8afcb-301">设置在从缓存中逐出项后将调用的[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="8afcb-301">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="8afcb-302">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="8afcb-302">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="8afcb-303">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="8afcb-303">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="8afcb-304">@No__t_0 实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="8afcb-304">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="8afcb-305">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="8afcb-305">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="8afcb-306">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="8afcb-306">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="8afcb-307">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-307">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="8afcb-308">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-308">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="8afcb-309">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="8afcb-309">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="8afcb-310">例如:</span><span class="sxs-lookup"><span data-stu-id="8afcb-310">For example:</span></span>

* <span data-ttu-id="8afcb-311">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="8afcb-311">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="8afcb-312">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="8afcb-312">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="8afcb-313">如果未设置 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit>，则缓存将不受限制。</span><span class="sxs-lookup"><span data-stu-id="8afcb-313">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="8afcb-314">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-314">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="8afcb-315">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="8afcb-315">Apps much be architected to:</span></span>

* <span data-ttu-id="8afcb-316">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="8afcb-316">Limit cache growth.</span></span>
* <span data-ttu-id="8afcb-317">如果可用内存有限，请调用 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*>：</span><span class="sxs-lookup"><span data-stu-id="8afcb-317">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="8afcb-318">下面的代码创建一个无单位固定大小 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> 可通过[依赖关系注入](xref:fundamentals/dependency-injection)进行访问：</span><span class="sxs-lookup"><span data-stu-id="8afcb-318">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="8afcb-319">`SizeLimit` 没有单位。</span><span class="sxs-lookup"><span data-stu-id="8afcb-319">`SizeLimit` does not have units.</span></span> <span data-ttu-id="8afcb-320">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-320">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="8afcb-321">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="8afcb-321">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="8afcb-322">如果缓存条目大小的总和超出 `SizeLimit` 指定的值，则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-322">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="8afcb-323">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="8afcb-323">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="8afcb-324">下面的代码向[依赖关系注入](xref:fundamentals/dependency-injection)容器注册 `MyMemoryCache`。</span><span class="sxs-lookup"><span data-stu-id="8afcb-324">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="8afcb-325">对于识别此大小限制缓存并知道如何适当设置缓存条目大小的组件，`MyMemoryCache` 创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="8afcb-325">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="8afcb-326">以下代码使用 `MyMemoryCache`：</span><span class="sxs-lookup"><span data-stu-id="8afcb-326">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="8afcb-327">缓存项的大小可以通过[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="8afcb-327">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="8afcb-328">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="8afcb-328">MemoryCache.Compact</span></span>

<span data-ttu-id="8afcb-329">`MemoryCache.Compact` 尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="8afcb-329">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="8afcb-330">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-330">All expired items.</span></span>
* <span data-ttu-id="8afcb-331">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="8afcb-331">Items by priority.</span></span> <span data-ttu-id="8afcb-332">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-332">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="8afcb-333">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="8afcb-333">Least recently used objects.</span></span>
* <span data-ttu-id="8afcb-334">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-334">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="8afcb-335">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-335">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="8afcb-336">永远不会删除优先级为 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 的固定项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-336">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="8afcb-337">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="8afcb-337">See [Compact source on GitHub](https://github.com/aspnet/Extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="8afcb-338">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="8afcb-338">Cache dependencies</span></span>

<span data-ttu-id="8afcb-339">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-339">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="8afcb-340">将 `CancellationChangeToken` 添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-340">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="8afcb-341">如果对 `CancellationTokenSource` 调用 `Cancel`，则会逐出两个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-341">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="8afcb-342">使用 `CancellationTokenSource` 允许以组的形式逐出多个缓存条目。</span><span class="sxs-lookup"><span data-stu-id="8afcb-342">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="8afcb-343">在上面的代码中，在 `using` 模式下，在 `using` 块中创建的缓存条目将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="8afcb-343">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="8afcb-344">附加说明</span><span class="sxs-lookup"><span data-stu-id="8afcb-344">Additional notes</span></span>

* <span data-ttu-id="8afcb-345">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="8afcb-345">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="8afcb-346">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="8afcb-346">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="8afcb-347">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="8afcb-347">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="8afcb-348">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="8afcb-348">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="8afcb-349">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="8afcb-349">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="8afcb-350">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="8afcb-350">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="8afcb-351">其他资源</span><span class="sxs-lookup"><span data-stu-id="8afcb-351">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
