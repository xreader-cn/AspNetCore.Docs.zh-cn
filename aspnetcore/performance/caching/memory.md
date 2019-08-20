---
title: 缓存在内存中 ASP.NET Core
author: rick-anderson
description: 了解如何在 ASP.NET Core 中的内存中缓存数据。
ms.author: riande
ms.custom: mvc
ms.date: 8/11/2019
uid: performance/caching/memory
ms.openlocfilehash: 474f71225371ea89b023ee077d4ecc03e9751681
ms.sourcegitcommit: f5f0ff65d4e2a961939762fb00e654491a2c772a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69030315"
---
# <a name="cache-in-memory-in-aspnet-core"></a><span data-ttu-id="67cdf-103">缓存在内存中 ASP.NET Core</span><span class="sxs-lookup"><span data-stu-id="67cdf-103">Cache in-memory in ASP.NET Core</span></span>

<span data-ttu-id="67cdf-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="67cdf-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [John Luo](https://github.com/JunTaoLuo), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="67cdf-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="67cdf-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/memory/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="caching-basics"></a><span data-ttu-id="67cdf-106">缓存基础知识</span><span class="sxs-lookup"><span data-stu-id="67cdf-106">Caching basics</span></span>

<span data-ttu-id="67cdf-107">通过减少生成内容所需的工作，缓存可以显著提高应用的性能和可伸缩性。</span><span class="sxs-lookup"><span data-stu-id="67cdf-107">Caching can significantly improve the performance and scalability of an app by reducing the work required to generate content.</span></span> <span data-ttu-id="67cdf-108">缓存对不经常更改的数据效果最佳。</span><span class="sxs-lookup"><span data-stu-id="67cdf-108">Caching works best with data that changes infrequently.</span></span> <span data-ttu-id="67cdf-109">缓存生成的数据副本的返回速度可以比从原始源返回更快。</span><span class="sxs-lookup"><span data-stu-id="67cdf-109">Caching makes a copy of data that can be returned much faster than from the original source.</span></span> <span data-ttu-id="67cdf-110">应该对应用进行编写和测试, 使其**永不**依赖于缓存的数据。</span><span class="sxs-lookup"><span data-stu-id="67cdf-110">Apps should be written and tested to **never** depend on cached data.</span></span>

<span data-ttu-id="67cdf-111">ASP.NET Core 支持多种不同的缓存。</span><span class="sxs-lookup"><span data-stu-id="67cdf-111">ASP.NET Core supports several different caches.</span></span> <span data-ttu-id="67cdf-112">最简单的缓存基于 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在 Web 服务器内存中的缓存。</span><span class="sxs-lookup"><span data-stu-id="67cdf-112">The simplest cache is based on the [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache), which represents a cache stored in the memory of the web server.</span></span> <span data-ttu-id="67cdf-113">在多个服务器的服务器场中运行的应用应确保会话在使用内存中缓存时处于粘滞。</span><span class="sxs-lookup"><span data-stu-id="67cdf-113">Apps that run on a server farm of multiple servers should ensure that sessions are sticky when using the in-memory cache.</span></span> <span data-ttu-id="67cdf-114">粘性会话可确保来自客户端的后续请求都转到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="67cdf-114">Sticky sessions ensure that subsequent requests from a client all go to the same server.</span></span> <span data-ttu-id="67cdf-115">例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。</span><span class="sxs-lookup"><span data-stu-id="67cdf-115">For example, Azure Web apps use [Application Request Routing](https://www.iis.net/learn/extensions/planning-for-arr) (ARR) to route all subsequent requests to the same server.</span></span>

<span data-ttu-id="67cdf-116">Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。</span><span class="sxs-lookup"><span data-stu-id="67cdf-116">Non-sticky sessions in a web farm require a [distributed cache](distributed.md) to avoid cache consistency problems.</span></span> <span data-ttu-id="67cdf-117">对于某些应用, 分布式缓存可支持比内存中缓存更高的向外扩展。</span><span class="sxs-lookup"><span data-stu-id="67cdf-117">For some apps, a distributed cache can support higher scale-out than an in-memory cache.</span></span> <span data-ttu-id="67cdf-118">使用分布式缓存可将缓存内存卸载到外部进程。</span><span class="sxs-lookup"><span data-stu-id="67cdf-118">Using a distributed cache offloads the cache memory to an external process.</span></span>

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="67cdf-119">除非将[CacheItemPriority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)设置为`CacheItemPriority.NeverRemove`, 否则`IMemoryCache`缓存会在内存压力下清除缓存条目。</span><span class="sxs-lookup"><span data-stu-id="67cdf-119">The `IMemoryCache` cache will evict cache entries under memory pressure unless the [cache priority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority) is set to `CacheItemPriority.NeverRemove`.</span></span> <span data-ttu-id="67cdf-120">可以通过设置`CacheItemPriority`来调整缓存在内存压力下清除项目的优先级。</span><span class="sxs-lookup"><span data-stu-id="67cdf-120">You can set the `CacheItemPriority` to adjust the priority with which the cache evicts items under memory pressure.</span></span>

::: moniker-end

<span data-ttu-id="67cdf-121">内存中缓存可以存储任何对象；分布式缓存接口仅限于`byte[]`。</span><span class="sxs-lookup"><span data-stu-id="67cdf-121">The in-memory cache can store any object; the distributed cache interface is limited to `byte[]`.</span></span> <span data-ttu-id="67cdf-122">内存中和分布式缓存将缓存项作为键值对。</span><span class="sxs-lookup"><span data-stu-id="67cdf-122">The in-memory and distributed cache store cache items as key-value pairs.</span></span>

## <a name="systemruntimecachingmemorycache"></a><span data-ttu-id="67cdf-123">System.Runtime.Caching/MemoryCache</span><span class="sxs-lookup"><span data-stu-id="67cdf-123">System.Runtime.Caching/MemoryCache</span></span>

<span data-ttu-id="67cdf-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache>([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于:</span><span class="sxs-lookup"><span data-stu-id="67cdf-124"><xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet package](https://www.nuget.org/packages/System.Runtime.Caching/)) can be used with:</span></span>

* <span data-ttu-id="67cdf-125">.NET Standard 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="67cdf-125">.NET Standard 2.0 or later.</span></span>
* <span data-ttu-id="67cdf-126">面向 .NET Standard 2.0 或更高版本的任何[.net 实现](/dotnet/standard/net-standard#net-implementation-support)。</span><span class="sxs-lookup"><span data-stu-id="67cdf-126">Any [.NET implementation](/dotnet/standard/net-standard#net-implementation-support) that targets .NET Standard 2.0 or later.</span></span> <span data-ttu-id="67cdf-127">例如, ASP.NET Core 2.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="67cdf-127">For example, ASP.NET Core 2.0 or later.</span></span>
* <span data-ttu-id="67cdf-128">.NET Framework 4.5 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="67cdf-128">.NET Framework 4.5 or later.</span></span>

<span data-ttu-id="67cdf-129">建议对 `System.Runtime.Caching`/`MemoryCache` 使用 [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (本文中所述), 因为它更好地集成到 ASP.NET Core 中。</span><span class="sxs-lookup"><span data-stu-id="67cdf-129">[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)/`IMemoryCache` (described in this article) is recommended over `System.Runtime.Caching`/`MemoryCache` because it's better integrated into ASP.NET Core.</span></span> <span data-ttu-id="67cdf-130">例如, `IMemoryCache`使用 ASP.NET Core[依赖关系注入](xref:fundamentals/dependency-injection)本身工作。</span><span class="sxs-lookup"><span data-stu-id="67cdf-130">For example, `IMemoryCache` works natively with ASP.NET Core [dependency injection](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="67cdf-131">将`System.Runtime.Caching` ASP.NET 4.x 中的代码移植到 ASP.NET Core 时, 请使用/ `MemoryCache`作为兼容性桥。</span><span class="sxs-lookup"><span data-stu-id="67cdf-131">Use `System.Runtime.Caching`/`MemoryCache` as a compatibility bridge when porting code from ASP.NET 4.x to ASP.NET Core.</span></span>

## <a name="cache-guidelines"></a><span data-ttu-id="67cdf-132">缓存指南</span><span class="sxs-lookup"><span data-stu-id="67cdf-132">Cache guidelines</span></span>

* <span data-ttu-id="67cdf-133">代码应始终具有回退选项, 以获取数据, 而**不**是依赖于可用的缓存值。</span><span class="sxs-lookup"><span data-stu-id="67cdf-133">Code should always have a fallback option to fetch data and **not** depend on a cached value being available.</span></span>
* <span data-ttu-id="67cdf-134">缓存使用稀有资源内存。</span><span class="sxs-lookup"><span data-stu-id="67cdf-134">The cache uses a scarce resource, memory.</span></span> <span data-ttu-id="67cdf-135">限制缓存增长:</span><span class="sxs-lookup"><span data-stu-id="67cdf-135">Limit cache growth:</span></span>
  * <span data-ttu-id="67cdf-136">不要使用外部输入作为缓存键。</span><span class="sxs-lookup"><span data-stu-id="67cdf-136">Do **not** use external input as cache keys.</span></span>
  * <span data-ttu-id="67cdf-137">使用过期限制缓存增长。</span><span class="sxs-lookup"><span data-stu-id="67cdf-137">Use expirations to limit cache growth.</span></span>
  * <span data-ttu-id="67cdf-138">[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="67cdf-138">[Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span> <span data-ttu-id="67cdf-139">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-139">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="67cdf-140">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-140">It's up to the developer to limit cache size.</span></span>

## <a name="using-imemorycache"></a><span data-ttu-id="67cdf-141">使用 IMemoryCache</span><span class="sxs-lookup"><span data-stu-id="67cdf-141">Using IMemoryCache</span></span>

> [!WARNING]
> <span data-ttu-id="67cdf-142">使用[依赖关系注入](xref:fundamentals/dependency-injection)中的*共享*内存缓存, `SetSize`并`Size`调用、 `SizeLimit`或以限制缓存大小可能会导致应用程序失败。</span><span class="sxs-lookup"><span data-stu-id="67cdf-142">Using a *shared* memory cache from [Dependency Injection](xref:fundamentals/dependency-injection) and calling `SetSize`, `Size`, or `SizeLimit` to limit cache size can cause the app to fail.</span></span> <span data-ttu-id="67cdf-143">在缓存上设置大小限制时, 在添加时, 所有项都必须指定大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-143">When a size limit is set on a cache, all entries must specify a size when being added.</span></span> <span data-ttu-id="67cdf-144">这可能会导致问题, 因为开发人员可能无法完全控制使用共享缓存的内容。</span><span class="sxs-lookup"><span data-stu-id="67cdf-144">This can lead to issues since developers may not have full control on what uses the shared cache.</span></span> <span data-ttu-id="67cdf-145">例如, Entity Framework Core 使用共享缓存并且未指定大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-145">For example, Entity Framework Core uses the shared cache and does not specify a size.</span></span> <span data-ttu-id="67cdf-146">如果应用设置了缓存大小限制并使用 EF Core, 则应用将引发`InvalidOperationException`。</span><span class="sxs-lookup"><span data-stu-id="67cdf-146">If an app sets a cache size limit and uses EF Core, the app throws an `InvalidOperationException`.</span></span>
> <span data-ttu-id="67cdf-147">当使用`SetSize`、 `Size`或`SizeLimit`来限制缓存时, 为缓存创建一个缓存单独。</span><span class="sxs-lookup"><span data-stu-id="67cdf-147">When using `SetSize`, `Size`, or `SizeLimit` to limit cache, create a cache singleton for caching.</span></span> <span data-ttu-id="67cdf-148">有关详细信息和示例, 请参阅[使用 SetSize、Size 和 SizeLimit 限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)。</span><span class="sxs-lookup"><span data-stu-id="67cdf-148">For more information and an example, see [Use SetSize, Size, and SizeLimit to limit cache size](#use-setsize-size-and-sizelimit-to-limit-cache-size).</span></span>

<span data-ttu-id="67cdf-149">内存中缓存是使用[依赖关系注入](xref:fundamentals/dependency-injection)从应用中引用的服务。</span><span class="sxs-lookup"><span data-stu-id="67cdf-149">In-memory caching is a *service* that's referenced from your app using [Dependency Injection](xref:fundamentals/dependency-injection).</span></span> <span data-ttu-id="67cdf-150">请在`ConfigureServices`中调用`AddMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="67cdf-150">Call `AddMemoryCache` in `ConfigureServices`:</span></span>

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

<span data-ttu-id="67cdf-151">在构造函数中请求 `IMemoryCache`实例：</span><span class="sxs-lookup"><span data-stu-id="67cdf-151">Request the `IMemoryCache` instance in the constructor:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="67cdf-152">`IMemoryCache`需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)"。</span><span class="sxs-lookup"><span data-stu-id="67cdf-152">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/).</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="67cdf-153">`IMemoryCache`需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), 此包可在[Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage)中找到的。</span><span class="sxs-lookup"><span data-stu-id="67cdf-153">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage).</span></span>

::: moniker-end

::: moniker range="> aspnetcore-2.0"

<span data-ttu-id="67cdf-154">`IMemoryCache`需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), 此包可在[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app)中找到的。</span><span class="sxs-lookup"><span data-stu-id="67cdf-154">`IMemoryCache` requires NuGet package [Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/), which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

::: moniker-end

<span data-ttu-id="67cdf-155">以下代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)来检查缓存中是否有时间。</span><span class="sxs-lookup"><span data-stu-id="67cdf-155">The following code uses [TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__) to check if a time is in the cache.</span></span> <span data-ttu-id="67cdf-156">如果未缓存时间, 则将创建一个新条目, 并[将其设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)为已添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="67cdf-156">If a time isn't cached, a new entry is created and added to the cache with [Set](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_).</span></span>

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

<span data-ttu-id="67cdf-157">将会显示当前时间和缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="67cdf-157">The current time and the cached time are displayed:</span></span>

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

<span data-ttu-id="67cdf-158">如果在`DateTime`超时期限内存在请求, 则缓存值将保留在缓存中。</span><span class="sxs-lookup"><span data-stu-id="67cdf-158">The cached `DateTime` value remains in the cache while there are requests within the timeout period.</span></span> <span data-ttu-id="67cdf-159">下图显示当前时间以及从缓存中检索的较早时间：</span><span class="sxs-lookup"><span data-stu-id="67cdf-159">The following image shows the current time and an older time retrieved from the cache:</span></span>

![显示了两个不同时间的索引视图](memory/_static/time.png)

<span data-ttu-id="67cdf-161">以下代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)和[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)来缓存数据。</span><span class="sxs-lookup"><span data-stu-id="67cdf-161">The following code uses [GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreate#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__) and [GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.getorcreateasync#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___) to cache data.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

<span data-ttu-id="67cdf-162">以下代码调用[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)来提取缓存的时间：</span><span class="sxs-lookup"><span data-stu-id="67cdf-162">The following code calls [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) to fetch the cached time:</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<span data-ttu-id="67cdf-163"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*>、 <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>和[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)是[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>类的扩展方法的一部分, 它扩展了的功能。</span><span class="sxs-lookup"><span data-stu-id="67cdf-163"><xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> , <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>, and [Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_) are extension methods part of the [CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) class that extends the capability of <xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>.</span></span> <span data-ttu-id="67cdf-164">有关其他缓存方法的说明, 请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)和[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)。</span><span class="sxs-lookup"><span data-stu-id="67cdf-164">See [IMemoryCache methods](/dotnet/api/microsoft.extensions.caching.memory.imemorycache) and [CacheExtensions methods](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions) for a description of other cache methods.</span></span>

## <a name="memorycacheentryoptions"></a><span data-ttu-id="67cdf-165">MemoryCacheEntryOptions</span><span class="sxs-lookup"><span data-stu-id="67cdf-165">MemoryCacheEntryOptions</span></span>

<span data-ttu-id="67cdf-166">下面的示例执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="67cdf-166">The following sample:</span></span>

* <span data-ttu-id="67cdf-167">设置绝对到期时间。</span><span class="sxs-lookup"><span data-stu-id="67cdf-167">Sets the absolute expiration time.</span></span> <span data-ttu-id="67cdf-168">这是条目可以被缓存的最长时间，防止可调过期持续更新时该条目过时太多。</span><span class="sxs-lookup"><span data-stu-id="67cdf-168">This is the maximum time the entry can be cached and prevents the item from becoming too stale when the sliding expiration is continuously renewed.</span></span>
* <span data-ttu-id="67cdf-169">设置可调过期时间。</span><span class="sxs-lookup"><span data-stu-id="67cdf-169">Sets a sliding expiration time.</span></span> <span data-ttu-id="67cdf-170">访问此缓存项的请求将重置可调过期时钟。</span><span class="sxs-lookup"><span data-stu-id="67cdf-170">Requests that access this cached item will reset the sliding expiration clock.</span></span>
* <span data-ttu-id="67cdf-171">将缓存优先级设置为`CacheItemPriority.NeverRemove`。</span><span class="sxs-lookup"><span data-stu-id="67cdf-171">Sets the cache priority to `CacheItemPriority.NeverRemove`.</span></span>
* <span data-ttu-id="67cdf-172">设置一个[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)它将在条目从缓存中清除后调用。</span><span class="sxs-lookup"><span data-stu-id="67cdf-172">Sets a [PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate) that will be called after the entry is evicted from the cache.</span></span> <span data-ttu-id="67cdf-173">在代码中运行该回调的线程不同于从缓存中移除条目的线程。</span><span class="sxs-lookup"><span data-stu-id="67cdf-173">The callback is run on a different thread from the code that removes the item from the cache.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a><span data-ttu-id="67cdf-174">使用 SetSize、Size 和 SizeLimit 限制缓存大小</span><span class="sxs-lookup"><span data-stu-id="67cdf-174">Use SetSize, Size, and SizeLimit to limit cache size</span></span>

<span data-ttu-id="67cdf-175">`MemoryCache`实例可以选择指定并强制实施大小限制。</span><span class="sxs-lookup"><span data-stu-id="67cdf-175">A `MemoryCache` instance may optionally specify and enforce a size limit.</span></span> <span data-ttu-id="67cdf-176">内存大小限制没有定义的度量单位, 因为缓存没有度量条目大小的机制。</span><span class="sxs-lookup"><span data-stu-id="67cdf-176">The memory size limit does not have a defined unit of measure because the cache has no mechanism to measure the size of entries.</span></span> <span data-ttu-id="67cdf-177">如果设置了缓存内存大小限制, 则所有条目都必须指定 size。</span><span class="sxs-lookup"><span data-stu-id="67cdf-177">If the cache memory size limit is set, all entries must specify size.</span></span> <span data-ttu-id="67cdf-178">ASP.NET Core 运行时不会根据内存压力限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-178">The ASP.NET Core runtime does not limit cache size based on memory pressure.</span></span> <span data-ttu-id="67cdf-179">开发人员需要限制缓存大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-179">It's up to the developer to limit cache size.</span></span> <span data-ttu-id="67cdf-180">指定的大小以开发人员选择的单位为单位。</span><span class="sxs-lookup"><span data-stu-id="67cdf-180">The size specified is in units the developer chooses.</span></span>

<span data-ttu-id="67cdf-181">例如:</span><span class="sxs-lookup"><span data-stu-id="67cdf-181">For example:</span></span>

* <span data-ttu-id="67cdf-182">如果 web 应用主要是缓存字符串, 则每个缓存条目大小都可以是字符串长度。</span><span class="sxs-lookup"><span data-stu-id="67cdf-182">If the web app was primarily caching strings, each cache entry size could be the string length.</span></span>
* <span data-ttu-id="67cdf-183">应用可以将所有条目的大小指定为 1, 而大小限制则为条目的计数。</span><span class="sxs-lookup"><span data-stu-id="67cdf-183">The app could specify the size of all entries as 1, and the size limit is the count of entries.</span></span>

<span data-ttu-id="67cdf-184">下面的代码创建可通过[依赖关系注入](xref:fundamentals/dependency-injection)访问的无单位固定大小[MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) :</span><span class="sxs-lookup"><span data-stu-id="67cdf-184">The following code creates a unitless fixed size [MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1) accessible by [dependency injection](xref:fundamentals/dependency-injection):</span></span>

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

<span data-ttu-id="67cdf-185">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)不包含单位。</span><span class="sxs-lookup"><span data-stu-id="67cdf-185">[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit) does not have units.</span></span> <span data-ttu-id="67cdf-186">如果已设置缓存内存大小, 则缓存条目必须以其认为最适合的任何单位指定大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-186">Cached entries must specify size in whatever units they deem most appropriate if the cache memory size has been set.</span></span> <span data-ttu-id="67cdf-187">缓存实例的所有用户都应使用同一单元系统。</span><span class="sxs-lookup"><span data-stu-id="67cdf-187">All users of a cache instance should use the same unit system.</span></span> <span data-ttu-id="67cdf-188">如果缓存条目大小之和超过指定`SizeLimit`的值, 则不会缓存条目。</span><span class="sxs-lookup"><span data-stu-id="67cdf-188">An entry will not be cached if the sum of the cached entry sizes exceeds the value specified by `SizeLimit`.</span></span> <span data-ttu-id="67cdf-189">如果未设置任何缓存大小限制, 则将忽略在该项上设置的缓存大小。</span><span class="sxs-lookup"><span data-stu-id="67cdf-189">If no cache size limit is set, the cache size set on the entry will be ignored.</span></span>

<span data-ttu-id="67cdf-190">以下代码将注册`MyMemoryCache`到[依赖关系注入](xref:fundamentals/dependency-injection)容器。</span><span class="sxs-lookup"><span data-stu-id="67cdf-190">The following code registers `MyMemoryCache` with the [dependency injection](xref:fundamentals/dependency-injection) container.</span></span>

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

<span data-ttu-id="67cdf-191">`MyMemoryCache`对于识别此大小限制缓存并知道如何适当地设置缓存条目大小的组件, 将创建为独立的内存缓存。</span><span class="sxs-lookup"><span data-stu-id="67cdf-191">`MyMemoryCache` is created as an independent memory cache for components that are aware of this size limited cache and know how to set cache entry size appropriately.</span></span>

<span data-ttu-id="67cdf-192">以下代码使用`MyMemoryCache`:</span><span class="sxs-lookup"><span data-stu-id="67cdf-192">The following code uses `MyMemoryCache`:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

<span data-ttu-id="67cdf-193">缓存项的大小可以通过[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法来设置:</span><span class="sxs-lookup"><span data-stu-id="67cdf-193">The size of the cache entry can be set by [Size](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size) or the [SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_) extension method:</span></span>

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a><span data-ttu-id="67cdf-194">缓存依赖关系</span><span class="sxs-lookup"><span data-stu-id="67cdf-194">Cache dependencies</span></span>

<span data-ttu-id="67cdf-195">以下示例演示在依赖项过期时如何使缓存项过期。</span><span class="sxs-lookup"><span data-stu-id="67cdf-195">The following sample shows how to expire a cache entry if a dependent entry expires.</span></span> <span data-ttu-id="67cdf-196">会将 `CancellationChangeToken`添加到缓存项。</span><span class="sxs-lookup"><span data-stu-id="67cdf-196">A `CancellationChangeToken` is added to the cached item.</span></span> <span data-ttu-id="67cdf-197">在 `Cancel`上调用 `CancellationTokenSource`，时，这两个缓存条目都将被清除。</span><span class="sxs-lookup"><span data-stu-id="67cdf-197">When `Cancel` is called on the `CancellationTokenSource`, both cache entries are evicted.</span></span>

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

<span data-ttu-id="67cdf-198">使用`CancellationTokenSource`可以将多个缓存条目作为一个组来清除。</span><span class="sxs-lookup"><span data-stu-id="67cdf-198">Using a `CancellationTokenSource` allows multiple cache entries to be evicted as a group.</span></span> <span data-ttu-id="67cdf-199">使用上面代码中的 `using`模式时`using`块中创建的缓存条目会继承触发器和过期时间设置。</span><span class="sxs-lookup"><span data-stu-id="67cdf-199">With the `using` pattern in the code above, cache entries created inside the `using` block will inherit triggers and expiration settings.</span></span>

## <a name="additional-notes"></a><span data-ttu-id="67cdf-200">附加说明</span><span class="sxs-lookup"><span data-stu-id="67cdf-200">Additional notes</span></span>

* <span data-ttu-id="67cdf-201">使用回调重新填充缓存项时：</span><span class="sxs-lookup"><span data-stu-id="67cdf-201">When using a callback to repopulate a cache item:</span></span>

  * <span data-ttu-id="67cdf-202">多个请求可能会发现缓存的键值为空，因为回调尚未完成。</span><span class="sxs-lookup"><span data-stu-id="67cdf-202">Multiple requests can find the cached key value empty because the callback hasn't completed.</span></span>
  * <span data-ttu-id="67cdf-203">这可能会导致多个线程重新填充缓存的项。</span><span class="sxs-lookup"><span data-stu-id="67cdf-203">This can result in several threads repopulating the cached item.</span></span>

* <span data-ttu-id="67cdf-204">使用一个缓存条目创建另一个缓存条目时，子条目会复制父条目的过期令牌以及基于时间的过期设置。</span><span class="sxs-lookup"><span data-stu-id="67cdf-204">When one cache entry is used to create another, the child copies the parent entry's expiration tokens and time-based expiration settings.</span></span> <span data-ttu-id="67cdf-205">手动删除或更新父项时, 子级不会过期。</span><span class="sxs-lookup"><span data-stu-id="67cdf-205">The child isn't expired by manual removal or updating of the parent entry.</span></span>

* <span data-ttu-id="67cdf-206">使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后将触发的回调。</span><span class="sxs-lookup"><span data-stu-id="67cdf-206">Use [PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks) to set the callbacks that will be fired after the cache entry is evicted from the cache.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="67cdf-207">其他资源</span><span class="sxs-lookup"><span data-stu-id="67cdf-207">Additional resources</span></span>

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
