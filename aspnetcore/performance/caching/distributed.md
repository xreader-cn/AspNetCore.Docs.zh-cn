---
title: ASP.NET Core 中的分布式缓存
author: rick-anderson
description: 了解如何使用 ASP.NET Core 分布式缓存来改善应用程序的性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
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
uid: performance/caching/distributed
ms.openlocfilehash: 64a4b6f606a4f5f8e73ef08f53cbb6e4003245aa
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020673"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="2494c-103">ASP.NET Core 中的分布式缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="2494c-104">作者： [Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2494c-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="2494c-105">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="2494c-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="2494c-106">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="2494c-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="2494c-107">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="2494c-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="2494c-108">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="2494c-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="2494c-109"> (一致性) 跨多个*服务器的请求*。</span><span class="sxs-lookup"><span data-stu-id="2494c-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="2494c-110">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="2494c-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="2494c-111">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="2494c-111">Doesn't use local memory.</span></span>

<span data-ttu-id="2494c-112">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="2494c-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="2494c-113">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="2494c-114">第三方实现也可用，例如[GitHub 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="2494c-115">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="2494c-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="2494c-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2494c-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2494c-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="2494c-117">Prerequisites</span></span>

<span data-ttu-id="2494c-118">若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="2494c-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="2494c-119">若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。</span><span class="sxs-lookup"><span data-stu-id="2494c-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="2494c-120">若要使用 NCache 分布式缓存，请将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包。</span><span class="sxs-lookup"><span data-stu-id="2494c-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="2494c-121">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="2494c-121">IDistributedCache interface</span></span>

<span data-ttu-id="2494c-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="2494c-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="2494c-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="2494c-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="2494c-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项 (作为 `byte[]` 数组) 添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="2494c-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：基于其键刷新缓存中的项，如果有任何) ，则重置其可调过期超时 (。</span><span class="sxs-lookup"><span data-stu-id="2494c-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="2494c-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="2494c-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="2494c-127">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="2494c-127">Establish distributed caching services</span></span>

<span data-ttu-id="2494c-128">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2494c-129">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="2494c-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="2494c-130">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="2494c-131">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="2494c-132">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="2494c-133">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="2494c-134">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-134">Distributed Memory Cache</span></span>

<span data-ttu-id="2494c-135">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="2494c-136">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="2494c-137">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="2494c-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="2494c-138">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="2494c-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="2494c-139">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="2494c-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="2494c-140">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="2494c-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="2494c-141">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="2494c-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="2494c-142">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="2494c-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="2494c-143">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="2494c-144">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="2494c-145">分布式 SQL Server 缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="2494c-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="2494c-146">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="2494c-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="2494c-147">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="2494c-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="2494c-148">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="2494c-149">提供 SQL Server 实例 (`Data Source`) 、数据库 (`Initial Catalog`) 、架构 (例如) `dbo` 和表名称 (例如 `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="2494c-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="2494c-150">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="2494c-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="2494c-151">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="2494c-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="2494c-153">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="2494c-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="2494c-154">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="2494c-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (和（可选） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理的外部 (例如，由[机密管理器](xref:security/app-secrets)存储，或在 appsettings 的*appsettings.js*中存储 / *。 {环境} json*文件) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="2494c-156">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="2494c-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="2494c-157">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-157">Distributed Redis Cache</span></span>

<span data-ttu-id="2494c-158">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="2494c-159">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="2494c-160">应用使用 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 中的非开发环境中的实例 () 配置缓存实现 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="2494c-161">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="2494c-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="2494c-162">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="2494c-163">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="2494c-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="2494c-164">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-164">Distributed NCache Cache</span></span>

<span data-ttu-id="2494c-165">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="2494c-166">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2494c-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="2494c-167">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="2494c-168">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="2494c-168">To configure NCache:</span></span>

1. <span data-ttu-id="2494c-169">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="2494c-170">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="2494c-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="2494c-171">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="2494c-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="2494c-172">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-172">Use the distributed cache</span></span>

<span data-ttu-id="2494c-173">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="2494c-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="2494c-174">实例由[ (DI) 的依赖关系注入](xref:fundamentals/dependency-injection)提供。</span><span class="sxs-lookup"><span data-stu-id="2494c-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2494c-175">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="2494c-176">使用 (缓存当前时间 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 有关详细信息，请参阅[泛型主机： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)) ：</span><span class="sxs-lookup"><span data-stu-id="2494c-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="2494c-177">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="2494c-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="2494c-178">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="2494c-179">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="2494c-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="2494c-180">如果自上一次 () 加载缓存时间之后经过了20秒的时间，则页面显示*缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="2494c-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="2494c-181">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="2494c-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="2494c-182">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="2494c-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="2494c-183">对于实例，无需使用实例的单一实例或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (至少为内置实现) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="2494c-184">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="2494c-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="2494c-185">建议</span><span class="sxs-lookup"><span data-stu-id="2494c-185">Recommendations</span></span>

<span data-ttu-id="2494c-186">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="2494c-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="2494c-187">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="2494c-187">Existing infrastructure</span></span>
* <span data-ttu-id="2494c-188">性能要求</span><span class="sxs-lookup"><span data-stu-id="2494c-188">Performance requirements</span></span>
* <span data-ttu-id="2494c-189">节约成本</span><span class="sxs-lookup"><span data-stu-id="2494c-189">Cost</span></span>
* <span data-ttu-id="2494c-190">团队体验</span><span class="sxs-lookup"><span data-stu-id="2494c-190">Team experience</span></span>

<span data-ttu-id="2494c-191">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="2494c-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="2494c-192">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="2494c-193">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="2494c-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="2494c-194">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="2494c-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="2494c-195">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="2494c-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="2494c-196">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="2494c-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2494c-197">其他资源</span><span class="sxs-lookup"><span data-stu-id="2494c-197">Additional resources</span></span>

* [<span data-ttu-id="2494c-198">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="2494c-199">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="2494c-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="2494c-200">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache 在 GitHub 上](https://github.com/Alachisoft/NCache)) </span><span class="sxs-lookup"><span data-stu-id="2494c-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="2494c-201">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="2494c-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="2494c-202">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="2494c-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="2494c-203">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="2494c-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="2494c-204">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="2494c-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="2494c-205"> (一致性) 跨多个*服务器的请求*。</span><span class="sxs-lookup"><span data-stu-id="2494c-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="2494c-206">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="2494c-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="2494c-207">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="2494c-207">Doesn't use local memory.</span></span>

<span data-ttu-id="2494c-208">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="2494c-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="2494c-209">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="2494c-210">第三方实现也可用，例如[GitHub 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="2494c-211">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="2494c-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="2494c-212">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2494c-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2494c-213">先决条件</span><span class="sxs-lookup"><span data-stu-id="2494c-213">Prerequisites</span></span>

<span data-ttu-id="2494c-214">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="2494c-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="2494c-215">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="2494c-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="2494c-216">Redis 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="2494c-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="2494c-217">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="2494c-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="2494c-218">NCache 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="2494c-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="2494c-219">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="2494c-219">IDistributedCache interface</span></span>

<span data-ttu-id="2494c-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="2494c-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="2494c-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="2494c-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="2494c-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项 (作为 `byte[]` 数组) 添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="2494c-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：基于其键刷新缓存中的项，如果有任何) ，则重置其可调过期超时 (。</span><span class="sxs-lookup"><span data-stu-id="2494c-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="2494c-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="2494c-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="2494c-225">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="2494c-225">Establish distributed caching services</span></span>

<span data-ttu-id="2494c-226">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2494c-227">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="2494c-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="2494c-228">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="2494c-229">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="2494c-230">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="2494c-231">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="2494c-232">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-232">Distributed Memory Cache</span></span>

<span data-ttu-id="2494c-233">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="2494c-234">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="2494c-235">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="2494c-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="2494c-236">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="2494c-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="2494c-237">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="2494c-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="2494c-238">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="2494c-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="2494c-239">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="2494c-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="2494c-240">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="2494c-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="2494c-241">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="2494c-242">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="2494c-243">分布式 SQL Server 缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="2494c-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="2494c-244">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="2494c-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="2494c-245">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="2494c-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="2494c-246">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="2494c-247">提供 SQL Server 实例 (`Data Source`) 、数据库 (`Initial Catalog`) 、架构 (例如) `dbo` 和表名称 (例如 `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="2494c-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="2494c-248">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="2494c-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="2494c-249">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="2494c-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="2494c-251">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="2494c-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="2494c-252">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="2494c-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (和（可选） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理的外部 (例如，由[机密管理器](xref:security/app-secrets)存储，或在 appsettings 的*appsettings.js*中存储 / *。 {环境} json*文件) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="2494c-254">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="2494c-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="2494c-255">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-255">Distributed Redis Cache</span></span>

<span data-ttu-id="2494c-256">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="2494c-257">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="2494c-258">应用使用 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 中的非开发环境中的实例 () 配置缓存实现 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="2494c-259">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="2494c-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="2494c-260">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="2494c-261">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="2494c-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="2494c-262">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-262">Distributed NCache Cache</span></span>

<span data-ttu-id="2494c-263">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="2494c-264">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2494c-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="2494c-265">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="2494c-266">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="2494c-266">To configure NCache:</span></span>

1. <span data-ttu-id="2494c-267">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="2494c-268">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="2494c-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="2494c-269">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="2494c-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="2494c-270">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-270">Use the distributed cache</span></span>

<span data-ttu-id="2494c-271">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="2494c-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="2494c-272">实例由[ (DI) 的依赖关系注入](xref:fundamentals/dependency-injection)提供。</span><span class="sxs-lookup"><span data-stu-id="2494c-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2494c-273">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="2494c-274">使用 (缓存当前时间 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 有关详细信息，请参阅[Web 主机： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：</span><span class="sxs-lookup"><span data-stu-id="2494c-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="2494c-275">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="2494c-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="2494c-276">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="2494c-277">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="2494c-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="2494c-278">如果自上一次 () 加载缓存时间之后经过了20秒的时间，则页面显示*缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="2494c-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="2494c-279">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="2494c-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="2494c-280">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="2494c-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="2494c-281">对于实例，无需使用实例的单一实例或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (至少为内置实现) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="2494c-282">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="2494c-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="2494c-283">建议</span><span class="sxs-lookup"><span data-stu-id="2494c-283">Recommendations</span></span>

<span data-ttu-id="2494c-284">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="2494c-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="2494c-285">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="2494c-285">Existing infrastructure</span></span>
* <span data-ttu-id="2494c-286">性能要求</span><span class="sxs-lookup"><span data-stu-id="2494c-286">Performance requirements</span></span>
* <span data-ttu-id="2494c-287">节约成本</span><span class="sxs-lookup"><span data-stu-id="2494c-287">Cost</span></span>
* <span data-ttu-id="2494c-288">团队体验</span><span class="sxs-lookup"><span data-stu-id="2494c-288">Team experience</span></span>

<span data-ttu-id="2494c-289">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="2494c-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="2494c-290">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="2494c-291">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="2494c-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="2494c-292">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="2494c-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="2494c-293">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="2494c-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="2494c-294">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="2494c-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2494c-295">其他资源</span><span class="sxs-lookup"><span data-stu-id="2494c-295">Additional resources</span></span>

* [<span data-ttu-id="2494c-296">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="2494c-297">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="2494c-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="2494c-298">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache 在 GitHub 上](https://github.com/Alachisoft/NCache)) </span><span class="sxs-lookup"><span data-stu-id="2494c-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="2494c-299">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="2494c-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="2494c-300">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="2494c-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="2494c-301">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="2494c-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="2494c-302">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="2494c-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="2494c-303"> (一致性) 跨多个*服务器的请求*。</span><span class="sxs-lookup"><span data-stu-id="2494c-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="2494c-304">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="2494c-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="2494c-305">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="2494c-305">Doesn't use local memory.</span></span>

<span data-ttu-id="2494c-306">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="2494c-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="2494c-307">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="2494c-308">第三方实现也可用，例如[GitHub 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="2494c-309">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="2494c-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="2494c-310">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="2494c-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2494c-311">先决条件</span><span class="sxs-lookup"><span data-stu-id="2494c-311">Prerequisites</span></span>

<span data-ttu-id="2494c-312">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="2494c-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="2494c-313">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="2494c-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="2494c-314">Redis 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="2494c-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="2494c-315">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="2494c-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="2494c-316">NCache 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="2494c-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="2494c-317">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="2494c-317">IDistributedCache interface</span></span>

<span data-ttu-id="2494c-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="2494c-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="2494c-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="2494c-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="2494c-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项 (作为 `byte[]` 数组) 添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="2494c-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：基于其键刷新缓存中的项，如果有任何) ，则重置其可调过期超时 (。</span><span class="sxs-lookup"><span data-stu-id="2494c-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="2494c-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="2494c-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="2494c-323">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="2494c-323">Establish distributed caching services</span></span>

<span data-ttu-id="2494c-324">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="2494c-325">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="2494c-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="2494c-326">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="2494c-327">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="2494c-328">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="2494c-329">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="2494c-330">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-330">Distributed Memory Cache</span></span>

<span data-ttu-id="2494c-331">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="2494c-332">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="2494c-333">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="2494c-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="2494c-334">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="2494c-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="2494c-335">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="2494c-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="2494c-336">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="2494c-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="2494c-337">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="2494c-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="2494c-338">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="2494c-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="2494c-339">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="2494c-340">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="2494c-341">分布式 SQL Server 缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="2494c-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="2494c-342">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="2494c-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="2494c-343">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="2494c-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="2494c-344">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="2494c-345">提供 SQL Server 实例 (`Data Source`) 、数据库 (`Initial Catalog`) 、架构 (例如) `dbo` 和表名称 (例如 `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="2494c-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="2494c-346">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="2494c-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="2494c-347">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="2494c-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="2494c-349">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="2494c-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="2494c-350">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="2494c-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="2494c-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (和（可选） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理的外部 (例如，由[机密管理器](xref:security/app-secrets)存储，或在 appsettings 的*appsettings.js*中存储 / *。 {环境} json*文件) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="2494c-352">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="2494c-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="2494c-353">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-353">Distributed Redis Cache</span></span>

<span data-ttu-id="2494c-354">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="2494c-355">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="2494c-356">应用使用实例) 配置缓存实现 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ：</span><span class="sxs-lookup"><span data-stu-id="2494c-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="2494c-357">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="2494c-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="2494c-358">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="2494c-359">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="2494c-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="2494c-360">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-360">Distributed NCache Cache</span></span>

<span data-ttu-id="2494c-361">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="2494c-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="2494c-362">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="2494c-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="2494c-363">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="2494c-364">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="2494c-364">To configure NCache:</span></span>

1. <span data-ttu-id="2494c-365">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="2494c-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="2494c-366">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="2494c-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="2494c-367">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="2494c-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="2494c-368">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-368">Use the distributed cache</span></span>

<span data-ttu-id="2494c-369">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="2494c-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="2494c-370">实例由[ (DI) 的依赖关系注入](xref:fundamentals/dependency-injection)提供。</span><span class="sxs-lookup"><span data-stu-id="2494c-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="2494c-371">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="2494c-372">使用 (缓存当前时间 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 有关详细信息，请参阅[Web 主机： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：</span><span class="sxs-lookup"><span data-stu-id="2494c-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="2494c-373">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="2494c-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="2494c-374">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="2494c-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="2494c-375">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="2494c-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="2494c-376">如果自上一次 () 加载缓存时间之后经过了20秒的时间，则页面显示*缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="2494c-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="2494c-377">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="2494c-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="2494c-378">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="2494c-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="2494c-379">对于实例，无需使用实例的单一实例或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (至少为内置实现) 。</span><span class="sxs-lookup"><span data-stu-id="2494c-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="2494c-380">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="2494c-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="2494c-381">建议</span><span class="sxs-lookup"><span data-stu-id="2494c-381">Recommendations</span></span>

<span data-ttu-id="2494c-382">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="2494c-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="2494c-383">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="2494c-383">Existing infrastructure</span></span>
* <span data-ttu-id="2494c-384">性能要求</span><span class="sxs-lookup"><span data-stu-id="2494c-384">Performance requirements</span></span>
* <span data-ttu-id="2494c-385">节约成本</span><span class="sxs-lookup"><span data-stu-id="2494c-385">Cost</span></span>
* <span data-ttu-id="2494c-386">团队体验</span><span class="sxs-lookup"><span data-stu-id="2494c-386">Team experience</span></span>

<span data-ttu-id="2494c-387">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="2494c-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="2494c-388">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="2494c-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="2494c-389">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="2494c-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="2494c-390">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="2494c-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="2494c-391">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="2494c-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="2494c-392">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="2494c-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2494c-393">其他资源</span><span class="sxs-lookup"><span data-stu-id="2494c-393">Additional resources</span></span>

* [<span data-ttu-id="2494c-394">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="2494c-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="2494c-395">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="2494c-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="2494c-396">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache 在 GitHub 上](https://github.com/Alachisoft/NCache)) </span><span class="sxs-lookup"><span data-stu-id="2494c-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 