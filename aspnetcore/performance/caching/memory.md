---
title: ASP.NET Core 中的缓存内存
author: rick-anderson
description: 了解如何在 ASP.NET Core 内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
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
uid: performance/caching/memory
ms.openlocfilehash: 131fd5f2d09b20814cbd557d6b6d873ce15501db
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021219"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="3937e-103">ASP.NET Core 中的缓存内存</span><span class="sxs-lookup"><span data-stu-id="3937e-103">Cache in-memory in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="3937e-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3937e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3937e-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3937e-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/3.0sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="3937e-106">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="3937e-106">Caching basics</span></span>

<span data-ttu-id="3937e-107">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="3937e-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="3937e-108">缓存最适用于不经常更改的**数据，生成**成本很高。</span><span class="sxs-lookup"><span data-stu-id="3937e-108">Caching works best with data that changes infrequently **and** is expensive to generate.</span></span> <span data-ttu-id="3937e-109">通过缓存，可以比从数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="3937e-109">Caching makes a copy of data that can be returned much faster than from the source.</span></span> <span data-ttu-id="3937e-110">应该对应用进行编写和测试，使其**永不**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="3937e-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="3937e-111">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="3937e-112">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)。</span><span class="sxs-lookup"><span data-stu-id="3937e-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache).</span></span> <span data-ttu-id="3937e-113">`IMemoryCache`表示存储在 web 服务器的内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-113">`IMemoryCache` represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="3937e-114">在服务器场上运行的应用 (多台服务器) 应确保会话在使用内存中缓存时处于粘滞状态。</span><span class="sxs-lookup"><span data-stu-id="3937e-114">Apps running on a server farm (multiple servers) should ensure sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="3937e-115">粘滞会话确保来自客户端的后续请求都将发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="3937e-115">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="3937e-116">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 将所有后续请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="3937e-116">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="3937e-117">Web 场中的非粘滞会话需要[分布式缓存](distributed.md)，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="3937e-117">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="3937e-118">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="3937e-118">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="3937e-119">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="3937e-119">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="3937e-120">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="3937e-120">The in-memory cache can store any object.</span></span> <span data-ttu-id="3937e-121">分布式缓存接口仅限 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-121">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="3937e-122">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="3937e-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="3937e-123">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="3937e-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="3937e-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于：</span><span class="sxs-lookup"><span data-stu-id="3937e-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="3937e-125">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3937e-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="3937e-126">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="3937e-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="3937e-127">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3937e-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="3937e-128">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3937e-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="3937e-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 建议使用 (本文中所述) ， `System.Runtime.Caching` / `MemoryCache` 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="3937e-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="3937e-130">例如， `IMemoryCache` 使用 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="3937e-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="3937e-131">将 `System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，请使用作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="3937e-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="3937e-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="3937e-132">Cache guidelines</span></span>

* <span data-ttu-id="3937e-133">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="3937e-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="3937e-134">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="3937e-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="3937e-135">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="3937e-135">Limit cache growth:</span></span>
  * <span data-ttu-id="3937e-136">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="3937e-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="3937e-137">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="3937e-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="3937e-138">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="3937e-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="3937e-139">ASP.NET Core 运行时不会根据内存**压力限制缓存**大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-139">The ASP.NET Core runtime does **not** limit cache size based on memory pressure.</span></span> <span data-ttu-id="3937e-140">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-140">It's up to the developer to limit cache size.</span></span>

## <a name="use-imemorycache"></a><span data-ttu-id="3937e-141">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="3937e-141">Use IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="3937e-142">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存，并调用 `SetSize` 、 `Size` 或 `SizeLimit` 以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="3937e-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="3937e-143">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="3937e-144">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="3937e-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="3937e-145">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="3937e-146">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="3937e-147">当使用 `SetSize` 、 `Size` 或 `SizeLimit` 来限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="3937e-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="3937e-148">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="3937e-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>
> <span data-ttu-id="3937e-149">共享缓存由其他框架或库共享。</span><span class="sxs-lookup"><span data-stu-id="3937e-149">A shared cache is one shared by other frameworks or libraries.</span></span> <span data-ttu-id="3937e-150">例如，EF Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-150">For example, EF Core uses the shared cache and does not specify a size.</span></span> 

<span data-ttu-id="3937e-151">内存中缓存是从应用程序中使用[依赖关系注入](xref:fundamentals/dependency-injection)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="3937e-151">In-memory caching is a *service* that's referenced from an app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="3937e-152">`IMemoryCache`在构造函数中请求实例：</span><span class="sxs-lookup"><span data-stu-id="3937e-152">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="3937e-153">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-153">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="3937e-154">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="3937e-154">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span> <span data-ttu-id="3937e-155">`CacheKeys`类是下载示例的一部分。</span><span class="sxs-lookup"><span data-stu-id="3937e-155">The `CacheKeys` class is part of the download sample.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/CacheKeys.cs)]

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="3937e-156">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="3937e-156">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/3.0sample/WebCacheSample/Views/Home/Cache.cshtml)]

<span data-ttu-id="3937e-157">如果在 `DateTime` 超时期限内存在请求，则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="3937e-157">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span>

<span data-ttu-id="3937e-158">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="3937e-158">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="3937e-159">以下代码调用[Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)缓存时间：</span><span class="sxs-lookup"><span data-stu-id="3937e-159">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="3937e-160">下面的代码获取或创建具有绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="3937e-160">The following code gets or creates a cached item with absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet99)]

<span data-ttu-id="3937e-161">只有具有可调过期的缓存项集存在过时的风险。</span><span class="sxs-lookup"><span data-stu-id="3937e-161">A cached item set with a sliding expiration only is at risk of becoming stale.</span></span> <span data-ttu-id="3937e-162">如果访问的时间比滑动过期时间间隔更频繁，则该项将永不过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-162">If it's accessed more frequently than the sliding expiration interval, the item will never expire.</span></span> <span data-ttu-id="3937e-163">将弹性过期与绝对过期组合在一起，以保证项目在其绝对过期时间通过后过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-163">Combine a sliding expiration with an absolute expiration to guarantee that the item expires once its absolute expiration time passes.</span></span> <span data-ttu-id="3937e-164">绝对过期会将项的上限设置为可缓存项的时间，同时仍允许项在可调整过期时间间隔内未请求时提前过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-164">The absolute expiration sets an upper bound to how long the item can be cached while still allowing the item to expire earlier if it isn't requested within the sliding expiration interval.</span></span> <span data-ttu-id="3937e-165">如果同时指定了绝对过期和可调过期时间，则过期时间以逻辑方式运算。</span><span class="sxs-lookup"><span data-stu-id="3937e-165">When both absolute and sliding expiration are specified, the expirations are logically ORed.</span></span> <span data-ttu-id="3937e-166">如果滑动过期时间间隔*或*绝对过期时间通过，则从缓存中逐出该项。</span><span class="sxs-lookup"><span data-stu-id="3937e-166">If either the sliding expiration interval *or* the absolute expiration time pass, the item is evicted from the cache.</span></span>

<span data-ttu-id="3937e-167">下面的代码获取或创建具有可调*和*绝对过期的缓存项：</span><span class="sxs-lookup"><span data-stu-id="3937e-167">The following code gets or creates a cached item with both sliding *and* absolute expiration:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet9)]

<span data-ttu-id="3937e-168">前面的代码保证数据的缓存时间不超过绝对时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-168">The preceding code guarantees the data will not be cached longer than the absolute time.</span></span>

<span data-ttu-id="3937e-169"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> 是类中的扩展方法 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> 。</span><span class="sxs-lookup"><span data-stu-id="3937e-169"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>, <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.Get*> are extension methods in the <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions> class.</span></span> <span data-ttu-id="3937e-170">这些方法扩展了的功能 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 。</span><span class="sxs-lookup"><span data-stu-id="3937e-170">These methods extend the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="3937e-171">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="3937e-171">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="3937e-172">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="3937e-172">The following sample:</span></span>

* <span data-ttu-id="3937e-173">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-173">Sets a sliding expiration time.</span></span> <span data-ttu-id="3937e-174">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-174">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="3937e-175">将缓存优先级设置为[CacheItemPriority. NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove)。</span><span class="sxs-lookup"><span data-stu-id="3937e-175">Sets the cache priority to [CacheItemPriority.NeverRemove](xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove).</span></span>
* <span data-ttu-id="3937e-176">设置在从缓存中逐出项后将调用的[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="3937e-176">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="3937e-177">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="3937e-177">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="3937e-178">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="3937e-178">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="3937e-179">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="3937e-179">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="3937e-180">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="3937e-180">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="3937e-181">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="3937e-181">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="3937e-182">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-182">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="3937e-183">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-183">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="3937e-184">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="3937e-184">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="3937e-185">例如：</span><span class="sxs-lookup"><span data-stu-id="3937e-185">For example:</span></span>

* <span data-ttu-id="3937e-186">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="3937e-186">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="3937e-187">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="3937e-187">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="3937e-188">如果 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> 未设置，则缓存的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="3937e-188">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> isn't set, the cache grows without bound.</span></span> <span data-ttu-id="3937e-189">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-189">The ASP.NET Core runtime doesn't trim the cache when system memory is low.</span></span> <span data-ttu-id="3937e-190">应用必须构建为：</span><span class="sxs-lookup"><span data-stu-id="3937e-190">Apps must be architected to:</span></span>

* <span data-ttu-id="3937e-191">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="3937e-191">Limit cache growth.</span></span>
* <span data-ttu-id="3937e-192"><xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 当可用内存有限时调用或：</span><span class="sxs-lookup"><span data-stu-id="3937e-192">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="3937e-193">以下代码创建了 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [依赖关系注入](xref:fundamentals/dependency-injection)可访问的无大小固定大小：</span><span class="sxs-lookup"><span data-stu-id="3937e-193">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="3937e-194">`SizeLimit`没有单位。</span><span class="sxs-lookup"><span data-stu-id="3937e-194">`SizeLimit` does not have units.</span></span> <span data-ttu-id="3937e-195">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-195">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="3937e-196">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="3937e-196">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="3937e-197">如果缓存条目大小之和超过指定的值，则不会缓存条目 `SizeLimit` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-197">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="3937e-198">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-198">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="3937e-199">以下代码将注册 `MyMemoryCache` 到[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="3937e-199">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Startup.cs?name=snippet)]

<span data-ttu-id="3937e-200">`MyMemoryCache`对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件，将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-200">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="3937e-201">以下代码使用 `MyMemoryCache` ：</span><span class="sxs-lookup"><span data-stu-id="3937e-201">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet)]

<span data-ttu-id="3937e-202">缓存项的大小可以通过 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> 或 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> 扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="3937e-202">The size of the cache entry can be set by <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryOptions.Size> or the <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheEntryExtensions.SetSize*> extension methods:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/SetSize.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="3937e-203">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="3937e-203">MemoryCache.Compact</span></span>

<span data-ttu-id="3937e-204">`MemoryCache.Compact`尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="3937e-204">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="3937e-205">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="3937e-205">All expired items.</span></span>
* <span data-ttu-id="3937e-206">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="3937e-206">Items by priority.</span></span> <span data-ttu-id="3937e-207">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="3937e-207">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="3937e-208">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="3937e-208">Least recently used objects.</span></span>
* <span data-ttu-id="3937e-209">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-209">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="3937e-210">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-210">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="3937e-211">永远不会删除具有优先级的固定项 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 。</span><span class="sxs-lookup"><span data-stu-id="3937e-211">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span> <span data-ttu-id="3937e-212">以下代码将删除缓存项并调用 `Compact` ：</span><span class="sxs-lookup"><span data-stu-id="3937e-212">The following code removes a cache item and calls `Compact`:</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="3937e-213">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="3937e-213">See [Compact source on GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="3937e-214">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="3937e-214">Cache dependencies</span></span>

<span data-ttu-id="3937e-215">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-215">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="3937e-216"><xref:Microsoft.Extensions.Primitives.CancellationChangeToken>添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-216">A <xref:Microsoft.Extensions.Primitives.CancellationChangeToken> is added to the cached item.</span></span> <span data-ttu-id="3937e-217">当 `Cancel` 在上调用时 `CancellationTokenSource` ，将逐出两个缓存项。</span><span class="sxs-lookup"><span data-stu-id="3937e-217">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="3937e-218">使用 <xref:System.Threading.CancellationTokenSource> 允许将多个缓存条目作为一个组逐出。</span><span class="sxs-lookup"><span data-stu-id="3937e-218">Using a <xref:System.Threading.CancellationTokenSource> allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="3937e-219">`using`在上面的代码中，在块中创建的缓存条目 `using` 将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="3937e-219">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="3937e-220">附加说明</span><span class="sxs-lookup"><span data-stu-id="3937e-220">Additional notes</span></span>

* <span data-ttu-id="3937e-221">不会在后台进行过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-221">Expiration doesn't happen in the background.</span></span> <span data-ttu-id="3937e-222">没有计时器可主动扫描过期项目的缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-222">There is no timer that actively scans the cache for expired items.</span></span> <span data-ttu-id="3937e-223">缓存上的任何活动 (`Get` ， `Set` `Remove`) 可以触发过期项的后台扫描。</span><span class="sxs-lookup"><span data-stu-id="3937e-223">Any activity on the cache (`Get`, `Set`, `Remove`) can trigger a background scan for expired items.</span></span> <span data-ttu-id="3937e-224"> (上的计时器 `CancellationTokenSource` <xref:System.Threading.CancellationTokenSource.CancelAfter*>) 还会删除项并触发扫描过期项。</span><span class="sxs-lookup"><span data-stu-id="3937e-224">A timer on the `CancellationTokenSource` (<xref:System.Threading.CancellationTokenSource.CancelAfter*>) also removes the entry and trigger a scan for expired items.</span></span> <span data-ttu-id="3937e-225">下面的示例将[CancellationTokenSource (TimeSpan) ](/dotnet/api/system.threading.cancellationtokensource.-ctor)用于注册的令牌。</span><span class="sxs-lookup"><span data-stu-id="3937e-225">The following example uses [CancellationTokenSource(TimeSpan)](/dotnet/api/system.threading.cancellationtokensource.-ctor) for the registered token.</span></span> <span data-ttu-id="3937e-226">此令牌激发后，会立即删除该条目，并激发逐出回调：</span><span class="sxs-lookup"><span data-stu-id="3937e-226">When this token fires it removes the entry immediately and fires the eviction callbacks:</span></span>

[!code-csharp[](memory/3.0sample/WebCacheSample/Controllers/HomeController.cs?name=snippet_ae)]

* <span data-ttu-id="3937e-227">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="3937e-227">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="3937e-228">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="3937e-228">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="3937e-229">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-229">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="3937e-230">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="3937e-230">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="3937e-231">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-231">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="3937e-232">用于 <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> 设置在从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="3937e-232">Use <xref:Microsoft.Extensions.Caching.Memory.ICacheEntry.PostEvictionCallbacks> to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>
* <span data-ttu-id="3937e-233">对于大多数应用， `IMemoryCache` 启用。</span><span class="sxs-lookup"><span data-stu-id="3937e-233">For most apps, `IMemoryCache` is enabled.</span></span> <span data-ttu-id="3937e-234">例如， `AddMvc` 在中调用、、、 `AddControllersWithViews` `AddRazorPages` `AddMvcCore().AddRazorViewEngine` 和许多其他 `Add{Service}` 方法将 `ConfigureServices` 启用 `IMemoryCache` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-234">For example, calling `AddMvc`, `AddControllersWithViews`, `AddRazorPages`, `AddMvcCore().AddRazorViewEngine`, and many other `Add{Service}` methods in `ConfigureServices`, enables `IMemoryCache`.</span></span> <span data-ttu-id="3937e-235">对于未调用上述方法之一的应用程序 `Add{Service}` ，可能需要 <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> 在中调用 `ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-235">For apps that are not calling one of the preceding `Add{Service}` methods, it may be necessary to call <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddMemoryCache*> in `ConfigureServices`.</span></span>

## <a name="background-cache-update"></a><span data-ttu-id="3937e-236">后台缓存更新</span><span class="sxs-lookup"><span data-stu-id="3937e-236">Background cache update</span></span>

<span data-ttu-id="3937e-237">使用[后台服务](xref:fundamentals/host/hosted-services)（如） <xref:Microsoft.Extensions.Hosting.IHostedService> 来更新缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-237">Use a [background service](xref:fundamentals/host/hosted-services) such as <xref:Microsoft.Extensions.Hosting.IHostedService> to update the cache.</span></span> <span data-ttu-id="3937e-238">后台服务可重新计算条目，然后仅在准备就绪时将其分配给缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-238">The background service can recompute the entries and then assign them to the cache only when they’re ready.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3937e-239">其他资源</span><span class="sxs-lookup"><span data-stu-id="3937e-239">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<!-- This is the 2.1 version -->
<span data-ttu-id="3937e-240">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [John Luo](https://github.com/JunTaoLuo)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="3937e-240">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="3937e-241">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="3937e-241">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="3937e-242">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="3937e-242">Caching basics</span></span>

<span data-ttu-id="3937e-243">缓存可以减少生成内容所需的工作，从而显著提高应用程序的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="3937e-243">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="3937e-244">缓存最适用于不经常更改的数据。</span><span class="sxs-lookup"><span data-stu-id="3937e-244">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="3937e-245">通过缓存，可以比从原始数据源返回的数据的副本速度快得多。</span><span class="sxs-lookup"><span data-stu-id="3937e-245">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="3937e-246">应编写并测试代码，使其**绝不会**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="3937e-246">Code should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="3937e-247">ASP.NET Core 支持多个不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-247">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="3937e-248">最简单的缓存基于[IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，这表示存储在 web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-248">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="3937e-249">在服务器场上运行的应用 (多台服务器) 应确保会话在使用内存中缓存时处于粘滞。</span><span class="sxs-lookup"><span data-stu-id="3937e-249">Apps that run on a server farm (multiple servers) should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="3937e-250">粘滞会话确保以后来自客户端的请求都发送到相同的服务器。</span><span class="sxs-lookup"><span data-stu-id="3937e-250">Sticky sessions ensure that later requests from a client all go to the same server.</span></span> <span data-ttu-id="3937e-251">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) 将来自用户代理的所有请求路由到同一服务器。</span><span class="sxs-lookup"><span data-stu-id="3937e-251">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all requests from a user agent to the same server.</span></span>

<span data-ttu-id="3937e-252">Web 场中的非粘滞会话需要[分布式缓存](distributed.md)，以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="3937e-252">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="3937e-253">对于某些应用，分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="3937e-253">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="3937e-254">使用分布式缓存会将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="3937e-254">Using a distributed cache offloads the cache memory to an external process.</span></span>

<span data-ttu-id="3937e-255">内存中缓存可以存储任何对象。</span><span class="sxs-lookup"><span data-stu-id="3937e-255">The in-memory cache can store any object.</span></span> <span data-ttu-id="3937e-256">分布式缓存接口仅限 `byte[]` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-256">The distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="3937e-257">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="3937e-257">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="3937e-258">MemoryCache/缓存</span><span class="sxs-lookup"><span data-stu-id="3937e-258">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="3937e-259"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于：</span><span class="sxs-lookup"><span data-stu-id="3937e-259"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="3937e-260">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3937e-260">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="3937e-261">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="3937e-261">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="3937e-262">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3937e-262">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="3937e-263">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="3937e-263">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="3937e-264">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` 建议使用 (本文中所述) ， `System.Runtime.Caching` / `MemoryCache` 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="3937e-264">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="3937e-265">例如， `IMemoryCache` 使用 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="3937e-265">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="3937e-266">将 `System.Runtime.Caching` / `MemoryCache` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时，请使用作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="3937e-266">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="3937e-267">缓存指南</span><span class="sxs-lookup"><span data-stu-id="3937e-267">Cache guidelines</span></span>

* <span data-ttu-id="3937e-268">代码应始终具有回退选项，以获取数据，而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="3937e-268">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="3937e-269">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="3937e-269">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="3937e-270">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="3937e-270">Limit cache growth:</span></span>
  * <span data-ttu-id="3937e-271">不要**使用外部**输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="3937e-271">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="3937e-272">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="3937e-272">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="3937e-273">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="3937e-273">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="3937e-274">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-274">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="3937e-275">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-275">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="3937e-276">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="3937e-276">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="3937e-277">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存，并调用 `SetSize` 、 `Size` 或 `SizeLimit` 以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="3937e-277">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="3937e-278">在缓存上设置大小限制时，在添加时，所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-278">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="3937e-279">这可能会导致问题，因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="3937e-279">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="3937e-280">例如，Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-280">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="3937e-281">如果应用设置了缓存大小限制并使用 EF Core，则应用将引发 `InvalidOperationException` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-281">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="3937e-282">当使用 `SetSize` 、 `Size` 或 `SizeLimit` 来限制缓存时，为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="3937e-282">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="3937e-283">有关详细信息和示例，请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="3937e-283">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="3937e-284">内存中缓存是从应用程序中使用[依赖关系注入](../../fundamentals/dependency-injection.md)引用的一种*服务*。</span><span class="sxs-lookup"><span data-stu-id="3937e-284">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="3937e-285">调用 `AddMemoryCache` 于 `ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="3937e-285">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="3937e-286">`IMemoryCache`在构造函数中请求实例：</span><span class="sxs-lookup"><span data-stu-id="3937e-286">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

<span data-ttu-id="3937e-287">`IMemoryCache`需要 NuGet 包 AspNetCore，此包可在[元包](xref:fundamentals/metapackage-app)中找到[的。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)</span><span class="sxs-lookup"><span data-stu-id="3937e-287">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="3937e-288">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-288">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="3937e-289">如果未缓存时间，则将创建一个新条目，并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="3937e-289">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp[](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="3937e-290">显示当前时间和缓存时间：</span><span class="sxs-lookup"><span data-stu-id="3937e-290">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="3937e-291">如果在 `DateTime` 超时期限内存在请求，则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="3937e-291">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="3937e-292">下图显示了从缓存中检索到的当前时间和旧时间：</span><span class="sxs-lookup"><span data-stu-id="3937e-292">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="3937e-294">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="3937e-294">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="3937e-295">以下代码调用[Get 获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)缓存时间：</span><span class="sxs-lookup"><span data-stu-id="3937e-295">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="3937e-296"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*> 和[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)是[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)类的扩展方法的一部分，它扩展了的功能 <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache> 。</span><span class="sxs-lookup"><span data-stu-id="3937e-296"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="3937e-297">有关其他缓存方法的说明，请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)。</span><span class="sxs-lookup"><span data-stu-id="3937e-297">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="3937e-298">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="3937e-298">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="3937e-299">下面的示例：</span><span class="sxs-lookup"><span data-stu-id="3937e-299">The following sample:</span></span>

* <span data-ttu-id="3937e-300">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-300">Sets a sliding expiration time.</span></span> <span data-ttu-id="3937e-301">访问此缓存项的请求将重置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="3937e-301">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="3937e-302">将缓存优先级设置为 `CacheItemPriority.NeverRemove` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-302">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="3937e-303">设置在从缓存中逐出项后将调用的[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) 。</span><span class="sxs-lookup"><span data-stu-id="3937e-303">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="3937e-304">回调在从缓存中删除项的代码的不同线程上运行。</span><span class="sxs-lookup"><span data-stu-id="3937e-304">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="3937e-305">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="3937e-305">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="3937e-306">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="3937e-306">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="3937e-307">缓存大小限制没有定义的度量单位，因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="3937e-307">The cache size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="3937e-308">如果设置了缓存大小限制，则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="3937e-308">If the cache size limit is set, all entries must specify size.</span></span> <span data-ttu-id="3937e-309">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-309">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="3937e-310">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-310">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="3937e-311">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="3937e-311">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="3937e-312">例如：</span><span class="sxs-lookup"><span data-stu-id="3937e-312">For example:</span></span>

* <span data-ttu-id="3937e-313">如果 web 应用主要是缓存字符串，则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="3937e-313">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="3937e-314">应用可以将所有条目的大小指定为1，而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="3937e-314">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="3937e-315">如果 <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> 未设置，则缓存的大小不受限制。</span><span class="sxs-lookup"><span data-stu-id="3937e-315">If <xref:Microsoft.Extensions.Caching.Memory.MemoryCacheOptions.SizeLimit> is not set, the cache grows without bound.</span></span> <span data-ttu-id="3937e-316">当系统内存不足时，ASP.NET Core 运行时不会剪裁缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-316">The ASP.NET Core runtime does not trim the cache when system memory is low.</span></span> <span data-ttu-id="3937e-317">应用程序的体系结构非常多：</span><span class="sxs-lookup"><span data-stu-id="3937e-317">Apps much be architected to:</span></span>

* <span data-ttu-id="3937e-318">限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="3937e-318">Limit cache growth.</span></span>
* <span data-ttu-id="3937e-319"><xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> 当可用内存有限时调用或：</span><span class="sxs-lookup"><span data-stu-id="3937e-319">Call <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Compact*> or <xref:Microsoft.Extensions.Caching.Memory.MemoryCache.Remove*> when available memory is limited:</span></span>

<span data-ttu-id="3937e-320">以下代码创建了 <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> [依赖关系注入](xref:fundamentals/dependency-injection)可访问的无大小固定大小：</span><span class="sxs-lookup"><span data-stu-id="3937e-320">The following code creates a unitless fixed size <xref:Microsoft.Extensions.Caching.Memory.MemoryCache> accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="3937e-321">`SizeLimit`没有单位。</span><span class="sxs-lookup"><span data-stu-id="3937e-321">`SizeLimit` does not have units.</span></span> <span data-ttu-id="3937e-322">如果已设置缓存大小限制，则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-322">Cached entries must specify size in whatever units they deem most appropriate if the cache size limit has been set.</span></span> <span data-ttu-id="3937e-323">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="3937e-323">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="3937e-324">如果缓存条目大小之和超过指定的值，则不会缓存条目 `SizeLimit` 。</span><span class="sxs-lookup"><span data-stu-id="3937e-324">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="3937e-325">如果未设置任何缓存大小限制，则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="3937e-325">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="3937e-326">以下代码将注册 `MyMemoryCache` 到[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="3937e-326">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="3937e-327">`MyMemoryCache`对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件，将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-327">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="3937e-328">以下代码使用 `MyMemoryCache` ：</span><span class="sxs-lookup"><span data-stu-id="3937e-328">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="3937e-329">缓存项的大小可以通过[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法来设置：</span><span class="sxs-lookup"><span data-stu-id="3937e-329">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

### <a name="memorycachecompact"></a><span data-ttu-id="3937e-330">MemoryCache</span><span class="sxs-lookup"><span data-stu-id="3937e-330">MemoryCache.Compact</span></span>

<span data-ttu-id="3937e-331">`MemoryCache.Compact`尝试按以下顺序删除缓存的指定百分比：</span><span class="sxs-lookup"><span data-stu-id="3937e-331">`MemoryCache.Compact` attempts to remove the specified percentage of the cache in the following order:</span></span>

* <span data-ttu-id="3937e-332">所有过期项。</span><span class="sxs-lookup"><span data-stu-id="3937e-332">All expired items.</span></span>
* <span data-ttu-id="3937e-333">按优先级排序。</span><span class="sxs-lookup"><span data-stu-id="3937e-333">Items by priority.</span></span> <span data-ttu-id="3937e-334">首先删除最低优先级项。</span><span class="sxs-lookup"><span data-stu-id="3937e-334">Lowest priority items are removed first.</span></span>
* <span data-ttu-id="3937e-335">最近最少使用的对象。</span><span class="sxs-lookup"><span data-stu-id="3937e-335">Least recently used objects.</span></span>
* <span data-ttu-id="3937e-336">绝对过期的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-336">Items with the earliest absolute expiration.</span></span>
* <span data-ttu-id="3937e-337">具有最早的可调过期项的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-337">Items with the earliest sliding expiration.</span></span>

<span data-ttu-id="3937e-338">永远不会删除具有优先级的固定项 <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> 。</span><span class="sxs-lookup"><span data-stu-id="3937e-338">Pinned items with priority <xref:Microsoft.Extensions.Caching.Memory.CacheItemPriority.NeverRemove> are never removed.</span></span>

[!code-csharp[](memory/3.0sample/RPcache/Pages/TestCache.cshtml.cs?name=snippet3)]

<span data-ttu-id="3937e-339">有关详细信息，请参阅[GitHub 上的 Compact 源](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393)。</span><span class="sxs-lookup"><span data-stu-id="3937e-339">See [Compact source on GitHub](https://github.com/dotnet/extensions/blob/v3.0.0-preview8.19405.4/src/Caching/Memory/src/MemoryCache.cs#L382-L393) for more information.</span></span>

## <a name="cache-dependencies"></a><span data-ttu-id="3937e-340">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="3937e-340">Cache dependencies</span></span>

<span data-ttu-id="3937e-341">下面的示例演示如何在依赖条目过期时使缓存条目过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-341">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="3937e-342"><xref:Microsoft.Extensions.Primitives.CancellationChangeToken>添加到缓存的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-342">A <xref:Microsoft.Extensions.Primitives.CancellationChangeToken> is added to the cached item.</span></span> <span data-ttu-id="3937e-343">当 `Cancel` 在上调用时 `CancellationTokenSource` ，将逐出两个缓存项。</span><span class="sxs-lookup"><span data-stu-id="3937e-343">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="3937e-344">使用 `CancellationTokenSource` 允许将多个缓存条目作为一个组逐出。</span><span class="sxs-lookup"><span data-stu-id="3937e-344">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="3937e-345">`using`在上面的代码中，在块中创建的缓存条目 `using` 将继承触发器和过期设置。</span><span class="sxs-lookup"><span data-stu-id="3937e-345">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="3937e-346">附加说明</span><span class="sxs-lookup"><span data-stu-id="3937e-346">Additional notes</span></span>

* <span data-ttu-id="3937e-347">使用回调来重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="3937e-347">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="3937e-348">多个请求可以查找缓存的密钥值为空，因为回调未完成。</span><span class="sxs-lookup"><span data-stu-id="3937e-348">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="3937e-349">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="3937e-349">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="3937e-350">当使用一个缓存条目创建另一个缓存条目时，子对象会复制父条目的过期令牌和基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="3937e-350">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="3937e-351">手动删除或更新父项时，子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="3937e-351">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="3937e-352">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="3937e-352">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="background-cache-update"></a><span data-ttu-id="3937e-353">后台缓存更新</span><span class="sxs-lookup"><span data-stu-id="3937e-353">Background cache update</span></span>

<span data-ttu-id="3937e-354">使用[后台服务](xref:fundamentals/host/hosted-services)（如） <xref:Microsoft.Extensions.Hosting.IHostedService> 来更新缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-354">Use a [background service](xref:fundamentals/host/hosted-services) such as <xref:Microsoft.Extensions.Hosting.IHostedService> to update the cache.</span></span> <span data-ttu-id="3937e-355">后台服务可重新计算条目，然后仅在准备就绪时将其分配给缓存。</span><span class="sxs-lookup"><span data-stu-id="3937e-355">The background service can recompute the entries and then assign them to the cache only when they’re ready.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="3937e-356">其他资源</span><span class="sxs-lookup"><span data-stu-id="3937e-356">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
