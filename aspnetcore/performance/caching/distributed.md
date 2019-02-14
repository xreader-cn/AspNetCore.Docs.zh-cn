---
title: 分布式缓存在 ASP.NET Core 中
author: guardrex
description: 了解如何使用 ASP.NET Core 分布式缓存来提高应用性能和可伸缩性，尤其是在云或服务器场环境中。
ms.author: riande
ms.custom: mvc
ms.date: 02/13/2019
uid: performance/caching/distributed
ms.openlocfilehash: a157eb075874d2118e3e34b51410b539a1ec37df
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248583"
---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="0d402-103">分布式缓存在 ASP.NET Core 中</span><span class="sxs-lookup"><span data-stu-id="0d402-103">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="0d402-104">作者：[Steve Smith](https://ardalis.com/) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0d402-104">By [Steve Smith](https://ardalis.com/) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="0d402-105">分布式的缓存是由多个应用程序服务器，通常作为对其进行访问的应用程序服务器的外部服务维护共享缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-105">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="0d402-106">分布式的缓存可以提高性能和可伸缩性的 ASP.NET Core 应用，尤其是当应用程序托管的云服务或服务器场。</span><span class="sxs-lookup"><span data-stu-id="0d402-106">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="0d402-107">分布式的缓存具有几大优势，其中缓存的数据存储在单个应用程序服务器其他缓存方案。</span><span class="sxs-lookup"><span data-stu-id="0d402-107">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="0d402-108">当分布式缓存的数据，则数据：</span><span class="sxs-lookup"><span data-stu-id="0d402-108">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="0d402-109">是*连贯*（一致） 跨多个服务器的请求。</span><span class="sxs-lookup"><span data-stu-id="0d402-109">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="0d402-110">服务器重新启动和应用部署仍然有效。</span><span class="sxs-lookup"><span data-stu-id="0d402-110">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="0d402-111">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="0d402-111">Doesn't use local memory.</span></span>

<span data-ttu-id="0d402-112">分布式的缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="0d402-112">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="0d402-113">本文介绍如何配置 SQL Server 和 Redis 分布式的缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-113">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="0d402-114">第三方实现也是可用，如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 上的 NCache](https://github.com/Alachisoft/NCache))。</span><span class="sxs-lookup"><span data-stu-id="0d402-114">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="0d402-115">无论选择哪一种实现，该应用程序与使用缓存进行交互<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口。</span><span class="sxs-lookup"><span data-stu-id="0d402-115">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="0d402-116">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="0d402-116">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="0d402-117">系统必备</span><span class="sxs-lookup"><span data-stu-id="0d402-117">Prerequisites</span></span>

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="0d402-118">若要使用的 SQL Server 分布式缓存，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或添加到的包引用[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)包。</span><span class="sxs-lookup"><span data-stu-id="0d402-118">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="0d402-119">若要使用 Redis 分布式缓存，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)并添加到的包引用[Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)包。</span><span class="sxs-lookup"><span data-stu-id="0d402-119">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="0d402-120">Redis 包不包括在`Microsoft.AspNetCore.App`包，因此您必须在项目文件中分别引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="0d402-120">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

<span data-ttu-id="0d402-121">若要使用的 SQL Server 分布式缓存，引用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)或添加到的包引用[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)包。</span><span class="sxs-lookup"><span data-stu-id="0d402-121">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="0d402-122">若要使用 Redis 分布式缓存，引用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)或添加到的包引用[Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)包。</span><span class="sxs-lookup"><span data-stu-id="0d402-122">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) or add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="0d402-123">Redis 包包含在`Microsoft.AspNetCore.All`包，因此无需引用在项目文件中单独的 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="0d402-123">The Redis package is included in `Microsoft.AspNetCore.All` package, so you don't need to reference the Redis package separately in your project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="0d402-124">若要使用的 SQL Server 分布式缓存中，添加到包引用[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)包。</span><span class="sxs-lookup"><span data-stu-id="0d402-124">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="0d402-125">若要使用 Redis 分布式缓存中，添加到包引用[Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)包。</span><span class="sxs-lookup"><span data-stu-id="0d402-125">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span>

::: moniker-end

## <a name="idistributedcache-interface"></a><span data-ttu-id="0d402-126">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="0d402-126">IDistributedCache interface</span></span>

<span data-ttu-id="0d402-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供了以下方法操作的分布式的缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="0d402-127">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="0d402-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;接受字符串键和检索缓存的项作为`byte[]`数组如果在缓存中找到。</span><span class="sxs-lookup"><span data-stu-id="0d402-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="0d402-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;中添加项 (作为`byte[]`数组) 到使用字符串键的缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="0d402-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash;刷新缓存基于其密钥，重置其滑动到期超时值 （如果有） 中的项。</span><span class="sxs-lookup"><span data-stu-id="0d402-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="0d402-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;移除缓存项根据其字符串键值。</span><span class="sxs-lookup"><span data-stu-id="0d402-131"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="0d402-132">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="0d402-132">Establish distributed caching services</span></span>

<span data-ttu-id="0d402-133">注册的实现<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>在`Startup.ConfigureServices`。</span><span class="sxs-lookup"><span data-stu-id="0d402-133">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="0d402-134">本主题中所述的框架提供实现包括：</span><span class="sxs-lookup"><span data-stu-id="0d402-134">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="0d402-135">分布式的内存缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-135">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="0d402-136">SQL Server 的分布式的缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-136">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="0d402-137">分布式的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-137">Distributed Redis cache</span></span>](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="0d402-138">分布式的内存缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-138">Distributed Memory Cache</span></span>

<span data-ttu-id="0d402-139">分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是框架提供的实现<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>，在内存中存储项。</span><span class="sxs-lookup"><span data-stu-id="0d402-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="0d402-140">分布式内存缓存不是实际的分布式的缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="0d402-141">缓存的项存储在运行该应用程序的服务器上的应用程序实例。</span><span class="sxs-lookup"><span data-stu-id="0d402-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="0d402-142">分布式内存缓存是一个有用的实现：</span><span class="sxs-lookup"><span data-stu-id="0d402-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="0d402-143">在开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="0d402-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="0d402-144">生产和内存消耗情况中使用一台服务器时不会产生问题。</span><span class="sxs-lookup"><span data-stu-id="0d402-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="0d402-145">实现分布式内存缓存摘要缓存数据存储。</span><span class="sxs-lookup"><span data-stu-id="0d402-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="0d402-146">它允许实现真正的分布式缓存解决方案在将来如果多个节点或容错能力变得非常必要。</span><span class="sxs-lookup"><span data-stu-id="0d402-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="0d402-147">示例应用将在开发环境中运行应用时使用的分布式内存缓存：</span><span class="sxs-lookup"><span data-stu-id="0d402-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=5)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="0d402-148">分布式的 SQL 服务器缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="0d402-149">SQL Server 的分布式缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许使用 SQL Server 数据库作为其后备存储分布式的缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="0d402-150">若要在 SQL Server 实例中创建的 SQL Server 缓存的项表中，可以使用`sql-cache`工具。</span><span class="sxs-lookup"><span data-stu-id="0d402-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="0d402-151">该工具使用名称和指定的架构创建一个表。</span><span class="sxs-lookup"><span data-stu-id="0d402-151">The tool creates a table with the name and schema that you specify.</span></span>

::: moniker range="< aspnetcore-2.1"

<span data-ttu-id="0d402-152">添加`SqlConfig.Tools`到`<ItemGroup>`元素的项目文件并运行`dotnet restore`。</span><span class="sxs-lookup"><span data-stu-id="0d402-152">Add `SqlConfig.Tools` to the `<ItemGroup>` element of the project file and run `dotnet restore`.</span></span>

```xml
<ItemGroup>
  <DotNetCliToolReference Include="Microsoft.Extensions.Caching.SqlConfig.Tools"
                          Version="2.0.2" />
</ItemGroup>
```

::: moniker-end

<span data-ttu-id="0d402-153">通过运行 SQL Server 中创建一个表`sql-cache create`命令。</span><span class="sxs-lookup"><span data-stu-id="0d402-153">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="0d402-154">提供 SQL Server 实例 (`Data Source`)，数据库 (`Initial Catalog`)，架构 (例如， `dbo`)，以及表名 (例如， `TestCache`):</span><span class="sxs-lookup"><span data-stu-id="0d402-154">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="0d402-155">记录一条消息以指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="0d402-155">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="0d402-156">创建的表`sql-cache`工具具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="0d402-156">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="0d402-158">应用应操作使用的实例的缓存值<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>，而不<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>。</span><span class="sxs-lookup"><span data-stu-id="0d402-158">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="0d402-159">本示例应用实现<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>非开发环境中：</span><span class="sxs-lookup"><span data-stu-id="0d402-159">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_ConfigureServices&highlight=9-15)]

> [!NOTE]
> <span data-ttu-id="0d402-160">一个<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(和 （可选）<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*>并<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理之外 (例如，通过存储[机密管理器](xref:security/app-secrets)中或在*appsettings.json* /*appsettings。{Environment}.json*文件)。</span><span class="sxs-lookup"><span data-stu-id="0d402-160">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{Environment}.json* files).</span></span> <span data-ttu-id="0d402-161">连接字符串可能包含应从源代码管理系统的凭据。</span><span class="sxs-lookup"><span data-stu-id="0d402-161">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="0d402-162">分布式的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-162">Distributed Redis Cache</span></span>

<span data-ttu-id="0d402-163">[Redis](https://redis.io/)是一种开源的内存中数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-163">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="0d402-164">您可以使用 Redis 本地，并且您可以配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)Azure 托管 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="0d402-164">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span> <span data-ttu-id="0d402-165">应用配置缓存实现使用<xref:Microsoft.Extensions.Caching.Redis.RedisCache>实例 (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span><span class="sxs-lookup"><span data-stu-id="0d402-165">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="0d402-166">若要在本地计算机上安装 Redis:</span><span class="sxs-lookup"><span data-stu-id="0d402-166">To install Redis on your local machine:</span></span>

* <span data-ttu-id="0d402-167">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="0d402-167">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
* <span data-ttu-id="0d402-168">运行`redis-server`从命令提示符。</span><span class="sxs-lookup"><span data-stu-id="0d402-168">Run `redis-server` from a command prompt.</span></span>

## <a name="use-the-distributed-cache"></a><span data-ttu-id="0d402-169">使用分布式的缓存</span><span class="sxs-lookup"><span data-stu-id="0d402-169">Use the distributed cache</span></span>

<span data-ttu-id="0d402-170">若要使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口，请求的实例<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>从任何应用程序中的构造函数。</span><span class="sxs-lookup"><span data-stu-id="0d402-170">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="0d402-171">实例由提供[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="0d402-171">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="0d402-172">当应用启动时<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>注入到`Startup.Configure`。</span><span class="sxs-lookup"><span data-stu-id="0d402-172">When the app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="0d402-173">使用缓存的当前时间<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>(有关详细信息，请参阅[Web 主机：IApplicationLifetime 接口](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span><span class="sxs-lookup"><span data-stu-id="0d402-173">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="0d402-174">示例应用程序注入<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>到`IndexModel`以供索引页。</span><span class="sxs-lookup"><span data-stu-id="0d402-174">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="0d402-175">每次加载索引页时，缓存时间检查缓存`OnGetAsync`。</span><span class="sxs-lookup"><span data-stu-id="0d402-175">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="0d402-176">如果尚未过期的缓存的时间，将显示时间。</span><span class="sxs-lookup"><span data-stu-id="0d402-176">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="0d402-177">如果自上一次访问缓存的时间 （已加载此页的最后一个时间） 已过去 20 秒，该页将显示*缓存时间已过*。</span><span class="sxs-lookup"><span data-stu-id="0d402-177">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="0d402-178">通过选择，立即更新为当前时间的缓存的时间**重置缓存时间**按钮。</span><span class="sxs-lookup"><span data-stu-id="0d402-178">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="0d402-179">按钮触发器`OnPostResetCachedTime`处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="0d402-179">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="0d402-180">无需为<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例使用 Singleton 或 Scoped 生命周期（至少对内置实现来说是这样的）。</span><span class="sxs-lookup"><span data-stu-id="0d402-180">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="0d402-181">此外可以创建<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例可能需要某一个而不是使用 DI，但在代码中创建实例会使代码难以测试和违反[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="0d402-181">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="0d402-182">建议</span><span class="sxs-lookup"><span data-stu-id="0d402-182">Recommendations</span></span>

<span data-ttu-id="0d402-183">确定哪一种实现的时<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>最适合于您的应用程序，请考虑以下：</span><span class="sxs-lookup"><span data-stu-id="0d402-183">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="0d402-184">现有的基础结构</span><span class="sxs-lookup"><span data-stu-id="0d402-184">Existing infrastructure</span></span>
* <span data-ttu-id="0d402-185">性能要求</span><span class="sxs-lookup"><span data-stu-id="0d402-185">Performance requirements</span></span>
* <span data-ttu-id="0d402-186">成本</span><span class="sxs-lookup"><span data-stu-id="0d402-186">Cost</span></span>
* <span data-ttu-id="0d402-187">团队体验</span><span class="sxs-lookup"><span data-stu-id="0d402-187">Team experience</span></span>

<span data-ttu-id="0d402-188">缓存解决方案通常依赖于内存中存储提供快速检索的缓存数据，但内存是有限的资源，而且成本展开。</span><span class="sxs-lookup"><span data-stu-id="0d402-188">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="0d402-189">仅存储通常用于缓存中的数据。</span><span class="sxs-lookup"><span data-stu-id="0d402-189">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="0d402-190">通常情况下，Redis 缓存提供更高的吞吐量和延迟低于 SQL Server 缓存。</span><span class="sxs-lookup"><span data-stu-id="0d402-190">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="0d402-191">但是，进行基准测试时通常需要确定的缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="0d402-191">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="0d402-192">当 SQL Server 用作分布式的缓存后备存储时，使用的同一个数据库缓存与应用程序的普通数据存储和检索可以对这两者的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="0d402-192">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="0d402-193">我们建议使用专用的 SQL Server 实例为分布式缓存后备存储。</span><span class="sxs-lookup"><span data-stu-id="0d402-193">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0d402-194">其他资源</span><span class="sxs-lookup"><span data-stu-id="0d402-194">Additional resources</span></span>

* [<span data-ttu-id="0d402-195">Redis 缓存在 Azure 上</span><span class="sxs-lookup"><span data-stu-id="0d402-195">Redis Cache on Azure</span></span>](https://azure.microsoft.com/documentation/services/redis-cache/)
* [<span data-ttu-id="0d402-196">在 Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="0d402-196">SQL Database on Azure</span></span>](https://azure.microsoft.com/documentation/services/sql-database/)
* <span data-ttu-id="0d402-197">[ASP.NET Core IDistributedCache 提供程序的 Web 场中的 NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([在 GitHub 上的 NCache](https://github.com/Alachisoft/NCache))</span><span class="sxs-lookup"><span data-stu-id="0d402-197">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
