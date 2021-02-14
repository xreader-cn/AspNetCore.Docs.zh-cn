---
title: ASP.NET Core 中的分布式缓存
author: rick-anderson
description: 了解如何使用 ASP.NET Core 分布式缓存来改善应用程序的性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/07/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: 6f89046f2e1805111dd81b3282253a72a7c6ea09
ms.sourcegitcommit: 1166b0ff3828418559510c661e8240e5c5717bb7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/12/2021
ms.locfileid: "100281015"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="14c3b-103">ASP.NET Core 中的分布式缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="14c3b-104">作者： [Mohsin Nasir](https://github.com/mohsinnasir) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="14c3b-104">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="14c3b-105">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="14c3b-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="14c3b-106">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="14c3b-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="14c3b-107">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="14c3b-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="14c3b-108">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="14c3b-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="14c3b-109"> (一致性) 跨多个 *服务器的请求* 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="14c3b-110">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="14c3b-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="14c3b-111">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-111">Doesn't use local memory.</span></span>

<span data-ttu-id="14c3b-112">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="14c3b-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="14c3b-113">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="14c3b-114">第三方实现也可用，例如[GitHub 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="14c3b-115">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="14c3b-116">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="14c3b-116">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="14c3b-117">先决条件</span><span class="sxs-lookup"><span data-stu-id="14c3b-117">Prerequisites</span></span>

<span data-ttu-id="14c3b-118">若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="14c3b-118">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="14c3b-119">若要使用 Redis 分布式缓存，请将包引用添加到 [StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-119">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="14c3b-120">若要使用 NCache 分布式缓存，请将包引用添加到 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-120">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="14c3b-121">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="14c3b-121">IDistributedCache interface</span></span>

<span data-ttu-id="14c3b-122"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="14c3b-122">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="14c3b-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="14c3b-123"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="14c3b-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项 (作为 `byte[]` 数组) 添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-124"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="14c3b-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：基于其键刷新缓存中的项，如果有任何) ，则重置其可调过期超时 (。</span><span class="sxs-lookup"><span data-stu-id="14c3b-125"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="14c3b-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="14c3b-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="14c3b-127">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="14c3b-127">Establish distributed caching services</span></span>

<span data-ttu-id="14c3b-128">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-128">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14c3b-129">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="14c3b-129">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="14c3b-130">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-130">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="14c3b-131">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-131">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="14c3b-132">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-132">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="14c3b-133">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-133">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="14c3b-134">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-134">Distributed Memory Cache</span></span>

<span data-ttu-id="14c3b-135">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-135">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="14c3b-136">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-136">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="14c3b-137">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="14c3b-137">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="14c3b-138">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="14c3b-138">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="14c3b-139">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="14c3b-139">In development and testing scenarios.</span></span>
* <span data-ttu-id="14c3b-140">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="14c3b-140">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="14c3b-141">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="14c3b-141">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="14c3b-142">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="14c3b-142">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="14c3b-143">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-143">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="14c3b-144">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-144">Distributed SQL Server Cache</span></span>

<span data-ttu-id="14c3b-145">分布式 SQL Server 缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="14c3b-145">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="14c3b-146">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="14c3b-146">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="14c3b-147">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="14c3b-147">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="14c3b-148">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-148">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="14c3b-149">提供 SQL Server 实例 (`Data Source`) 、数据库 (`Initial Catalog`) 、架构 (例如) `dbo` 和表名称 (例如 `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-149">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="14c3b-150">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="14c3b-150">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="14c3b-151">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="14c3b-151">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="14c3b-153">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-153">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="14c3b-154">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-154">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="14c3b-155"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (和（可选） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理的外部 (例如，由 [机密管理器](xref:security/app-secrets)或 appsettings 中存储的 *appsettings.json* / *。 {环境} json* 文件) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-155">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="14c3b-156">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="14c3b-156">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="14c3b-157">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-157">Distributed Redis Cache</span></span>

<span data-ttu-id="14c3b-158">[Redis](https://redis.io/) 是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-158">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span>  <span data-ttu-id="14c3b-159">可以为 Azure 托管的 ASP.NET Core 应用配置 [Azure Redis 缓存](https://azure.microsoft.com/services/cache/) ，并使用 Azure Redis 缓存进行本地开发。</span><span class="sxs-lookup"><span data-stu-id="14c3b-159">You can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app, and use an Azure Redis Cache for local development.</span></span>

<span data-ttu-id="14c3b-160">应用使用实例 () 配置缓存实现 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-160">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>).</span></span>

<span data-ttu-id="14c3b-161">有关详细信息，请参阅 [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-161">For more information, see [Azure Cache for Redis](/azure/azure-cache-for-redis/cache-overview).</span></span>

<span data-ttu-id="14c3b-162">若要讨论本地 Redis 缓存的替代方法，请参阅 [此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/19542) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-162">See [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/19542) for a discussion on alternative approaches to a local Redis cache.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="14c3b-163">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-163">Distributed NCache Cache</span></span>

<span data-ttu-id="14c3b-164">[NCache](https://github.com/Alachisoft/NCache) 是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-164">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="14c3b-165">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="14c3b-165">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="14c3b-166">若要在本地计算机上安装和配置 NCache，请参阅 [Windows ( .net 和 .Net Core) 入门指南 ](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-166">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="14c3b-167">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="14c3b-167">To configure NCache:</span></span>

1. <span data-ttu-id="14c3b-168">安装 [NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-168">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="14c3b-169">在 [ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="14c3b-169">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="14c3b-170">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="14c3b-170">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="14c3b-171">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-171">Use the distributed cache</span></span>

<span data-ttu-id="14c3b-172">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="14c3b-172">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="14c3b-173">实例由 [ (DI) 的依赖关系注入 ](xref:fundamentals/dependency-injection)提供。</span><span class="sxs-lookup"><span data-stu-id="14c3b-173">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="14c3b-174">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-174">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="14c3b-175">使用 (缓存当前时间 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 有关详细信息，请参阅 [泛型主机： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)) ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-175">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="14c3b-176">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="14c3b-176">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="14c3b-177">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-177">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="14c3b-178">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="14c3b-178">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="14c3b-179">如果自上一次 () 加载缓存时间之后经过了20秒的时间，则页面显示 *缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="14c3b-179">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="14c3b-180">通过选择 " **重置缓存时间** " 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="14c3b-180">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="14c3b-181">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="14c3b-181">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="14c3b-182">对于实例，无需使用实例的单一实例或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (至少为内置实现) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-182">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="14c3b-183">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反 [显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-183">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="14c3b-184">建议</span><span class="sxs-lookup"><span data-stu-id="14c3b-184">Recommendations</span></span>

<span data-ttu-id="14c3b-185">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="14c3b-185">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="14c3b-186">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="14c3b-186">Existing infrastructure</span></span>
* <span data-ttu-id="14c3b-187">性能要求</span><span class="sxs-lookup"><span data-stu-id="14c3b-187">Performance requirements</span></span>
* <span data-ttu-id="14c3b-188">Cost</span><span class="sxs-lookup"><span data-stu-id="14c3b-188">Cost</span></span>
* <span data-ttu-id="14c3b-189">团队体验</span><span class="sxs-lookup"><span data-stu-id="14c3b-189">Team experience</span></span>

<span data-ttu-id="14c3b-190">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="14c3b-190">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="14c3b-191">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-191">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="14c3b-192">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="14c3b-192">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="14c3b-193">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="14c3b-193">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="14c3b-194">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14c3b-194">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="14c3b-195">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="14c3b-195">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14c3b-196">其他资源</span><span class="sxs-lookup"><span data-stu-id="14c3b-196">Additional resources</span></span>

* [<span data-ttu-id="14c3b-197">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-197">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="14c3b-198">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="14c3b-198">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="14c3b-199">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache 在 GitHub 上](https://github.com/Alachisoft/NCache)) </span><span class="sxs-lookup"><span data-stu-id="14c3b-199">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="14c3b-200">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="14c3b-200">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="14c3b-201">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="14c3b-201">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="14c3b-202">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="14c3b-202">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="14c3b-203">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="14c3b-203">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="14c3b-204"> (一致性) 跨多个 *服务器的请求* 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-204">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="14c3b-205">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="14c3b-205">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="14c3b-206">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-206">Doesn't use local memory.</span></span>

<span data-ttu-id="14c3b-207">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="14c3b-207">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="14c3b-208">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-208">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="14c3b-209">第三方实现也可用，例如[GitHub 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-209">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="14c3b-210">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-210">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="14c3b-211">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="14c3b-211">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="14c3b-212">先决条件</span><span class="sxs-lookup"><span data-stu-id="14c3b-212">Prerequisites</span></span>

<span data-ttu-id="14c3b-213">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="14c3b-213">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="14c3b-214">若要使用 Redis 分布式缓存，请参考 [AspNetCore 元包](xref:fundamentals/metapackage-app) ，并 [将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) 添加到包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-214">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="14c3b-215">Redis 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-215">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="14c3b-216">若要使用 NCache 分布式缓存，请参考 [AspNetCore 元包](xref:fundamentals/metapackage-app) ，并将包引用添加到 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 包中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-216">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="14c3b-217">NCache 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-217">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="14c3b-218">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="14c3b-218">IDistributedCache interface</span></span>

<span data-ttu-id="14c3b-219"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="14c3b-219">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="14c3b-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="14c3b-220"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="14c3b-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项 (作为 `byte[]` 数组) 添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-221"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="14c3b-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：基于其键刷新缓存中的项，如果有任何) ，则重置其可调过期超时 (。</span><span class="sxs-lookup"><span data-stu-id="14c3b-222"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="14c3b-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="14c3b-223"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="14c3b-224">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="14c3b-224">Establish distributed caching services</span></span>

<span data-ttu-id="14c3b-225">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-225">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14c3b-226">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="14c3b-226">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="14c3b-227">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-227">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="14c3b-228">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-228">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="14c3b-229">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-229">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="14c3b-230">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-230">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="14c3b-231">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-231">Distributed Memory Cache</span></span>

<span data-ttu-id="14c3b-232">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-232">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="14c3b-233">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-233">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="14c3b-234">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="14c3b-234">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="14c3b-235">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="14c3b-235">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="14c3b-236">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="14c3b-236">In development and testing scenarios.</span></span>
* <span data-ttu-id="14c3b-237">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="14c3b-237">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="14c3b-238">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="14c3b-238">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="14c3b-239">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="14c3b-239">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="14c3b-240">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-240">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="14c3b-241">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-241">Distributed SQL Server Cache</span></span>

<span data-ttu-id="14c3b-242">分布式 SQL Server 缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="14c3b-242">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="14c3b-243">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="14c3b-243">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="14c3b-244">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="14c3b-244">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="14c3b-245">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-245">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="14c3b-246">提供 SQL Server 实例 (`Data Source`) 、数据库 (`Initial Catalog`) 、架构 (例如) `dbo` 和表名称 (例如 `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-246">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="14c3b-247">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="14c3b-247">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="14c3b-248">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="14c3b-248">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="14c3b-250">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-250">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="14c3b-251">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-251">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="14c3b-252"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (和（可选） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理的外部 (例如，由 [机密管理器](xref:security/app-secrets)或 appsettings 中存储的 *appsettings.json* / *。 {环境} json* 文件) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-252">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="14c3b-253">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="14c3b-253">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="14c3b-254">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-254">Distributed Redis Cache</span></span>

<span data-ttu-id="14c3b-255">[Redis](https://redis.io/) 是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-255">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="14c3b-256">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置 [Azure Redis 缓存](https://azure.microsoft.com/services/cache/) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-256">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="14c3b-257">应用使用 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 中的非开发环境中的实例 () 配置缓存实现 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-257">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="14c3b-258">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="14c3b-258">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="14c3b-259">安装 [Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-259">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="14c3b-260">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="14c3b-260">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="14c3b-261">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-261">Distributed NCache Cache</span></span>

<span data-ttu-id="14c3b-262">[NCache](https://github.com/Alachisoft/NCache) 是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-262">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="14c3b-263">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="14c3b-263">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="14c3b-264">若要在本地计算机上安装和配置 NCache，请参阅 [Windows ( .net 和 .Net Core) 入门指南 ](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-264">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="14c3b-265">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="14c3b-265">To configure NCache:</span></span>

1. <span data-ttu-id="14c3b-266">安装 [NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-266">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="14c3b-267">在 [ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="14c3b-267">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="14c3b-268">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="14c3b-268">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="14c3b-269">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-269">Use the distributed cache</span></span>

<span data-ttu-id="14c3b-270">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="14c3b-270">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="14c3b-271">实例由 [ (DI) 的依赖关系注入 ](xref:fundamentals/dependency-injection)提供。</span><span class="sxs-lookup"><span data-stu-id="14c3b-271">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="14c3b-272">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-272">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="14c3b-273">使用 (缓存当前时间 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 有关详细信息，请参阅 [Web 主机： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-273">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="14c3b-274">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="14c3b-274">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="14c3b-275">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-275">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="14c3b-276">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="14c3b-276">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="14c3b-277">如果自上一次 () 加载缓存时间之后经过了20秒的时间，则页面显示 *缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="14c3b-277">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="14c3b-278">通过选择 " **重置缓存时间** " 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="14c3b-278">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="14c3b-279">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="14c3b-279">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="14c3b-280">对于实例，无需使用实例的单一实例或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (至少为内置实现) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-280">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="14c3b-281">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反 [显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-281">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="14c3b-282">建议</span><span class="sxs-lookup"><span data-stu-id="14c3b-282">Recommendations</span></span>

<span data-ttu-id="14c3b-283">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="14c3b-283">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="14c3b-284">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="14c3b-284">Existing infrastructure</span></span>
* <span data-ttu-id="14c3b-285">性能要求</span><span class="sxs-lookup"><span data-stu-id="14c3b-285">Performance requirements</span></span>
* <span data-ttu-id="14c3b-286">Cost</span><span class="sxs-lookup"><span data-stu-id="14c3b-286">Cost</span></span>
* <span data-ttu-id="14c3b-287">团队体验</span><span class="sxs-lookup"><span data-stu-id="14c3b-287">Team experience</span></span>

<span data-ttu-id="14c3b-288">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="14c3b-288">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="14c3b-289">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-289">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="14c3b-290">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="14c3b-290">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="14c3b-291">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="14c3b-291">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="14c3b-292">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14c3b-292">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="14c3b-293">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="14c3b-293">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14c3b-294">其他资源</span><span class="sxs-lookup"><span data-stu-id="14c3b-294">Additional resources</span></span>

* [<span data-ttu-id="14c3b-295">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-295">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="14c3b-296">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="14c3b-296">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="14c3b-297">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache 在 GitHub 上](https://github.com/Alachisoft/NCache)) </span><span class="sxs-lookup"><span data-stu-id="14c3b-297">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="14c3b-298">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="14c3b-298">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="14c3b-299">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="14c3b-299">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="14c3b-300">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="14c3b-300">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="14c3b-301">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="14c3b-301">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="14c3b-302"> (一致性) 跨多个 *服务器的请求* 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-302">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="14c3b-303">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="14c3b-303">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="14c3b-304">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-304">Doesn't use local memory.</span></span>

<span data-ttu-id="14c3b-305">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="14c3b-305">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="14c3b-306">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-306">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="14c3b-307">第三方实现也可用，例如[GitHub 上](https://github.com/Alachisoft/NCache)的[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) (NCache) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-307">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="14c3b-308">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-308">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="14c3b-309">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="14c3b-309">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="14c3b-310">先决条件</span><span class="sxs-lookup"><span data-stu-id="14c3b-310">Prerequisites</span></span>

<span data-ttu-id="14c3b-311">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="14c3b-311">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="14c3b-312">若要使用 Redis 分布式缓存，请参考 [AspNetCore 元包](xref:fundamentals/metapackage-app) ，并 [将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) 添加到包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-312">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="14c3b-313">Redis 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-313">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="14c3b-314">若要使用 NCache 分布式缓存，请参考 [AspNetCore 元包](xref:fundamentals/metapackage-app) ，并将包引用添加到 [NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) 包中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-314">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="14c3b-315">NCache 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="14c3b-315">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="14c3b-316">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="14c3b-316">IDistributedCache interface</span></span>

<span data-ttu-id="14c3b-317"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="14c3b-317">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="14c3b-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="14c3b-318"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="14c3b-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项 (作为 `byte[]` 数组) 添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-319"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="14c3b-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：基于其键刷新缓存中的项，如果有任何) ，则重置其可调过期超时 (。</span><span class="sxs-lookup"><span data-stu-id="14c3b-320"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="14c3b-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="14c3b-321"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="14c3b-322">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="14c3b-322">Establish distributed caching services</span></span>

<span data-ttu-id="14c3b-323">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-323">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="14c3b-324">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="14c3b-324">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="14c3b-325">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-325">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="14c3b-326">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-326">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="14c3b-327">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-327">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="14c3b-328">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-328">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="14c3b-329">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-329">Distributed Memory Cache</span></span>

<span data-ttu-id="14c3b-330">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-330">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="14c3b-331">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-331">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="14c3b-332">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="14c3b-332">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="14c3b-333">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="14c3b-333">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="14c3b-334">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="14c3b-334">In development and testing scenarios.</span></span>
* <span data-ttu-id="14c3b-335">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="14c3b-335">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="14c3b-336">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="14c3b-336">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="14c3b-337">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="14c3b-337">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="14c3b-338">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-338">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="14c3b-339">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-339">Distributed SQL Server Cache</span></span>

<span data-ttu-id="14c3b-340">分布式 SQL Server 缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="14c3b-340">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="14c3b-341">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="14c3b-341">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="14c3b-342">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="14c3b-342">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="14c3b-343">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-343">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="14c3b-344">提供 SQL Server 实例 (`Data Source`) 、数据库 (`Initial Catalog`) 、架构 (例如) `dbo` 和表名称 (例如 `TestCache`) ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-344">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="14c3b-345">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="14c3b-345">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="14c3b-346">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="14c3b-346">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="14c3b-348">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-348">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="14c3b-349">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-349">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="14c3b-350"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (和（可选） <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理的外部 (例如，由 [机密管理器](xref:security/app-secrets)或 appsettings 中存储的 *appsettings.json* / *。 {环境} json* 文件) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-350">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="14c3b-351">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="14c3b-351">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="14c3b-352">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-352">Distributed Redis Cache</span></span>

<span data-ttu-id="14c3b-353">[Redis](https://redis.io/) 是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-353">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="14c3b-354">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置 [Azure Redis 缓存](https://azure.microsoft.com/services/cache/) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-354">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="14c3b-355">应用使用实例) 配置缓存实现 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-355">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="14c3b-356">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="14c3b-356">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="14c3b-357">安装 [Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-357">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="14c3b-358">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="14c3b-358">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="14c3b-359">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-359">Distributed NCache Cache</span></span>

<span data-ttu-id="14c3b-360">[NCache](https://github.com/Alachisoft/NCache) 是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="14c3b-360">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="14c3b-361">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="14c3b-361">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="14c3b-362">若要在本地计算机上安装和配置 NCache，请参阅 [Windows ( .net 和 .Net Core) 入门指南 ](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-362">To install and configure NCache on your local machine, see [Getting Started Guide for Windows (.NET and .NET Core)](https://www.alachisoft.com/resources/docs/ncache/getting-started-guide-windows/).</span></span>

<span data-ttu-id="14c3b-363">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="14c3b-363">To configure NCache:</span></span>

1. <span data-ttu-id="14c3b-364">安装 [NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-364">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="14c3b-365">在 [ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="14c3b-365">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="14c3b-366">将以下代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="14c3b-366">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="14c3b-367">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-367">Use the distributed cache</span></span>

<span data-ttu-id="14c3b-368">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="14c3b-368">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="14c3b-369">实例由 [ (DI) 的依赖关系注入 ](xref:fundamentals/dependency-injection)提供。</span><span class="sxs-lookup"><span data-stu-id="14c3b-369">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="14c3b-370">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-370">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="14c3b-371">使用 (缓存当前时间 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 有关详细信息，请参阅 [Web 主机： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)) ：</span><span class="sxs-lookup"><span data-stu-id="14c3b-371">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="14c3b-372">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="14c3b-372">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="14c3b-373">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-373">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="14c3b-374">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="14c3b-374">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="14c3b-375">如果自上一次 () 加载缓存时间之后经过了20秒的时间，则页面显示 *缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="14c3b-375">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="14c3b-376">通过选择 " **重置缓存时间** " 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="14c3b-376">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="14c3b-377">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="14c3b-377">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="14c3b-378">对于实例，无需使用实例的单一实例或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> (至少为内置实现) 。</span><span class="sxs-lookup"><span data-stu-id="14c3b-378">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="14c3b-379">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反 [显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="14c3b-379">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="14c3b-380">建议</span><span class="sxs-lookup"><span data-stu-id="14c3b-380">Recommendations</span></span>

<span data-ttu-id="14c3b-381">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="14c3b-381">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="14c3b-382">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="14c3b-382">Existing infrastructure</span></span>
* <span data-ttu-id="14c3b-383">性能要求</span><span class="sxs-lookup"><span data-stu-id="14c3b-383">Performance requirements</span></span>
* <span data-ttu-id="14c3b-384">Cost</span><span class="sxs-lookup"><span data-stu-id="14c3b-384">Cost</span></span>
* <span data-ttu-id="14c3b-385">团队体验</span><span class="sxs-lookup"><span data-stu-id="14c3b-385">Team experience</span></span>

<span data-ttu-id="14c3b-386">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="14c3b-386">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="14c3b-387">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="14c3b-387">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="14c3b-388">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="14c3b-388">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="14c3b-389">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="14c3b-389">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="14c3b-390">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="14c3b-390">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="14c3b-391">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="14c3b-391">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="14c3b-392">其他资源</span><span class="sxs-lookup"><span data-stu-id="14c3b-392">Additional resources</span></span>

* [<span data-ttu-id="14c3b-393">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="14c3b-393">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="14c3b-394">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="14c3b-394">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="14c3b-395">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache 在 GitHub 上](https://github.com/Alachisoft/NCache)) </span><span class="sxs-lookup"><span data-stu-id="14c3b-395">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
