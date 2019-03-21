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
# <a name="cache-in-memory-in-aspnet-core"></a>缓存在内存中 ASP.NET Core

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)， [John Luo](https://github.com/JunTaoLuo)，和[Steve Smith](https://ardalis.com/)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/memory/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="caching-basics"></a>缓存的基础知识

通过减少生成内容所需的工作，缓存可以显著提高应用的性能和可伸缩性。 缓存对不经常更改的数据效果最佳。 缓存生成的数据副本的返回速度可以比从原始源返回更快。 在编写并测试应用时，应避免依赖缓存的数据。

ASP.NET Core 支持多种不同的缓存。 最简单的缓存基于 [IMemoryCache](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)，它表示存储在 Web 服务器内存中的缓存。 在包含多个服务器的服务器场上运行的应用应确保在使用内存中缓存时，会话是粘性的。 粘性会话可确保来自客户端的后续请求都转到同一台服务器。 例如，Azure Web 应用使用[应用程序请求路由](https://www.iis.net/learn/extensions/planning-for-arr)(ARR) 将所有的后续请求路由到同一台服务器。

Web 场中的非粘性会话需要[分布式缓存](distributed.md)以避免缓存一致性问题。 对于某些应用，分布式的缓存可以支持更高版本向外缩放比内存中缓存。 使用分布式缓存可将缓存内存卸载到外部进程。

::: moniker range="< aspnetcore-2.0"

除非将[CacheItemPriority](/dotnet/api/microsoft.extensions.caching.memory.cacheitempriority)设置为`CacheItemPriority.NeverRemove`, 否则`IMemoryCache`缓存会在内存压力下清除缓存条目。 可以通过设置`CacheItemPriority`来调整缓存在内存压力下清除项目的优先级。

::: moniker-end

内存中缓存可以存储任何对象；分布式缓存接口仅限于`byte[]`。 内存中和分布式缓存将缓存项存储为键 / 值对。

## <a name="systemruntimecachingmemorycache"></a>System.Runtime.Caching/MemoryCache

<xref:System.Runtime.Caching>/<xref:System.Runtime.Caching.MemoryCache> ([NuGet 包](https://www.nuget.org/packages/System.Runtime.Caching/)) 可用于：

* .NET standard 2.0 或更高版本。
* 任何[.NET 实现](/dotnet/standard/net-standard#net-implementation-support)面向.NET Standard 2.0 或更高版本。 例如，ASP.NET Core 2.0 或更高版本。
* .NET framework 4.5 或更高版本。

[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/) / `IMemoryCache` （本主题中所述） 建议`System.Runtime.Caching` / `MemoryCache`由于它更好地集成到 ASP.NET Core。 例如，`IMemoryCache`适用于 ASP.NET Core 的本机[依赖关系注入](xref:fundamentals/dependency-injection)。

使用`System.Runtime.Caching` / `MemoryCache`起到桥梁兼容性时将从 ASP.NET 代码移植到 ASP.NET Core 4.x。

## <a name="cache-guidelines"></a>缓存指南

* 代码应始终具有一个回退选项，以提取数据并**不**取决于可用的已缓存值。
* 缓存使用稀缺资源，内存。 限制缓存增长：
  * 不要**不**外部输入用作缓存密钥。
  * 使用过期时间来限制缓存增长。
  * [使用 SetSize、 大小和大小限制来限制缓存大小](#use-setsize-size-and-sizelimit-to-limit-cache-size)

## <a name="using-imemorycache"></a>使用 IMemoryCache

内存中缓存是使用[依赖关系注入](../../fundamentals/dependency-injection.md)从应用中引用的服务。 请在`ConfigureServices`中调用`AddMemoryCache`:

[!code-csharp[](memory/sample/WebCache/Startup.cs?highlight=9)]

在构造函数中请求 `IMemoryCache`实例：

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ctor)]

::: moniker range="< aspnetcore-2.0"

`IMemoryCache` 需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

`IMemoryCache` 需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)，其现已推出[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)。

::: moniker-end

::: moniker range="> aspnetcore-2.0"

`IMemoryCache` 需要 NuGet 包[Microsoft.Extensions.Caching.Memory](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Memory/)，其现已推出[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)。

::: moniker-end

下面的代码使用[TryGetValue](/dotnet/api/microsoft.extensions.caching.memory.imemorycache.trygetvalue?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_IMemoryCache_TryGetValue_System_Object_System_Object__)用于检查一次是否在缓存中。 如果不缓存一次，创建并添加到缓存中，使用新的条目[设置](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.set?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_CacheExtensions_Set__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object___0_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_)。

[!code-csharp [](memory/sample/WebCache/CacheKeys.cs)]

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet1)]

将会显示当前时间和缓存的时间：

[!code-cshtml[](memory/sample/WebCache/Views/Home/Cache.cshtml)]

缓存`DateTime`值保留在缓存中时的超时期限 （和由于内存压力而没有逐出） 内的请求。 下图显示当前时间以及从缓存中检索的较早时间：

![显示了两个不同时间的索引视图](memory/_static/time.png)

下面的代码使用[GetOrCreate](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreate__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry___0__)并[GetOrCreateAsync](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions#Microsoft_Extensions_Caching_Memory_CacheExtensions_GetOrCreateAsync__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_System_Func_Microsoft_Extensions_Caching_Memory_ICacheEntry_System_Threading_Tasks_Task___0___)缓存数据。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet2&highlight=3-7,14-19)]

以下代码调用[Get](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)来提取缓存的时间：

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_gct)]

<xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreate*> <xref:Microsoft.Extensions.Caching.Memory.CacheExtensions.GetOrCreateAsync*>，并[获取](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions.get#Microsoft_Extensions_Caching_Memory_CacheExtensions_Get__1_Microsoft_Extensions_Caching_Memory_IMemoryCache_System_Object_)扩展方法一部分[CacheExtensions](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)类，该类扩展的功能<xref:Microsoft.Extensions.Caching.Memory.IMemoryCache>。 请参阅[IMemoryCache 方法](/dotnet/api/microsoft.extensions.caching.memory.imemorycache)并[CacheExtensions 方法](/dotnet/api/microsoft.extensions.caching.memory.cacheextensions)有关缓存的其他方法的说明。

## <a name="memorycacheentryoptions"></a>MemoryCacheEntryOptions

下面的示例执行以下操作：

* 设置绝对到期时间。 这是条目可以被缓存的最长时间，防止可调过期持续更新时该条目过时太多。
* 设置可调过期时间。 访问此缓存项的请求将重置可调过期时钟。
* 缓存优先级设置为`CacheItemPriority.NeverRemove`。
* 设置一个[PostEvictionDelegate](/dotnet/api/microsoft.extensions.caching.memory.postevictiondelegate)它将在条目从缓存中清除后调用。 在代码中运行该回调的线程不同于从缓存中移除条目的线程。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_et&highlight=14-21)]

::: moniker range=">= aspnetcore-2.0"

## <a name="use-setsize-size-and-sizelimit-to-limit-cache-size"></a>使用 SetSize、 大小和大小限制来限制缓存大小

一个`MemoryCache`实例可能会根据需要指定和强制实施大小限制。 因为缓存中不有任何机制来度量值的条目的大小，没有一个定义的度量单位的内存大小限制。 如果设置的缓存内存大小限制，则所有条目必须都指定大小。 指定的大小为开发人员选择的单位。

例如：

* 如果 web 应用程序主要已缓存字符串，每个缓存项大小可能是字符串长度。
* 应用程序可以为 1，指定的所有条目的大小，大小限制为条目的计数。

下面的代码创建无单位固定大小[MemoryCache](/dotnet/api/microsoft.extensions.caching.memory.memorycache?view=aspnetcore-2.1)可供源[依赖关系注入](xref:fundamentals/dependency-injection):

[!code-csharp[](memory/sample/RPcache/Services/MyMemoryCache.cs?name=snippet)]

[SizeLimit](/dotnet/api/microsoft.extensions.caching.memory.memorycacheoptions.sizelimit?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheOptions_SizeLimit)没有单位。 缓存的条目必须在他们认为最合适，如果缓存内存大小已设置任何单位指定大小。 缓存实例的所有用户应都使用相同单位系统。 如果已缓存的条目大小的总和超过指定的值，不会缓存条目`SizeLimit`。 如果设置缓存大小限制，将忽略在条目上设置的缓存大小。

下面的代码寄存器`MyMemoryCache`与[依赖关系注入](xref:fundamentals/dependency-injection)容器。

[!code-csharp[](memory/sample/RPcache/Startup.cs?name=snippet&highlight=5)]

`MyMemoryCache` 创建作为独立的内存缓存的组件的此大小限制缓存的了解，并知道如何适当地设置缓存项大小。

下面的代码使用`MyMemoryCache`:

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet)]

可以通过设置缓存项的大小[大小](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryoptions.size?view=aspnetcore-2.1#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_Size)或[SetSize](/dotnet/api/microsoft.extensions.caching.memory.memorycacheentryextensions.setsize?view=aspnetcore-2.0#Microsoft_Extensions_Caching_Memory_MemoryCacheEntryExtensions_SetSize_Microsoft_Extensions_Caching_Memory_MemoryCacheEntryOptions_System_Int64_)扩展方法：

[!code-csharp[](memory/sample/RPcache/Pages/About.cshtml.cs?name=snippet2&highlight=9,10,14,15)]

::: moniker-end

## <a name="cache-dependencies"></a>缓存依赖项

以下示例演示在依赖项过期时如何使缓存项过期。 会将 `CancellationChangeToken`添加到缓存项。 在 `Cancel`上调用 `CancellationTokenSource`，时，这两个缓存条目都将被清除。

[!code-csharp[](memory/sample/WebCache/Controllers/HomeController.cs?name=snippet_ed)]

使用`CancellationTokenSource`可以将多个缓存条目作为一个组来清除。 使用上面代码中的 `using`模式时`using`块中创建的缓存条目会继承触发器和过期时间设置。

## <a name="additional-notes"></a>附加说明

* 使用回调重新填充缓存项时：

  * 多个请求可能会发现缓存的键值为空，因为回调尚未完成。
  * 这可能导致重新填充缓存的项的多个线程。

* 使用一个缓存条目创建另一个缓存条目时，子条目会复制父条目的过期令牌以及基于时间的过期设置。 子级不是通过手动删除过期的或更新的父项。

* 使用[PostEvictionCallbacks](/dotnet/api/microsoft.extensions.caching.memory.icacheentry.postevictioncallbacks#Microsoft_Extensions_Caching_Memory_ICacheEntry_PostEvictionCallbacks)设置从缓存中逐出缓存项后，将触发的回调。

## <a name="additional-resources"></a>其他资源

* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
