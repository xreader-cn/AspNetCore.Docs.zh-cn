---
title: 缓存在内存中 ASP.NET Core
author: rick-anderson
description: 了解如何在 ASP.NET Core 中的内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 02/11/2019
uid: performance/caching/memory
ms.openlocfilehash: c115e43b9dd4f838ab9600c2e105d86732d857ad
ms.sourcegitcommit: 5f299daa7c8102d56a63b214b9a34cc4bc87bc42
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2019
ms.locfileid: "58208261"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="9b938-103">缓存在内存中 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="9b938-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="9b938-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="9b938-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="9b938-105">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9b938-105">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="9b938-106">缓存的基础知识</span><span class="sxs-lookup"><span data-stu-id="9b938-106">Caching basics</span></span>

<span data-ttu-id="9b938-107">通过减少生成内容所需的工作，缓存可以显著提高应用的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="9b938-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="9b938-108">缓存对不经常更改的数据效果最佳。</span><span class="sxs-lookup"><span data-stu-id="9b938-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="9b938-109">缓存生成的数据副本的返回速度可以比从原始源返回更快。</span><span class="sxs-lookup"><span data-stu-id="9b938-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="9b938-110">在编写并测试应用时，应避免依赖缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="9b938-110">You should write and test your app to never depend on cached data.</span></span>

<span data-ttu-id="9b938-111">ASP.NET Core 支持多种不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="9b938-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="9b938-112">最简单的缓存基于 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在 Web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="9b938-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="9b938-113">在包含多个服务器的服务器场上运行的应用应确保在使用内存中缓存时，会话是粘性的。</span><span class="sxs-lookup"><span data-stu-id="9b938-113">Apps which run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="9b938-114">粘性会话可确保来自客户端的后续请求都转到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="9b938-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="9b938-115">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="9b938-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="9b938-116">Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="9b938-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="9b938-117">对于某些应用，分布式的缓存可以支持更高版本向外缩放比内存中缓存。</span><span class="sxs-lookup"><span data-stu-id="9b938-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="9b938-118">使用分布式缓存可将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="9b938-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9b938-119">除非将[CacheItemPriority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)设置为`CacheItemPriority.NeverRemove`, 否则`IMemoryCache`缓存会在内存压力下清除缓存条目。</span><span class="sxs-lookup"><span data-stu-id="9b938-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="9b938-120">可以通过设置`CacheItemPriority`来调整缓存在内存压力下清除项目的优先级。</span><span class="sxs-lookup"><span data-stu-id="9b938-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="9b938-121">内存中缓存可以存储任何对象；分布式缓存接口仅限于`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="9b938-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="9b938-122">内存中和分布式缓存将缓存项存储为键 / 值对。</span><span class="sxs-lookup"><span data-stu-id="9b938-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="9b938-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="9b938-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="9b938-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于：</span><span class="sxs-lookup"><span data-stu-id="9b938-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="9b938-125">.NET standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b938-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="9b938-126">任何[.NET 实现](/dotnet/standard/net-standard#net-implementation-support)面向.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b938-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="9b938-127">例如，ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b938-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="9b938-128">.NET framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="9b938-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="9b938-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` （本主题中所述） 建议`System.Runtime.Caching` / `MemoryCache`由于它更好地集成到 ASP.NET Core。</span><span class="sxs-lookup"><span data-stu-id="9b938-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this topic) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="9b938-130">例如，`IMemoryCache`适用于 ASP.NET Core 的本机[依赖关系注入](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="9b938-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="9b938-131">使用`System.Runtime.Caching` / `MemoryCache`起到桥梁兼容性时将从 ASP.NET 代码移植到 ASP.NET Core 4.x。</span><span class="sxs-lookup"><span data-stu-id="9b938-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="9b938-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="9b938-132">Cache guidelines</span></span>

* <span data-ttu-id="9b938-133">代码应始终具有一个回退选项，以提取数据并**不**取决于可用的已缓存值。</span><span class="sxs-lookup"><span data-stu-id="9b938-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="9b938-134">缓存使用稀缺资源，内存。</span><span class="sxs-lookup"><span data-stu-id="9b938-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="9b938-135">限制缓存增长：</span><span class="sxs-lookup"><span data-stu-id="9b938-135">Limit cache growth:</span></span>
  * <span data-ttu-id="9b938-136">不要**不**外部输入用作缓存密钥。</span><span class="sxs-lookup"><span data-stu-id="9b938-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="9b938-137">使用过期时间来限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="9b938-137">Use expirations to limit cache growth.</span></span>
  * [<span data-ttu-id="9b938-138">使用 SetSize、 大小和大小限制来限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="9b938-138">Use SetSize, Size, and SizeLimit to limit cache size</span></span>](#use-setsize-size-and-sizelimit-to-limit-cache-size)

## <a name="using-imemorycache"></a><span data-ttu-id="9b938-139">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="9b938-139">Using IMemoryCache</span></span>

<span data-ttu-id="9b938-140">内存中缓存是使用[依赖关系注入](../../fundamentals/dependency-injection.md)从应用中引用的服务。</span><span class="sxs-lookup"><span data-stu-id="9b938-140">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](../../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="9b938-141">请在`ConfigureServices`中调用`AddMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="9b938-141">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="9b938-142">在构造函数中请求 `IMemoryCache`实例：</span><span class="sxs-lookup"><span data-stu-id="9b938-142">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="9b938-143">`IMemoryCache` 需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)。</span><span class="sxs-lookup"><span data-stu-id="9b938-143">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="9b938-144">`IMemoryCache` 需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)，其现已推出[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)。</span><span class="sxs-lookup"><span data-stu-id="9b938-144">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="9b938-145">`IMemoryCache` 需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)，其现已推出[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="9b938-145">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="9b938-146">下面的代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)用于检查一次是否在缓存中。</span><span class="sxs-lookup"><span data-stu-id="9b938-146">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="9b938-147">如果不缓存一次，创建并添加到缓存中，使用新的条目[设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)。</span><span class="sxs-lookup"><span data-stu-id="9b938-147">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="9b938-148">将会显示当前时间和缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="9b938-148">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="9b938-149">缓存`DateTime`值保留在缓存中时的超时期限 （和由于内存压力而没有逐出） 内的请求。</span><span class="sxs-lookup"><span data-stu-id="9b938-149">The cached `DateTime` value remains in the cache while there are requests within the timeout period (and no eviction due to memory pressure).</span></span> <span data-ttu-id="9b938-150">下图显示当前时间以及从缓存中检索的较早时间：</span><span class="sxs-lookup"><span data-stu-id="9b938-150">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示了两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="9b938-152">下面的代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)并[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)缓存数据。</span><span class="sxs-lookup"><span data-stu-id="9b938-152">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="9b938-153">以下代码调用[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)来提取缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="9b938-153">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="9b938-154"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>，并[获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)扩展方法一部分[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)类，该类扩展的功能<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>。</span><span class="sxs-lookup"><span data-stu-id="9b938-154"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="9b938-155">请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)并[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)有关缓存的其他方法的说明。</span><span class="sxs-lookup"><span data-stu-id="9b938-155">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="9b938-156">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="9b938-156">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="9b938-157">下面的示例执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="9b938-157">The following sample:</span></span>

* <span data-ttu-id="9b938-158">设置绝对到期时间。</span><span class="sxs-lookup"><span data-stu-id="9b938-158">Sets the absolute expiration time.</span></span> <span data-ttu-id="9b938-159">这是条目可以被缓存的最长时间，防止可调过期持续更新时该条目过时太多。</span><span class="sxs-lookup"><span data-stu-id="9b938-159">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
* <span data-ttu-id="9b938-160">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="9b938-160">Sets a sliding expiration time.</span></span> <span data-ttu-id="9b938-161">访问此缓存项的请求将重置可调过期时钟。</span><span class="sxs-lookup"><span data-stu-id="9b938-161">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="9b938-162">缓存优先级设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="9b938-162">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="9b938-163">设置一个[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)它将在条目从缓存中清除后调用。</span><span class="sxs-lookup"><span data-stu-id="9b938-163">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="9b938-164">在代码中运行该回调的线程不同于从缓存中移除条目的线程。</span><span class="sxs-lookup"><span data-stu-id="9b938-164">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="9b938-165">使用 SetSize、 大小和大小限制来限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="9b938-165">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="9b938-166">一个`MemoryCache`实例可能会根据需要指定和强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="9b938-166">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="9b938-167">因为缓存中不有任何机制来度量值的条目的大小，没有一个定义的度量单位的内存大小限制。</span><span class="sxs-lookup"><span data-stu-id="9b938-167">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="9b938-168">如果设置的缓存内存大小限制，则所有条目必须都指定大小。</span><span class="sxs-lookup"><span data-stu-id="9b938-168">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="9b938-169">指定的大小为开发人员选择的单位。</span><span class="sxs-lookup"><span data-stu-id="9b938-169">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="9b938-170">例如：</span><span class="sxs-lookup"><span data-stu-id="9b938-170">For example:</span></span>

* <span data-ttu-id="9b938-171">如果 web 应用程序主要已缓存字符串，每个缓存项大小可能是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="9b938-171">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="9b938-172">应用程序可以为 1，指定的所有条目的大小，大小限制为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="9b938-172">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="9b938-173">下面的代码创建无单位固定大小[MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1)可供源[依赖关系注入](xref:fundamentals/dependency-injection):</span><span class="sxs-lookup"><span data-stu-id="9b938-173">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="9b938-174">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)没有单位。</span><span class="sxs-lookup"><span data-stu-id="9b938-174">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="9b938-175">缓存的条目必须在他们认为最合适，如果缓存内存大小已设置任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="9b938-175">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="9b938-176">缓存实例的所有用户应都使用相同单位系统。</span><span class="sxs-lookup"><span data-stu-id="9b938-176">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="9b938-177">如果已缓存的条目大小的总和超过指定的值，不会缓存条目`SizeLimit`。</span><span class="sxs-lookup"><span data-stu-id="9b938-177">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="9b938-178">如果设置缓存大小限制，将忽略在条目上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="9b938-178">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="9b938-179">下面的代码寄存器`MyMemoryCache`与[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="9b938-179">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="9b938-180">`MyMemoryCache` 创建作为独立的内存缓存的组件的此大小限制缓存的了解，并知道如何适当地设置缓存项大小。</span><span class="sxs-lookup"><span data-stu-id="9b938-180">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="9b938-181">下面的代码使用`MyMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="9b938-181">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="9b938-182">可以通过设置缓存项的大小[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法：</span><span class="sxs-lookup"><span data-stu-id="9b938-182">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="9b938-183">缓存依赖项</span><span class="sxs-lookup"><span data-stu-id="9b938-183">Cache dependencies</span></span>

<span data-ttu-id="9b938-184">以下示例演示在依赖项过期时如何使缓存项过期。</span><span class="sxs-lookup"><span data-stu-id="9b938-184">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="9b938-185">会将 `CancellationChangeToken`添加到缓存项。</span><span class="sxs-lookup"><span data-stu-id="9b938-185">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="9b938-186">在 `Cancel`上调用 `CancellationTokenSource`，时，这两个缓存条目都将被清除。</span><span class="sxs-lookup"><span data-stu-id="9b938-186">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="9b938-187">使用`CancellationTokenSource`可以将多个缓存条目作为一个组来清除。</span><span class="sxs-lookup"><span data-stu-id="9b938-187">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="9b938-188">使用上面代码中的 `using`模式时`using`块中创建的缓存条目会继承触发器和过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="9b938-188">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="9b938-189">附加说明</span><span class="sxs-lookup"><span data-stu-id="9b938-189">Additional notes</span></span>

* <span data-ttu-id="9b938-190">使用回调重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="9b938-190">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="9b938-191">多个请求可能会发现缓存的键值为空，因为回调尚未完成。</span><span class="sxs-lookup"><span data-stu-id="9b938-191">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="9b938-192">这可能导致重新填充缓存的项的多个线程。</span><span class="sxs-lookup"><span data-stu-id="9b938-192">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="9b938-193">使用一个缓存条目创建另一个缓存条目时，子条目会复制父条目的过期令牌以及基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="9b938-193">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="9b938-194">子级不是通过手动删除过期的或更新的父项。</span><span class="sxs-lookup"><span data-stu-id="9b938-194">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="9b938-195">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后，将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="9b938-195">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9b938-196">其他资源</span><span class="sxs-lookup"><span data-stu-id="9b938-196">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
