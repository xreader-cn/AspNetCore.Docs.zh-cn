---
title: ASP.NET Core 中的分布式缓存
author: guardrex
description: 了解如何使用 ASP.NET Core 分布式缓存来改善应用程序的性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
uid: performance/caching/distributed
ms.openlocfilehash: d39ac6c7496de7cf9dc8d40718bbaf611e744c19
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/10/2020
ms.locfileid: "77114739"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="88857-103">ASP.NET Core 中的分布式缓存</span><span class="sxs-lookup"><span data-stu-id="88857-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="88857-104">作者： [Luke Latham](https://github.com/guardrex)、 [Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="88857-104">By [Luke Latham](https://github.com/guardrex), [Mohsin Nasir](https://github.com/mohsinnasir), and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="88857-105">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="88857-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="88857-106">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="88857-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="88857-107">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="88857-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="88857-108">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="88857-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="88857-109">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="88857-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="88857-110">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="88857-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="88857-111">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="88857-111">Doesn't use local memory.</span></span>

<span data-ttu-id="88857-112">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="88857-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="88857-113">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="88857-114">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="88857-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="88857-115">无论选择哪种实现，应用都使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="88857-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="88857-116">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="88857-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="88857-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="88857-117">Prerequisites</span></span>

<span data-ttu-id="88857-118">若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="88857-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="88857-119">若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。</span><span class="sxs-lookup"><span data-stu-id="88857-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="88857-120">若要使用 NCache 分布式缓存，请将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包。</span><span class="sxs-lookup"><span data-stu-id="88857-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="88857-121">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="88857-121">IDistributedCache interface</span></span>

<span data-ttu-id="88857-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="88857-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="88857-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="88857-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="88857-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="88857-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="88857-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>中，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="88857-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="88857-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 会根据其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="88857-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="88857-127">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="88857-127">Establish distributed caching services</span></span>

<span data-ttu-id="88857-128">在 `Startup.ConfigureServices`中注册 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现。</span><span class="sxs-lookup"><span data-stu-id="88857-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88857-129">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="88857-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="88857-130">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="88857-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="88857-131">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="88857-132">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="88857-133">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="88857-134">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="88857-134">Distributed Memory Cache</span></span>

<span data-ttu-id="88857-135">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现，用于将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="88857-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="88857-136">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="88857-137">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="88857-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="88857-138">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="88857-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="88857-139">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="88857-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="88857-140">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="88857-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="88857-141">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="88857-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="88857-142">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="88857-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="88857-143">当应用程序在 `Startup.ConfigureServices`的开发环境中运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="88857-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="88857-144">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="88857-145">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="88857-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="88857-146">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="88857-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="88857-147">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="88857-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="88857-148">通过运行 `sql-cache create` 命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="88857-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="88857-149">提供 SQL Server 实例（`Data Source`）、数据库（`Initial Catalog`）、架构（例如 `dbo`）和表名（例如，`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="88857-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="88857-150">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="88857-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="88857-151">`sql-cache` 工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="88857-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="88857-153">应用应使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>的实例而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>来操作缓存值。</span><span class="sxs-lookup"><span data-stu-id="88857-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="88857-154">示例应用在 `Startup.ConfigureServices`的非开发环境中实施 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>：</span><span class="sxs-lookup"><span data-stu-id="88857-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="88857-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （并且可以选择 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理之外（例如，由[机密管理器](xref:security/app-secrets)存储或在*appsettings*/appsettings 中存储） *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="88857-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="88857-156">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="88857-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="88857-157">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-157">Distributed Redis Cache</span></span>

<span data-ttu-id="88857-158">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="88857-159">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="88857-159">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="88857-160">应用使用 `Startup.ConfigureServices`的非开发环境中的 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="88857-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="88857-161">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="88857-161">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="88857-162">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="88857-162">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="88857-163">在命令提示符下运行 `redis-server`。</span><span class="sxs-lookup"><span data-stu-id="88857-163">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="88857-164">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-164">Distributed NCache Cache</span></span>

<span data-ttu-id="88857-165">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-165">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="88857-166">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="88857-166">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="88857-167">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="88857-167">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="88857-168">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="88857-168">To configure NCache:</span></span>

1. <span data-ttu-id="88857-169">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="88857-169">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="88857-170">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="88857-170">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="88857-171">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="88857-171">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="88857-172">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="88857-172">Use the distributed cache</span></span>

<span data-ttu-id="88857-173">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请从应用程序中的任何构造函数请求 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实例。</span><span class="sxs-lookup"><span data-stu-id="88857-173">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="88857-174">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="88857-174">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="88857-175">示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。</span><span class="sxs-lookup"><span data-stu-id="88857-175">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="88857-176">当前时间使用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 进行缓存（有关详细信息，请参阅[泛型 Host： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)）：</span><span class="sxs-lookup"><span data-stu-id="88857-176">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="88857-177">示例应用将 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 插入到 `IndexModel` 中，供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="88857-177">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="88857-178">每次加载索引页时，都会在 `OnGetAsync`中检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-178">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="88857-179">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="88857-179">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="88857-180">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="88857-180">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="88857-181">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="88857-181">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="88857-182">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="88857-182">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="88857-183">对于 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，无需使用单独的或作用域生存期（至少对于内置实现而言）。</span><span class="sxs-lookup"><span data-stu-id="88857-183">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="88857-184">你还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不必使用 DI，而是使用代码创建一个实例，从而使代码更难以测试，并违反了[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="88857-184">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="88857-185">建议</span><span class="sxs-lookup"><span data-stu-id="88857-185">Recommendations</span></span>

<span data-ttu-id="88857-186">确定最适合你的应用的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="88857-186">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="88857-187">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="88857-187">Existing infrastructure</span></span>
* <span data-ttu-id="88857-188">性能要求</span><span class="sxs-lookup"><span data-stu-id="88857-188">Performance requirements</span></span>
* <span data-ttu-id="88857-189">成本</span><span class="sxs-lookup"><span data-stu-id="88857-189">Cost</span></span>
* <span data-ttu-id="88857-190">团队体验</span><span class="sxs-lookup"><span data-stu-id="88857-190">Team experience</span></span>

<span data-ttu-id="88857-191">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="88857-191">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="88857-192">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="88857-192">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="88857-193">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="88857-193">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="88857-194">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="88857-194">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="88857-195">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="88857-195">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="88857-196">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="88857-196">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88857-197">其他资源</span><span class="sxs-lookup"><span data-stu-id="88857-197">Additional resources</span></span>

* [<span data-ttu-id="88857-198">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-198">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="88857-199">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="88857-199">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="88857-200">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="88857-200">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="88857-201">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="88857-201">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="88857-202">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="88857-202">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="88857-203">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="88857-203">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="88857-204">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="88857-204">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="88857-205">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="88857-205">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="88857-206">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="88857-206">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="88857-207">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="88857-207">Doesn't use local memory.</span></span>

<span data-ttu-id="88857-208">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="88857-208">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="88857-209">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-209">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="88857-210">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="88857-210">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="88857-211">无论选择哪种实现，应用都使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="88857-211">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="88857-212">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="88857-212">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="88857-213">先决条件</span><span class="sxs-lookup"><span data-stu-id="88857-213">Prerequisites</span></span>

<span data-ttu-id="88857-214">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="88857-214">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="88857-215">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="88857-215">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="88857-216">Redis 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="88857-216">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="88857-217">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="88857-217">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="88857-218">NCache 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="88857-218">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="88857-219">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="88857-219">IDistributedCache interface</span></span>

<span data-ttu-id="88857-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="88857-220">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="88857-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="88857-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="88857-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="88857-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="88857-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>中，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="88857-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="88857-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 会根据其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="88857-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="88857-225">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="88857-225">Establish distributed caching services</span></span>

<span data-ttu-id="88857-226">在 `Startup.ConfigureServices`中注册 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现。</span><span class="sxs-lookup"><span data-stu-id="88857-226">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88857-227">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="88857-227">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="88857-228">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="88857-228">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="88857-229">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-229">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="88857-230">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-230">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="88857-231">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-231">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="88857-232">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="88857-232">Distributed Memory Cache</span></span>

<span data-ttu-id="88857-233">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现，用于将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="88857-233">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="88857-234">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-234">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="88857-235">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="88857-235">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="88857-236">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="88857-236">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="88857-237">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="88857-237">In development and testing scenarios.</span></span>
* <span data-ttu-id="88857-238">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="88857-238">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="88857-239">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="88857-239">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="88857-240">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="88857-240">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="88857-241">当应用程序在 `Startup.ConfigureServices`的开发环境中运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="88857-241">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="88857-242">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-242">Distributed SQL Server Cache</span></span>

<span data-ttu-id="88857-243">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="88857-243">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="88857-244">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="88857-244">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="88857-245">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="88857-245">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="88857-246">通过运行 `sql-cache create` 命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="88857-246">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="88857-247">提供 SQL Server 实例（`Data Source`）、数据库（`Initial Catalog`）、架构（例如 `dbo`）和表名（例如，`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="88857-247">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="88857-248">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="88857-248">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="88857-249">`sql-cache` 工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="88857-249">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="88857-251">应用应使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>的实例而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>来操作缓存值。</span><span class="sxs-lookup"><span data-stu-id="88857-251">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="88857-252">示例应用在 `Startup.ConfigureServices`的非开发环境中实施 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>：</span><span class="sxs-lookup"><span data-stu-id="88857-252">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="88857-253"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （并且可以选择 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理之外（例如，由[机密管理器](xref:security/app-secrets)存储或在*appsettings*/appsettings 中存储） *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="88857-253">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="88857-254">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="88857-254">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="88857-255">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-255">Distributed Redis Cache</span></span>

<span data-ttu-id="88857-256">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-256">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="88857-257">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="88857-257">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="88857-258">应用使用 `Startup.ConfigureServices`的非开发环境中的 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="88857-258">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="88857-259">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="88857-259">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="88857-260">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="88857-260">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="88857-261">在命令提示符下运行 `redis-server`。</span><span class="sxs-lookup"><span data-stu-id="88857-261">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="88857-262">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-262">Distributed NCache Cache</span></span>

<span data-ttu-id="88857-263">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-263">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="88857-264">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="88857-264">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="88857-265">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="88857-265">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="88857-266">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="88857-266">To configure NCache:</span></span>

1. <span data-ttu-id="88857-267">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="88857-267">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="88857-268">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="88857-268">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="88857-269">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="88857-269">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="88857-270">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="88857-270">Use the distributed cache</span></span>

<span data-ttu-id="88857-271">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请从应用程序中的任何构造函数请求 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实例。</span><span class="sxs-lookup"><span data-stu-id="88857-271">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="88857-272">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="88857-272">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="88857-273">示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。</span><span class="sxs-lookup"><span data-stu-id="88857-273">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="88857-274">当前时间使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 进行缓存（有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="88857-274">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="88857-275">示例应用将 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 插入到 `IndexModel` 中，供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="88857-275">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="88857-276">每次加载索引页时，都会在 `OnGetAsync`中检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-276">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="88857-277">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="88857-277">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="88857-278">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="88857-278">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="88857-279">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="88857-279">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="88857-280">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="88857-280">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="88857-281">对于 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，无需使用单独的或作用域生存期（至少对于内置实现而言）。</span><span class="sxs-lookup"><span data-stu-id="88857-281">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="88857-282">你还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不必使用 DI，而是使用代码创建一个实例，从而使代码更难以测试，并违反了[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="88857-282">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="88857-283">建议</span><span class="sxs-lookup"><span data-stu-id="88857-283">Recommendations</span></span>

<span data-ttu-id="88857-284">确定最适合你的应用的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="88857-284">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="88857-285">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="88857-285">Existing infrastructure</span></span>
* <span data-ttu-id="88857-286">性能要求</span><span class="sxs-lookup"><span data-stu-id="88857-286">Performance requirements</span></span>
* <span data-ttu-id="88857-287">成本</span><span class="sxs-lookup"><span data-stu-id="88857-287">Cost</span></span>
* <span data-ttu-id="88857-288">团队体验</span><span class="sxs-lookup"><span data-stu-id="88857-288">Team experience</span></span>

<span data-ttu-id="88857-289">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="88857-289">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="88857-290">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="88857-290">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="88857-291">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="88857-291">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="88857-292">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="88857-292">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="88857-293">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="88857-293">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="88857-294">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="88857-294">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88857-295">其他资源</span><span class="sxs-lookup"><span data-stu-id="88857-295">Additional resources</span></span>

* [<span data-ttu-id="88857-296">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-296">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="88857-297">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="88857-297">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="88857-298">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="88857-298">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="88857-299">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="88857-299">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="88857-300">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="88857-300">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="88857-301">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="88857-301">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="88857-302">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="88857-302">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="88857-303">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="88857-303">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="88857-304">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="88857-304">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="88857-305">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="88857-305">Doesn't use local memory.</span></span>

<span data-ttu-id="88857-306">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="88857-306">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="88857-307">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-307">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="88857-308">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="88857-308">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="88857-309">无论选择哪种实现，应用都使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口与缓存交互。</span><span class="sxs-lookup"><span data-stu-id="88857-309">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="88857-310">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="88857-310">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="88857-311">先决条件</span><span class="sxs-lookup"><span data-stu-id="88857-311">Prerequisites</span></span>

<span data-ttu-id="88857-312">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="88857-312">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="88857-313">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="88857-313">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="88857-314">Redis 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="88857-314">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="88857-315">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="88857-315">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="88857-316">NCache 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="88857-316">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="88857-317">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="88857-317">IDistributedCache interface</span></span>

<span data-ttu-id="88857-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="88857-318">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="88857-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="88857-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="88857-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="88857-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="88857-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>中，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="88857-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="88857-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 会根据其字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="88857-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="88857-323">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="88857-323">Establish distributed caching services</span></span>

<span data-ttu-id="88857-324">在 `Startup.ConfigureServices`中注册 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现。</span><span class="sxs-lookup"><span data-stu-id="88857-324">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="88857-325">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="88857-325">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="88857-326">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="88857-326">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="88857-327">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-327">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="88857-328">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-328">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="88857-329">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-329">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="88857-330">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="88857-330">Distributed Memory Cache</span></span>

<span data-ttu-id="88857-331">分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现，用于将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="88857-331">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="88857-332">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-332">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="88857-333">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="88857-333">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="88857-334">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="88857-334">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="88857-335">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="88857-335">In development and testing scenarios.</span></span>
* <span data-ttu-id="88857-336">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="88857-336">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="88857-337">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="88857-337">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="88857-338">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="88857-338">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="88857-339">当应用程序在 `Startup.ConfigureServices`的开发环境中运行时，示例应用程序会使用分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="88857-339">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="88857-340">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-340">Distributed SQL Server Cache</span></span>

<span data-ttu-id="88857-341">分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="88857-341">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="88857-342">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="88857-342">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="88857-343">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="88857-343">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="88857-344">通过运行 `sql-cache create` 命令在 SQL Server 中创建一个表。</span><span class="sxs-lookup"><span data-stu-id="88857-344">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="88857-345">提供 SQL Server 实例（`Data Source`）、数据库（`Initial Catalog`）、架构（例如 `dbo`）和表名（例如，`TestCache`）：</span><span class="sxs-lookup"><span data-stu-id="88857-345">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="88857-346">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="88857-346">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="88857-347">`sql-cache` 工具创建的表具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="88857-347">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="88857-349">应用应使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>的实例而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>来操作缓存值。</span><span class="sxs-lookup"><span data-stu-id="88857-349">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="88857-350">示例应用在 `Startup.ConfigureServices`的非开发环境中实施 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>：</span><span class="sxs-lookup"><span data-stu-id="88857-350">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="88857-351"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （并且可以选择 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理之外（例如，由[机密管理器](xref:security/app-secrets)存储或在*appsettings*/appsettings 中存储） *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="88857-351">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="88857-352">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="88857-352">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="88857-353">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-353">Distributed Redis Cache</span></span>

<span data-ttu-id="88857-354">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-354">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="88857-355">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="88857-355">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="88857-356">应用使用 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>）配置缓存实现：</span><span class="sxs-lookup"><span data-stu-id="88857-356">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="88857-357">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="88857-357">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="88857-358">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="88857-358">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="88857-359">在命令提示符下运行 `redis-server`。</span><span class="sxs-lookup"><span data-stu-id="88857-359">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="88857-360">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-360">Distributed NCache Cache</span></span>

<span data-ttu-id="88857-361">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-361">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="88857-362">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="88857-362">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="88857-363">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="88857-363">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="88857-364">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="88857-364">To configure NCache:</span></span>

1. <span data-ttu-id="88857-365">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="88857-365">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="88857-366">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="88857-366">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="88857-367">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="88857-367">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="88857-368">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="88857-368">Use the distributed cache</span></span>

<span data-ttu-id="88857-369">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请从应用程序中的任何构造函数请求 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实例。</span><span class="sxs-lookup"><span data-stu-id="88857-369">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="88857-370">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="88857-370">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="88857-371">示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。</span><span class="sxs-lookup"><span data-stu-id="88857-371">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="88857-372">当前时间使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 进行缓存（有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="88857-372">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="88857-373">示例应用将 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 插入到 `IndexModel` 中，供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="88857-373">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="88857-374">每次加载索引页时，都会在 `OnGetAsync`中检查缓存时间的缓存。</span><span class="sxs-lookup"><span data-stu-id="88857-374">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="88857-375">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="88857-375">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="88857-376">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="88857-376">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="88857-377">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="88857-377">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="88857-378">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="88857-378">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="88857-379">对于 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，无需使用单独的或作用域生存期（至少对于内置实现而言）。</span><span class="sxs-lookup"><span data-stu-id="88857-379">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="88857-380">你还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不必使用 DI，而是使用代码创建一个实例，从而使代码更难以测试，并违反了[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="88857-380">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="88857-381">建议</span><span class="sxs-lookup"><span data-stu-id="88857-381">Recommendations</span></span>

<span data-ttu-id="88857-382">确定最适合你的应用的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="88857-382">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="88857-383">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="88857-383">Existing infrastructure</span></span>
* <span data-ttu-id="88857-384">性能要求</span><span class="sxs-lookup"><span data-stu-id="88857-384">Performance requirements</span></span>
* <span data-ttu-id="88857-385">成本</span><span class="sxs-lookup"><span data-stu-id="88857-385">Cost</span></span>
* <span data-ttu-id="88857-386">团队体验</span><span class="sxs-lookup"><span data-stu-id="88857-386">Team experience</span></span>

<span data-ttu-id="88857-387">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="88857-387">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="88857-388">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="88857-388">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="88857-389">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="88857-389">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="88857-390">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="88857-390">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="88857-391">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="88857-391">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="88857-392">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="88857-392">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="88857-393">其他资源</span><span class="sxs-lookup"><span data-stu-id="88857-393">Additional resources</span></span>

* [<span data-ttu-id="88857-394">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="88857-394">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="88857-395">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="88857-395">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="88857-396">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="88857-396">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
