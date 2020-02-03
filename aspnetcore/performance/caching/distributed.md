---
title: ASP.NET Core 中的分布式缓存
author: guardrex
description: 了解如何使用 ASP.NET Core 分布式缓存来改善应用程序的性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/22/2020
uid: performance/caching/distributed
ms.openlocfilehash: 3e039a26505aed1bcc0299880039760fef19fd67
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727240"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="b7b76-103">ASP.NET Core 中的分布式缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="b7b76-104">作者： [Luke Latham](https://github.com/guardrex)、 [Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b7b76-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b7b76-105">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="b7b76-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="b7b76-106">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="b7b76-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="b7b76-107">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="b7b76-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="b7b76-108">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="b7b76-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="b7b76-109">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="b7b76-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="b7b76-110">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="b7b76-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="b7b76-111">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="b7b76-111">Doesn't use local memory.</span></span>

<span data-ttu-id="b7b76-112">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="b7b76-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="b7b76-113">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="b7b76-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="b7b76-114">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="b7b76-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="b7b76-115">无论选择哪种实现，应用都使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="b7b76-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="b7b76-116">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b7b76-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="b7b76-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="b7b76-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7b76-118">若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="b7b76-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b7b76-119">若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="b7b76-120">若要使用 NCache 分布式缓存，请将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b7b76-121">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="b7b76-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b7b76-122">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="b7b76-123">Redis 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-123">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="b7b76-124">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-124">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="b7b76-125">NCache 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-125">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b7b76-126">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="b7b76-126">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="b7b76-127">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-127">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="b7b76-128">Redis 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-128">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="b7b76-129">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-129">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="b7b76-130">NCache 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="b7b76-130">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="b7b76-131">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="b7b76-131">IDistributedCache interface</span></span>

<span data-ttu-id="b7b76-132"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="b7b76-132">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="b7b76-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="b7b76-133"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="b7b76-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-134"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="b7b76-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>中，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="b7b76-135"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="b7b76-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 会根据其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="b7b76-136"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="b7b76-137">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="b7b76-137">Establish distributed caching services</span></span>

<span data-ttu-id="b7b76-138">在 `Startup.ConfigureServices`中注册 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现。</span><span class="sxs-lookup"><span data-stu-id="b7b76-138">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="b7b76-139">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="b7b76-139">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="b7b76-140">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-140">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="b7b76-141">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-141">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="b7b76-142">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-142">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="b7b76-143">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-143">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="b7b76-144">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-144">Distributed Memory Cache</span></span>

<span data-ttu-id="b7b76-145">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现，用于将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-145">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="b7b76-146">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="b7b76-146">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="b7b76-147">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="b7b76-147">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="b7b76-148">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="b7b76-148">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="b7b76-149">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="b7b76-149">In development and testing scenarios.</span></span>
* <span data-ttu-id="b7b76-150">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="b7b76-150">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="b7b76-151">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="b7b76-151">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="b7b76-152">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="b7b76-152">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="b7b76-153">当应用程序在 `Startup.ConfigureServices`的开发环境中运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="b7b76-153">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="b7b76-154">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-154">Distributed SQL Server Cache</span></span>

<span data-ttu-id="b7b76-155">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="b7b76-155">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="b7b76-156">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="b7b76-156">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="b7b76-157">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="b7b76-157">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="b7b76-158">通过运行 `sql-cache create` 命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="b7b76-158">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="b7b76-159">提供 SQL Server 实例（`Data Source`）、数据库（`Initial Catalog`）、架构（例如 `dbo`）和表名（例如，`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="b7b76-159">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="b7b76-160">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="b7b76-160">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="b7b76-161">`sql-cache` 工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="b7b76-161">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="b7b76-163">应用应使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>的实例而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>来操作缓存值。</span><span class="sxs-lookup"><span data-stu-id="b7b76-163">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="b7b76-164">示例应用在 `Startup.ConfigureServices`的非开发环境中实施 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>：</span><span class="sxs-lookup"><span data-stu-id="b7b76-164">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b7b76-165"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （并且可以选择 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理之外（例如，由[机密管理器](xref:security/app-secrets)存储或在*appsettings*/appsettings 中存储） *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="b7b76-165">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="b7b76-166">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="b7b76-166">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="b7b76-167">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-167">Distributed Redis Cache</span></span>

<span data-ttu-id="b7b76-168">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="b7b76-168">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="b7b76-169">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="b7b76-169">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7b76-170">应用使用 `Startup.ConfigureServices`的非开发环境中的 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="b7b76-170">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="b7b76-171">应用使用 `Startup.ConfigureServices`的非开发环境中的 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="b7b76-171">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="b7b76-172">应用使用 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="b7b76-172">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

<span data-ttu-id="b7b76-173">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="b7b76-173">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="b7b76-174">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="b7b76-174">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="b7b76-175">在命令提示符下运行 `redis-server`。</span><span class="sxs-lookup"><span data-stu-id="b7b76-175">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="b7b76-176">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-176">Distributed NCache Cache</span></span>

<span data-ttu-id="b7b76-177">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="b7b76-177">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="b7b76-178">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="b7b76-178">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="b7b76-179">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="b7b76-179">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="b7b76-180">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="b7b76-180">To configure NCache:</span></span>

1. <span data-ttu-id="b7b76-181">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="b7b76-181">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="b7b76-182">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="b7b76-182">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="b7b76-183">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="b7b76-183">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="b7b76-184">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-184">Use the distributed cache</span></span>

<span data-ttu-id="b7b76-185">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请从应用程序中的任何构造函数请求 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实例。</span><span class="sxs-lookup"><span data-stu-id="b7b76-185">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="b7b76-186">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="b7b76-186">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b7b76-187">示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-187">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b7b76-188">当前时间使用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 进行缓存（有关详细信息，请参阅[泛型 Host： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)）：</span><span class="sxs-lookup"><span data-stu-id="b7b76-188">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b7b76-189">示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-189">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="b7b76-190">当前时间使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 进行缓存（有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="b7b76-190">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

<span data-ttu-id="b7b76-191">示例应用将 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 插入到 `IndexModel` 中，供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="b7b76-191">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="b7b76-192">每次加载索引页时，都会在 `OnGetAsync`中检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="b7b76-192">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="b7b76-193">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="b7b76-193">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="b7b76-194">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="b7b76-194">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="b7b76-195">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="b7b76-195">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="b7b76-196">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="b7b76-196">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> <span data-ttu-id="b7b76-197">对于 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，无需使用单独的或作用域生存期（至少对于内置实现而言）。</span><span class="sxs-lookup"><span data-stu-id="b7b76-197">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="b7b76-198">你还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不必使用 DI，而是使用代码创建一个实例，从而使代码更难以测试，并违反了[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="b7b76-198">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="b7b76-199">建议</span><span class="sxs-lookup"><span data-stu-id="b7b76-199">Recommendations</span></span>

<span data-ttu-id="b7b76-200">确定最适合你的应用的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="b7b76-200">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="b7b76-201">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="b7b76-201">Existing infrastructure</span></span>
* <span data-ttu-id="b7b76-202">性能要求</span><span class="sxs-lookup"><span data-stu-id="b7b76-202">Performance requirements</span></span>
* <span data-ttu-id="b7b76-203">成本</span><span class="sxs-lookup"><span data-stu-id="b7b76-203">Cost</span></span>
* <span data-ttu-id="b7b76-204">团队体验</span><span class="sxs-lookup"><span data-stu-id="b7b76-204">Team experience</span></span>

<span data-ttu-id="b7b76-205">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="b7b76-205">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="b7b76-206">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="b7b76-206">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="b7b76-207">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="b7b76-207">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="b7b76-208">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="b7b76-208">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="b7b76-209">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="b7b76-209">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="b7b76-210">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="b7b76-210">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b7b76-211">其他资源</span><span class="sxs-lookup"><span data-stu-id="b7b76-211">Additional resources</span></span>

* [<span data-ttu-id="b7b76-212">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="b7b76-212">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="b7b76-213">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="b7b76-213">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="b7b76-214">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="b7b76-214">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
