---
title: ASP.NET Core 中的分布式缓存
author: rick-anderson
description: 了解如何使用 ASP.NET Core 分布式缓存来改善应用程序的性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: performance/caching/distributed
ms.openlocfilehash: 206ff55aa530cd06c162e49f400b436e9fb9f07a
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775292"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="832f3-103">ASP.NET Core 中的分布式缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="832f3-104">作者： [Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="832f3-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="832f3-105">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="832f3-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="832f3-106">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="832f3-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="832f3-107">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="832f3-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="832f3-108">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="832f3-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="832f3-109">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="832f3-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="832f3-110">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="832f3-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="832f3-111">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="832f3-111">Doesn't use local memory.</span></span>

<span data-ttu-id="832f3-112">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="832f3-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="832f3-113">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="832f3-114">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="832f3-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="832f3-115">无论选择哪种实现，应用都会使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="832f3-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="832f3-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="832f3-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="832f3-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="832f3-117">Prerequisites</span></span>

<span data-ttu-id="832f3-118">若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="832f3-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="832f3-119">若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。</span><span class="sxs-lookup"><span data-stu-id="832f3-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="832f3-120">若要使用 NCache 分布式缓存，请将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包。</span><span class="sxs-lookup"><span data-stu-id="832f3-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="832f3-121">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="832f3-121">IDistributedCache interface</span></span>

<span data-ttu-id="832f3-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="832f3-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="832f3-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;接受字符串键，并在缓存中找到缓存项`byte[]`作为数组。</span><span class="sxs-lookup"><span data-stu-id="832f3-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="832f3-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;使用字符串键将项`byte[]` （作为数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="832f3-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash;基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="832f3-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="832f3-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;基于其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="832f3-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="832f3-127">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="832f3-127">Establish distributed caching services</span></span>

<span data-ttu-id="832f3-128">在中`Startup.ConfigureServices`注册的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现。</span><span class="sxs-lookup"><span data-stu-id="832f3-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="832f3-129">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="832f3-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="832f3-130">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="832f3-131">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="832f3-132">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="832f3-133">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="832f3-134">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-134">Distributed Memory Cache</span></span>

<span data-ttu-id="832f3-135">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="832f3-136">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="832f3-137">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="832f3-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="832f3-138">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="832f3-139">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="832f3-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="832f3-140">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="832f3-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="832f3-141">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="832f3-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="832f3-142">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="832f3-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="832f3-143">当应用程序在的开发环境中`Startup.ConfigureServices`运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="832f3-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="832f3-144">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="832f3-145">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="832f3-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="832f3-146">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用`sql-cache`工具。</span><span class="sxs-lookup"><span data-stu-id="832f3-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="832f3-147">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="832f3-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="832f3-148">通过运行`sql-cache create`命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="832f3-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="832f3-149">提供 SQL Server 实例`Data Source`（）、数据库（`Initial Catalog`）、架构（例如， `dbo`）和表名（例如`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="832f3-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="832f3-150">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="832f3-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="832f3-151">该`sql-cache`工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="832f3-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="832f3-153">应用应使用的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例（而不是<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>）来处理缓存值。</span><span class="sxs-lookup"><span data-stu-id="832f3-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="832f3-154">示例应用在中<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`的非开发环境中实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="832f3-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （以及（可选<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*/appsettings 中存储）*。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="832f3-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="832f3-156">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="832f3-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="832f3-157">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-157">Distributed Redis Cache</span></span>

<span data-ttu-id="832f3-158">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="832f3-159">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="832f3-160">应用使用中<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> `Startup.ConfigureServices`的非开发环境中的实例<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>（）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="832f3-161">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="832f3-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="832f3-162">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="832f3-163">在`redis-server`命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="832f3-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="832f3-164">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-164">Distributed NCache Cache</span></span>

<span data-ttu-id="832f3-165">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="832f3-166">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="832f3-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="832f3-167">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="832f3-168">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="832f3-168">To configure NCache:</span></span>

1. <span data-ttu-id="832f3-169">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="832f3-170">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="832f3-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="832f3-171">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="832f3-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="832f3-172">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-172">Use the distributed cache</span></span>

<span data-ttu-id="832f3-173">若要使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口，请从应用程序<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="832f3-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="832f3-174">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="832f3-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="832f3-175">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>将插入到中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="832f3-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="832f3-176">使用<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>缓存当前时间（有关详细信息，请参阅[泛型 Host： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)）：</span><span class="sxs-lookup"><span data-stu-id="832f3-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="832f3-177">示例应用将注入<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>到中`IndexModel`供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="832f3-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="832f3-178">每次加载索引页时，都会在中`OnGetAsync`检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="832f3-179">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="832f3-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="832f3-180">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="832f3-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="832f3-181">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="832f3-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="832f3-182">按钮触发`OnPostResetCachedTime`处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="832f3-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="832f3-183">对于实例， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>无需使用单独的或作用域生存期（至少对于内置实现）。</span><span class="sxs-lookup"><span data-stu-id="832f3-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="832f3-184">您还可以创建一个<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="832f3-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="832f3-185">建议</span><span class="sxs-lookup"><span data-stu-id="832f3-185">Recommendations</span></span>

<span data-ttu-id="832f3-186">确定最适合你的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="832f3-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="832f3-187">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="832f3-187">Existing infrastructure</span></span>
* <span data-ttu-id="832f3-188">性能要求</span><span class="sxs-lookup"><span data-stu-id="832f3-188">Performance requirements</span></span>
* <span data-ttu-id="832f3-189">开销</span><span class="sxs-lookup"><span data-stu-id="832f3-189">Cost</span></span>
* <span data-ttu-id="832f3-190">团队体验</span><span class="sxs-lookup"><span data-stu-id="832f3-190">Team experience</span></span>

<span data-ttu-id="832f3-191">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="832f3-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="832f3-192">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="832f3-193">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="832f3-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="832f3-194">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="832f3-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="832f3-195">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="832f3-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="832f3-196">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="832f3-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="832f3-197">其他资源</span><span class="sxs-lookup"><span data-stu-id="832f3-197">Additional resources</span></span>

* [<span data-ttu-id="832f3-198">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="832f3-199">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="832f3-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="832f3-200">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="832f3-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="832f3-201">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="832f3-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="832f3-202">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="832f3-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="832f3-203">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="832f3-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="832f3-204">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="832f3-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="832f3-205">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="832f3-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="832f3-206">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="832f3-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="832f3-207">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="832f3-207">Doesn't use local memory.</span></span>

<span data-ttu-id="832f3-208">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="832f3-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="832f3-209">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="832f3-210">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="832f3-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="832f3-211">无论选择哪种实现，应用都会使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="832f3-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="832f3-212">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="832f3-212">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="832f3-213">先决条件</span><span class="sxs-lookup"><span data-stu-id="832f3-213">Prerequisites</span></span>

<span data-ttu-id="832f3-214">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="832f3-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="832f3-215">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="832f3-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="832f3-216">Redis 包不包含在`Microsoft.AspNetCore.App`包中，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="832f3-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="832f3-217">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="832f3-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="832f3-218">NCache 包不包含在`Microsoft.AspNetCore.App`包中，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="832f3-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="832f3-219">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="832f3-219">IDistributedCache interface</span></span>

<span data-ttu-id="832f3-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="832f3-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="832f3-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;接受字符串键，并在缓存中找到缓存项`byte[]`作为数组。</span><span class="sxs-lookup"><span data-stu-id="832f3-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="832f3-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;使用字符串键将项`byte[]` （作为数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="832f3-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash;基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="832f3-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="832f3-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;基于其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="832f3-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="832f3-225">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="832f3-225">Establish distributed caching services</span></span>

<span data-ttu-id="832f3-226">在中`Startup.ConfigureServices`注册的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现。</span><span class="sxs-lookup"><span data-stu-id="832f3-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="832f3-227">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="832f3-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="832f3-228">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="832f3-229">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="832f3-230">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="832f3-231">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="832f3-232">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-232">Distributed Memory Cache</span></span>

<span data-ttu-id="832f3-233">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="832f3-234">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="832f3-235">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="832f3-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="832f3-236">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="832f3-237">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="832f3-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="832f3-238">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="832f3-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="832f3-239">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="832f3-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="832f3-240">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="832f3-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="832f3-241">当应用程序在的开发环境中`Startup.ConfigureServices`运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="832f3-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="832f3-242">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="832f3-243">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="832f3-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="832f3-244">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用`sql-cache`工具。</span><span class="sxs-lookup"><span data-stu-id="832f3-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="832f3-245">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="832f3-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="832f3-246">通过运行`sql-cache create`命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="832f3-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="832f3-247">提供 SQL Server 实例`Data Source`（）、数据库（`Initial Catalog`）、架构（例如， `dbo`）和表名（例如`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="832f3-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="832f3-248">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="832f3-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="832f3-249">该`sql-cache`工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="832f3-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="832f3-251">应用应使用的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例（而不是<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>）来处理缓存值。</span><span class="sxs-lookup"><span data-stu-id="832f3-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="832f3-252">示例应用在中<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`的非开发环境中实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="832f3-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （以及（可选<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*/appsettings 中存储）*。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="832f3-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="832f3-254">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="832f3-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="832f3-255">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-255">Distributed Redis Cache</span></span>

<span data-ttu-id="832f3-256">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="832f3-257">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="832f3-258">应用使用中<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> `Startup.ConfigureServices`的非开发环境中的实例<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>（）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="832f3-259">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="832f3-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="832f3-260">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="832f3-261">在`redis-server`命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="832f3-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="832f3-262">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-262">Distributed NCache Cache</span></span>

<span data-ttu-id="832f3-263">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="832f3-264">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="832f3-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="832f3-265">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="832f3-266">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="832f3-266">To configure NCache:</span></span>

1. <span data-ttu-id="832f3-267">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="832f3-268">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="832f3-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="832f3-269">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="832f3-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="832f3-270">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-270">Use the distributed cache</span></span>

<span data-ttu-id="832f3-271">若要使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口，请从应用程序<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="832f3-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="832f3-272">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="832f3-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="832f3-273">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>将插入到中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="832f3-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="832f3-274">使用<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>缓存当前时间（有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="832f3-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="832f3-275">示例应用将注入<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>到中`IndexModel`供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="832f3-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="832f3-276">每次加载索引页时，都会在中`OnGetAsync`检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="832f3-277">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="832f3-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="832f3-278">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="832f3-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="832f3-279">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="832f3-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="832f3-280">按钮触发`OnPostResetCachedTime`处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="832f3-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="832f3-281">对于实例， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>无需使用单独的或作用域生存期（至少对于内置实现）。</span><span class="sxs-lookup"><span data-stu-id="832f3-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="832f3-282">您还可以创建一个<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="832f3-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="832f3-283">建议</span><span class="sxs-lookup"><span data-stu-id="832f3-283">Recommendations</span></span>

<span data-ttu-id="832f3-284">确定最适合你的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="832f3-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="832f3-285">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="832f3-285">Existing infrastructure</span></span>
* <span data-ttu-id="832f3-286">性能要求</span><span class="sxs-lookup"><span data-stu-id="832f3-286">Performance requirements</span></span>
* <span data-ttu-id="832f3-287">开销</span><span class="sxs-lookup"><span data-stu-id="832f3-287">Cost</span></span>
* <span data-ttu-id="832f3-288">团队体验</span><span class="sxs-lookup"><span data-stu-id="832f3-288">Team experience</span></span>

<span data-ttu-id="832f3-289">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="832f3-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="832f3-290">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="832f3-291">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="832f3-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="832f3-292">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="832f3-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="832f3-293">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="832f3-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="832f3-294">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="832f3-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="832f3-295">其他资源</span><span class="sxs-lookup"><span data-stu-id="832f3-295">Additional resources</span></span>

* [<span data-ttu-id="832f3-296">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="832f3-297">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="832f3-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="832f3-298">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="832f3-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="832f3-299">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="832f3-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="832f3-300">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="832f3-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="832f3-301">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="832f3-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="832f3-302">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="832f3-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="832f3-303">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="832f3-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="832f3-304">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="832f3-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="832f3-305">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="832f3-305">Doesn't use local memory.</span></span>

<span data-ttu-id="832f3-306">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="832f3-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="832f3-307">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="832f3-308">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="832f3-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="832f3-309">无论选择哪种实现，应用都会使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="832f3-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="832f3-310">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="832f3-310">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="832f3-311">先决条件</span><span class="sxs-lookup"><span data-stu-id="832f3-311">Prerequisites</span></span>

<span data-ttu-id="832f3-312">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="832f3-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="832f3-313">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="832f3-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="832f3-314">Redis 包不包含在`Microsoft.AspNetCore.App`包中，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="832f3-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="832f3-315">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="832f3-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="832f3-316">NCache 包不包含在`Microsoft.AspNetCore.App`包中，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="832f3-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="832f3-317">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="832f3-317">IDistributedCache interface</span></span>

<span data-ttu-id="832f3-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="832f3-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="832f3-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;接受字符串键，并在缓存中找到缓存项`byte[]`作为数组。</span><span class="sxs-lookup"><span data-stu-id="832f3-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="832f3-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;使用字符串键将项`byte[]` （作为数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="832f3-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash;基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="832f3-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="832f3-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;基于其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="832f3-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="832f3-323">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="832f3-323">Establish distributed caching services</span></span>

<span data-ttu-id="832f3-324">在中`Startup.ConfigureServices`注册的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现。</span><span class="sxs-lookup"><span data-stu-id="832f3-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="832f3-325">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="832f3-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="832f3-326">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="832f3-327">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="832f3-328">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="832f3-329">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="832f3-330">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-330">Distributed Memory Cache</span></span>

<span data-ttu-id="832f3-331">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="832f3-332">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="832f3-333">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="832f3-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="832f3-334">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="832f3-335">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="832f3-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="832f3-336">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="832f3-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="832f3-337">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="832f3-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="832f3-338">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="832f3-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="832f3-339">当应用程序在的开发环境中`Startup.ConfigureServices`运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="832f3-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="832f3-340">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="832f3-341">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="832f3-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="832f3-342">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用`sql-cache`工具。</span><span class="sxs-lookup"><span data-stu-id="832f3-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="832f3-343">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="832f3-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="832f3-344">通过运行`sql-cache create`命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="832f3-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="832f3-345">提供 SQL Server 实例`Data Source`（）、数据库（`Initial Catalog`）、架构（例如， `dbo`）和表名（例如`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="832f3-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="832f3-346">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="832f3-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="832f3-347">该`sql-cache`工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="832f3-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="832f3-349">应用应使用的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例（而不是<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>）来处理缓存值。</span><span class="sxs-lookup"><span data-stu-id="832f3-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="832f3-350">示例应用在中<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`的非开发环境中实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="832f3-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （以及（可选<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*/appsettings 中存储）*。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="832f3-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="832f3-352">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="832f3-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="832f3-353">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-353">Distributed Redis Cache</span></span>

<span data-ttu-id="832f3-354">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="832f3-355">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="832f3-356">应用使用<xref:Microsoft.Extensions.Caching.Redis.RedisCache>实例（<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="832f3-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="832f3-357">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="832f3-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="832f3-358">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="832f3-359">在`redis-server`命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="832f3-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="832f3-360">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-360">Distributed NCache Cache</span></span>

<span data-ttu-id="832f3-361">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="832f3-362">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="832f3-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="832f3-363">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="832f3-364">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="832f3-364">To configure NCache:</span></span>

1. <span data-ttu-id="832f3-365">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="832f3-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="832f3-366">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="832f3-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="832f3-367">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="832f3-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="832f3-368">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-368">Use the distributed cache</span></span>

<span data-ttu-id="832f3-369">若要使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口，请从应用程序<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="832f3-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="832f3-370">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="832f3-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="832f3-371">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>将插入到中`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="832f3-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="832f3-372">使用<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>缓存当前时间（有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="832f3-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="832f3-373">示例应用将注入<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>到中`IndexModel`供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="832f3-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="832f3-374">每次加载索引页时，都会在中`OnGetAsync`检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="832f3-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="832f3-375">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="832f3-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="832f3-376">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="832f3-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="832f3-377">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="832f3-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="832f3-378">按钮触发`OnPostResetCachedTime`处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="832f3-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="832f3-379">对于实例， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>无需使用单独的或作用域生存期（至少对于内置实现）。</span><span class="sxs-lookup"><span data-stu-id="832f3-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="832f3-380">您还可以创建一个<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="832f3-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="832f3-381">建议</span><span class="sxs-lookup"><span data-stu-id="832f3-381">Recommendations</span></span>

<span data-ttu-id="832f3-382">确定最适合你的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="832f3-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="832f3-383">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="832f3-383">Existing infrastructure</span></span>
* <span data-ttu-id="832f3-384">性能要求</span><span class="sxs-lookup"><span data-stu-id="832f3-384">Performance requirements</span></span>
* <span data-ttu-id="832f3-385">开销</span><span class="sxs-lookup"><span data-stu-id="832f3-385">Cost</span></span>
* <span data-ttu-id="832f3-386">团队体验</span><span class="sxs-lookup"><span data-stu-id="832f3-386">Team experience</span></span>

<span data-ttu-id="832f3-387">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="832f3-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="832f3-388">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="832f3-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="832f3-389">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="832f3-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="832f3-390">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="832f3-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="832f3-391">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="832f3-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="832f3-392">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="832f3-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="832f3-393">其他资源</span><span class="sxs-lookup"><span data-stu-id="832f3-393">Additional resources</span></span>

* [<span data-ttu-id="832f3-394">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="832f3-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="832f3-395">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="832f3-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="832f3-396">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="832f3-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 