---
<span data-ttu-id="044d9-101">标题：作者：说明： monikerRange：： ms. 作者： ms. 自定义： ms. 日期：非 loc：</span><span class="sxs-lookup"><span data-stu-id="044d9-101">title: author: description: monikerRange: ms.author: ms.custom: ms.date: no-loc:</span></span>
- <span data-ttu-id="044d9-102">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="044d9-102">'Blazor'</span></span>
- <span data-ttu-id="044d9-103">'Identity'</span><span class="sxs-lookup"><span data-stu-id="044d9-103">'Identity'</span></span>
- <span data-ttu-id="044d9-104">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="044d9-104">'Let's Encrypt'</span></span>
- <span data-ttu-id="044d9-105">'Razor'</span><span class="sxs-lookup"><span data-stu-id="044d9-105">'Razor'</span></span>
- <span data-ttu-id="044d9-106">'SignalR' uid:</span><span class="sxs-lookup"><span data-stu-id="044d9-106">'SignalR' uid:</span></span> 

---
# <a name="distributed-caching-in-aspnet-core"></a><span data-ttu-id="044d9-107">ASP.NET Core 中的分布式缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-107">Distributed caching in ASP.NET Core</span></span>

<span data-ttu-id="044d9-108">作者： [Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="044d9-108">By [Mohsin Nasir](https://github.com/mohsinnasir) and [Steve Smith](https://ardalis.com/)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="044d9-109">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="044d9-109">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="044d9-110">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="044d9-110">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="044d9-111">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="044d9-111">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="044d9-112">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="044d9-112">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="044d9-113">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="044d9-113">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="044d9-114">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="044d9-114">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="044d9-115">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="044d9-115">Doesn't use local memory.</span></span>

<span data-ttu-id="044d9-116">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="044d9-116">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="044d9-117">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-117">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="044d9-118">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="044d9-118">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="044d9-119">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="044d9-119">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="044d9-120">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="044d9-120">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="044d9-121">先决条件</span><span class="sxs-lookup"><span data-stu-id="044d9-121">Prerequisites</span></span>

<span data-ttu-id="044d9-122">若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="044d9-122">To use a SQL Server distributed cache, add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="044d9-123">若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。</span><span class="sxs-lookup"><span data-stu-id="044d9-123">To use a Redis distributed cache, add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span>

<span data-ttu-id="044d9-124">若要使用 NCache 分布式缓存，请将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包。</span><span class="sxs-lookup"><span data-stu-id="044d9-124">To use NCache distributed cache, add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="044d9-125">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="044d9-125">IDistributedCache interface</span></span>

<span data-ttu-id="044d9-126"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="044d9-126">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="044d9-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="044d9-127"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="044d9-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-128"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="044d9-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：根据项的键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="044d9-129"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="044d9-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="044d9-130"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="044d9-131">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="044d9-131">Establish distributed caching services</span></span>

<span data-ttu-id="044d9-132">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-132">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="044d9-133">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="044d9-133">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="044d9-134">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-134">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="044d9-135">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-135">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="044d9-136">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-136">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="044d9-137">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-137">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="044d9-138">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-138">Distributed Memory Cache</span></span>

<span data-ttu-id="044d9-139">分布式内存缓存（ <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ）是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-139">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="044d9-140">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-140">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="044d9-141">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="044d9-141">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="044d9-142">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="044d9-142">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="044d9-143">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="044d9-143">In development and testing scenarios.</span></span>
* <span data-ttu-id="044d9-144">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="044d9-144">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="044d9-145">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="044d9-145">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="044d9-146">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="044d9-146">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="044d9-147">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-147">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="044d9-148">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-148">Distributed SQL Server Cache</span></span>

<span data-ttu-id="044d9-149">分布式 SQL Server 缓存实现（ <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="044d9-149">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="044d9-150">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="044d9-150">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="044d9-151">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="044d9-151">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="044d9-152">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-152">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="044d9-153">提供 SQL Server 实例（ `Data Source` ）、数据库（ `Initial Catalog` ）、架构（例如， `dbo` ）和表名（例如 `TestCache` ）：</span><span class="sxs-lookup"><span data-stu-id="044d9-153">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="044d9-154">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="044d9-154">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="044d9-155">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="044d9-155">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="044d9-157">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="044d9-157">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="044d9-158">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-158">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="044d9-159"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>（以及（可选 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*appsettings 中存储） / *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="044d9-159">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="044d9-160">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="044d9-160">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="044d9-161">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-161">Distributed Redis Cache</span></span>

<span data-ttu-id="044d9-162">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-162">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="044d9-163">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-163">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="044d9-164">应用使用 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 中的非开发环境中的实例（）配置缓存实现 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-164">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="044d9-165">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="044d9-165">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="044d9-166">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-166">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="044d9-167">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="044d9-167">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="044d9-168">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-168">Distributed NCache Cache</span></span>

<span data-ttu-id="044d9-169">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-169">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="044d9-170">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="044d9-170">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="044d9-171">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-171">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="044d9-172">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="044d9-172">To configure NCache:</span></span>

1. <span data-ttu-id="044d9-173">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-173">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="044d9-174">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="044d9-174">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="044d9-175">将下列代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="044d9-175">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="044d9-176">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-176">Use the distributed cache</span></span>

<span data-ttu-id="044d9-177">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="044d9-177">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="044d9-178">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="044d9-178">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="044d9-179">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-179">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="044d9-180">使用缓存当前时间 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> （有关详细信息，请参阅[泛型 Host： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)）：</span><span class="sxs-lookup"><span data-stu-id="044d9-180">The current time is cached using <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> (for more information, see [Generic Host: IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)):</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="044d9-181">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="044d9-181">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="044d9-182">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-182">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="044d9-183">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="044d9-183">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="044d9-184">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="044d9-184">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="044d9-185">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="044d9-185">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="044d9-186">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="044d9-186">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="044d9-187">对于实例，无需使用单独的或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （至少对于内置实现）。</span><span class="sxs-lookup"><span data-stu-id="044d9-187">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="044d9-188">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="044d9-188">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="044d9-189">建议</span><span class="sxs-lookup"><span data-stu-id="044d9-189">Recommendations</span></span>

<span data-ttu-id="044d9-190">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="044d9-190">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="044d9-191">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="044d9-191">Existing infrastructure</span></span>
* <span data-ttu-id="044d9-192">性能要求</span><span class="sxs-lookup"><span data-stu-id="044d9-192">Performance requirements</span></span>
* <span data-ttu-id="044d9-193">成本</span><span class="sxs-lookup"><span data-stu-id="044d9-193">Cost</span></span>
* <span data-ttu-id="044d9-194">团队体验</span><span class="sxs-lookup"><span data-stu-id="044d9-194">Team experience</span></span>

<span data-ttu-id="044d9-195">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="044d9-195">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="044d9-196">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-196">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="044d9-197">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="044d9-197">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="044d9-198">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="044d9-198">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="044d9-199">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="044d9-199">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="044d9-200">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="044d9-200">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="044d9-201">其他资源</span><span class="sxs-lookup"><span data-stu-id="044d9-201">Additional resources</span></span>

* [<span data-ttu-id="044d9-202">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-202">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="044d9-203">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="044d9-203">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="044d9-204">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="044d9-204">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="044d9-205">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="044d9-205">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="044d9-206">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="044d9-206">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="044d9-207">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="044d9-207">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="044d9-208">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="044d9-208">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="044d9-209">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="044d9-209">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="044d9-210">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="044d9-210">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="044d9-211">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="044d9-211">Doesn't use local memory.</span></span>

<span data-ttu-id="044d9-212">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="044d9-212">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="044d9-213">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-213">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="044d9-214">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="044d9-214">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="044d9-215">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="044d9-215">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="044d9-216">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="044d9-216">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="044d9-217">先决条件</span><span class="sxs-lookup"><span data-stu-id="044d9-217">Prerequisites</span></span>

<span data-ttu-id="044d9-218">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="044d9-218">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="044d9-219">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="044d9-219">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis) package.</span></span> <span data-ttu-id="044d9-220">Redis 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="044d9-220">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="044d9-221">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="044d9-221">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="044d9-222">NCache 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="044d9-222">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="044d9-223">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="044d9-223">IDistributedCache interface</span></span>

<span data-ttu-id="044d9-224"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="044d9-224">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="044d9-225"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="044d9-225"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="044d9-226"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-226"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="044d9-227"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：根据项的键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="044d9-227"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="044d9-228"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="044d9-228"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="044d9-229">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="044d9-229">Establish distributed caching services</span></span>

<span data-ttu-id="044d9-230">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-230">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="044d9-231">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="044d9-231">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="044d9-232">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-232">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="044d9-233">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-233">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="044d9-234">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-234">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="044d9-235">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-235">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="044d9-236">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-236">Distributed Memory Cache</span></span>

<span data-ttu-id="044d9-237">分布式内存缓存（ <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ）是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-237">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="044d9-238">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-238">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="044d9-239">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="044d9-239">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="044d9-240">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="044d9-240">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="044d9-241">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="044d9-241">In development and testing scenarios.</span></span>
* <span data-ttu-id="044d9-242">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="044d9-242">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="044d9-243">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="044d9-243">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="044d9-244">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="044d9-244">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="044d9-245">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-245">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="044d9-246">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-246">Distributed SQL Server Cache</span></span>

<span data-ttu-id="044d9-247">分布式 SQL Server 缓存实现（ <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="044d9-247">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="044d9-248">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="044d9-248">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="044d9-249">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="044d9-249">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="044d9-250">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-250">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="044d9-251">提供 SQL Server 实例（ `Data Source` ）、数据库（ `Initial Catalog` ）、架构（例如， `dbo` ）和表名（例如 `TestCache` ）：</span><span class="sxs-lookup"><span data-stu-id="044d9-251">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="044d9-252">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="044d9-252">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="044d9-253">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="044d9-253">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="044d9-255">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="044d9-255">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="044d9-256">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-256">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="044d9-257"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>（以及（可选 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*appsettings 中存储） / *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="044d9-257">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="044d9-258">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="044d9-258">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="044d9-259">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-259">Distributed Redis Cache</span></span>

<span data-ttu-id="044d9-260">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-260">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="044d9-261">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-261">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="044d9-262">应用使用 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 中的非开发环境中的实例（）配置缓存实现 <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*> `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-262">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

<span data-ttu-id="044d9-263">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="044d9-263">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="044d9-264">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-264">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="044d9-265">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="044d9-265">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="044d9-266">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-266">Distributed NCache Cache</span></span>

<span data-ttu-id="044d9-267">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-267">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="044d9-268">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="044d9-268">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="044d9-269">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-269">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="044d9-270">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="044d9-270">To configure NCache:</span></span>

1. <span data-ttu-id="044d9-271">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-271">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="044d9-272">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="044d9-272">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="044d9-273">将下列代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="044d9-273">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="044d9-274">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-274">Use the distributed cache</span></span>

<span data-ttu-id="044d9-275">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="044d9-275">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="044d9-276">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="044d9-276">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="044d9-277">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-277">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="044d9-278">使用缓存当前时间 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> （有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="044d9-278">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="044d9-279">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="044d9-279">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="044d9-280">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-280">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="044d9-281">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="044d9-281">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="044d9-282">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="044d9-282">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="044d9-283">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="044d9-283">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="044d9-284">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="044d9-284">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="044d9-285">对于实例，无需使用单独的或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （至少对于内置实现）。</span><span class="sxs-lookup"><span data-stu-id="044d9-285">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="044d9-286">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="044d9-286">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="044d9-287">建议</span><span class="sxs-lookup"><span data-stu-id="044d9-287">Recommendations</span></span>

<span data-ttu-id="044d9-288">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="044d9-288">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="044d9-289">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="044d9-289">Existing infrastructure</span></span>
* <span data-ttu-id="044d9-290">性能要求</span><span class="sxs-lookup"><span data-stu-id="044d9-290">Performance requirements</span></span>
* <span data-ttu-id="044d9-291">成本</span><span class="sxs-lookup"><span data-stu-id="044d9-291">Cost</span></span>
* <span data-ttu-id="044d9-292">团队体验</span><span class="sxs-lookup"><span data-stu-id="044d9-292">Team experience</span></span>

<span data-ttu-id="044d9-293">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="044d9-293">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="044d9-294">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-294">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="044d9-295">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="044d9-295">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="044d9-296">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="044d9-296">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="044d9-297">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="044d9-297">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="044d9-298">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="044d9-298">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="044d9-299">其他资源</span><span class="sxs-lookup"><span data-stu-id="044d9-299">Additional resources</span></span>

* [<span data-ttu-id="044d9-300">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-300">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="044d9-301">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="044d9-301">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="044d9-302">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="044d9-302">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end

::: moniker range="< aspnetcore-2.2"

<span data-ttu-id="044d9-303">分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。</span><span class="sxs-lookup"><span data-stu-id="044d9-303">A distributed cache is a cache shared by multiple app servers, typically maintained as an external service to the app servers that access it.</span></span> <span data-ttu-id="044d9-304">分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。</span><span class="sxs-lookup"><span data-stu-id="044d9-304">A distributed cache can improve the performance and scalability of an ASP.NET Core app, especially when the app is hosted by a cloud service or a server farm.</span></span>

<span data-ttu-id="044d9-305">与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。</span><span class="sxs-lookup"><span data-stu-id="044d9-305">A distributed cache has several advantages over other caching scenarios where cached data is stored on individual app servers.</span></span>

<span data-ttu-id="044d9-306">当分布式缓存数据时，数据将：</span><span class="sxs-lookup"><span data-stu-id="044d9-306">When cached data is distributed, the data:</span></span>

* <span data-ttu-id="044d9-307">跨多个服务器的请求具有*连贯*（一致）。</span><span class="sxs-lookup"><span data-stu-id="044d9-307">Is *coherent* (consistent) across requests to multiple servers.</span></span>
* <span data-ttu-id="044d9-308">置服务器重启和应用部署。</span><span class="sxs-lookup"><span data-stu-id="044d9-308">Survives server restarts and app deployments.</span></span>
* <span data-ttu-id="044d9-309">不使用本地内存。</span><span class="sxs-lookup"><span data-stu-id="044d9-309">Doesn't use local memory.</span></span>

<span data-ttu-id="044d9-310">分布式缓存配置是特定于实现的。</span><span class="sxs-lookup"><span data-stu-id="044d9-310">Distributed cache configuration is implementation specific.</span></span> <span data-ttu-id="044d9-311">本文介绍如何配置 SQL Server 和 Redis 分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-311">This article describes how to configure SQL Server and Redis distributed caches.</span></span> <span data-ttu-id="044d9-312">第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。</span><span class="sxs-lookup"><span data-stu-id="044d9-312">Third party implementations are also available, such as [NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache)).</span></span> <span data-ttu-id="044d9-313">无论选择哪种实现，应用都会使用接口与缓存交互 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 。</span><span class="sxs-lookup"><span data-stu-id="044d9-313">Regardless of which implementation is selected, the app interacts with the cache using the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface.</span></span>

<span data-ttu-id="044d9-314">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="044d9-314">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="044d9-315">先决条件</span><span class="sxs-lookup"><span data-stu-id="044d9-315">Prerequisites</span></span>

<span data-ttu-id="044d9-316">若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)</span><span class="sxs-lookup"><span data-stu-id="044d9-316">To use a SQL Server distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) or add a package reference to the [Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer) package.</span></span>

<span data-ttu-id="044d9-317">若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。</span><span class="sxs-lookup"><span data-stu-id="044d9-317">To use a Redis distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis) package.</span></span> <span data-ttu-id="044d9-318">Redis 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 Redis 包。</span><span class="sxs-lookup"><span data-stu-id="044d9-318">The Redis package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the Redis package separately in your project file.</span></span>

<span data-ttu-id="044d9-319">若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。</span><span class="sxs-lookup"><span data-stu-id="044d9-319">To use NCache distributed cache, reference the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) and add a package reference to the [NCache.Microsoft.Extensions.Caching.OpenSource](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource) package.</span></span> <span data-ttu-id="044d9-320">NCache 包不包含在包中 `Microsoft.AspNetCore.App` ，因此必须在项目文件中单独引用 NCache 包。</span><span class="sxs-lookup"><span data-stu-id="044d9-320">The NCache package isn't included in the `Microsoft.AspNetCore.App` package, so you must reference the NCache package separately in your project file.</span></span>

## <a name="idistributedcache-interface"></a><span data-ttu-id="044d9-321">IDistributedCache 接口</span><span class="sxs-lookup"><span data-stu-id="044d9-321">IDistributedCache interface</span></span>

<span data-ttu-id="044d9-322"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：</span><span class="sxs-lookup"><span data-stu-id="044d9-322">The <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface provides the following methods to manipulate items in the distributed cache implementation:</span></span>

* <span data-ttu-id="044d9-323"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>：接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。</span><span class="sxs-lookup"><span data-stu-id="044d9-323"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*>: Accepts a string key and retrieves a cached item as a `byte[]` array if found in the cache.</span></span>
* <span data-ttu-id="044d9-324"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>：使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-324"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>: Adds an item (as `byte[]` array) to the cache using a string key.</span></span>
* <span data-ttu-id="044d9-325"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>、 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ：根据项的键刷新缓存中的项，并重置其可调过期超时值（如果有）。</span><span class="sxs-lookup"><span data-stu-id="044d9-325"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*>: Refreshes an item in the cache based on its key, resetting its sliding expiration timeout (if any).</span></span>
* <span data-ttu-id="044d9-326"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>：根据缓存项的字符串键删除缓存项。</span><span class="sxs-lookup"><span data-stu-id="044d9-326"><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*>: Removes a cache item based on its string key.</span></span>

## <a name="establish-distributed-caching-services"></a><span data-ttu-id="044d9-327">建立分布式缓存服务</span><span class="sxs-lookup"><span data-stu-id="044d9-327">Establish distributed caching services</span></span>

<span data-ttu-id="044d9-328">在中注册的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-328">Register an implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> in `Startup.ConfigureServices`.</span></span> <span data-ttu-id="044d9-329">本主题中所述的框架提供的实现包括：</span><span class="sxs-lookup"><span data-stu-id="044d9-329">Framework-provided implementations described in this topic include:</span></span>

* [<span data-ttu-id="044d9-330">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-330">Distributed Memory Cache</span></span>](#distributed-memory-cache)
* [<span data-ttu-id="044d9-331">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-331">Distributed SQL Server cache</span></span>](#distributed-sql-server-cache)
* [<span data-ttu-id="044d9-332">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-332">Distributed Redis cache</span></span>](#distributed-redis-cache)
* [<span data-ttu-id="044d9-333">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-333">Distributed NCache cache</span></span>](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a><span data-ttu-id="044d9-334">分布式内存缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-334">Distributed Memory Cache</span></span>

<span data-ttu-id="044d9-335">分布式内存缓存（ <xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*> ）是一个框架提供的实现 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> ，它将项存储在内存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-335">The Distributed Memory Cache (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) is a framework-provided implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> that stores items in memory.</span></span> <span data-ttu-id="044d9-336">分布式内存缓存不是实际的分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-336">The Distributed Memory Cache isn't an actual distributed cache.</span></span> <span data-ttu-id="044d9-337">缓存项由应用程序实例存储在运行应用程序的服务器上。</span><span class="sxs-lookup"><span data-stu-id="044d9-337">Cached items are stored by the app instance on the server where the app is running.</span></span>

<span data-ttu-id="044d9-338">分布式内存缓存是一种有用的实现：</span><span class="sxs-lookup"><span data-stu-id="044d9-338">The Distributed Memory Cache is a useful implementation:</span></span>

* <span data-ttu-id="044d9-339">用于开发和测试方案。</span><span class="sxs-lookup"><span data-stu-id="044d9-339">In development and testing scenarios.</span></span>
* <span data-ttu-id="044d9-340">在生产环境中使用单一服务器并且内存消耗不是问题。</span><span class="sxs-lookup"><span data-stu-id="044d9-340">When a single server is used in production and memory consumption isn't an issue.</span></span> <span data-ttu-id="044d9-341">实现分布式内存缓存会抽象化缓存的数据存储。</span><span class="sxs-lookup"><span data-stu-id="044d9-341">Implementing the Distributed Memory Cache abstracts cached data storage.</span></span> <span data-ttu-id="044d9-342">如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。</span><span class="sxs-lookup"><span data-stu-id="044d9-342">It allows for implementing a true distributed caching solution in the future if multiple nodes or fault tolerance become necessary.</span></span>

<span data-ttu-id="044d9-343">当应用程序在的开发环境中运行时，示例应用程序会使用分布式内存缓存 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-343">The sample app makes use of the Distributed Memory Cache when the app is run in the Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a><span data-ttu-id="044d9-344">分布式 SQL Server 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-344">Distributed SQL Server Cache</span></span>

<span data-ttu-id="044d9-345">分布式 SQL Server 缓存实现（ <xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*> ）允许分布式缓存使用 SQL Server 数据库作为其后备存储。</span><span class="sxs-lookup"><span data-stu-id="044d9-345">The Distributed SQL Server Cache implementation (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) allows the distributed cache to use a SQL Server database as its backing store.</span></span> <span data-ttu-id="044d9-346">若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。</span><span class="sxs-lookup"><span data-stu-id="044d9-346">To create a SQL Server cached item table in a SQL Server instance, you can use the `sql-cache` tool.</span></span> <span data-ttu-id="044d9-347">该工具将创建一个表，其中包含指定的名称和架构。</span><span class="sxs-lookup"><span data-stu-id="044d9-347">The tool creates a table with the name and schema that you specify.</span></span>

<span data-ttu-id="044d9-348">通过运行命令在 SQL Server 中创建一个表 `sql-cache create` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-348">Create a table in SQL Server by running the `sql-cache create` command.</span></span> <span data-ttu-id="044d9-349">提供 SQL Server 实例（ `Data Source` ）、数据库（ `Initial Catalog` ）、架构（例如， `dbo` ）和表名（例如 `TestCache` ）：</span><span class="sxs-lookup"><span data-stu-id="044d9-349">Provide the SQL Server instance (`Data Source`), database (`Initial Catalog`), schema (for example, `dbo`), and table name (for example, `TestCache`):</span></span>

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

<span data-ttu-id="044d9-350">将记录一条消息，指示该工具已成功：</span><span class="sxs-lookup"><span data-stu-id="044d9-350">A message is logged to indicate that the tool was successful:</span></span>

```console
Table and index were created successfully.
```

<span data-ttu-id="044d9-351">该工具创建的表 `sql-cache` 具有以下架构：</span><span class="sxs-lookup"><span data-stu-id="044d9-351">The table created by the `sql-cache` tool has the following schema:</span></span>

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> <span data-ttu-id="044d9-353">应用应使用的实例 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （而不是）来处理缓存值 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 。</span><span class="sxs-lookup"><span data-stu-id="044d9-353">An app should manipulate cache values using an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>, not a <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>.</span></span>

<span data-ttu-id="044d9-354">示例应用在 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> 中的非开发环境中实现 `Startup.ConfigureServices` ：</span><span class="sxs-lookup"><span data-stu-id="044d9-354">The sample app implements <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> in a non-Development environment in `Startup.ConfigureServices`:</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> <span data-ttu-id="044d9-355"><xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>（以及（可选 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*> ）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*appsettings 中存储） / *。环境} json*文件）。</span><span class="sxs-lookup"><span data-stu-id="044d9-355">A <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> (and optionally, <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> and <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) are typically stored outside of source control (for example, stored by the [Secret Manager](xref:security/app-secrets) or in *appsettings.json*/*appsettings.{ENVIRONMENT}.json* files).</span></span> <span data-ttu-id="044d9-356">连接字符串可能包含应保留在源代码管理系统之外的凭据。</span><span class="sxs-lookup"><span data-stu-id="044d9-356">The connection string may contain credentials that should be kept out of source control systems.</span></span>

### <a name="distributed-redis-cache"></a><span data-ttu-id="044d9-357">分布式 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-357">Distributed Redis Cache</span></span>

<span data-ttu-id="044d9-358">[Redis](https://redis.io/)是内存中数据存储的开源数据存储，通常用作分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-358">[Redis](https://redis.io/) is an open source in-memory data store, which is often used as a distributed cache.</span></span> <span data-ttu-id="044d9-359">可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-359">You can use Redis locally, and you can configure an [Azure Redis Cache](https://azure.microsoft.com/services/cache/) for an Azure-hosted ASP.NET Core app.</span></span>

<span data-ttu-id="044d9-360">应用使用实例（）配置缓存实现 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*> ：</span><span class="sxs-lookup"><span data-stu-id="044d9-360">An app configures the cache implementation using a <xref:Microsoft.Extensions.Caching.Redis.RedisCache> instance (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):</span></span>

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

<span data-ttu-id="044d9-361">若要在本地计算机上安装 Redis：</span><span class="sxs-lookup"><span data-stu-id="044d9-361">To install Redis on your local machine:</span></span>

1. <span data-ttu-id="044d9-362">安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-362">Install the [Chocolatey Redis package](https://chocolatey.org/packages/redis-64/).</span></span>
1. <span data-ttu-id="044d9-363">`redis-server`在命令提示符下运行。</span><span class="sxs-lookup"><span data-stu-id="044d9-363">Run `redis-server` from a command prompt.</span></span>

### <a name="distributed-ncache-cache"></a><span data-ttu-id="044d9-364">分布式 NCache 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-364">Distributed NCache Cache</span></span>

<span data-ttu-id="044d9-365">[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。</span><span class="sxs-lookup"><span data-stu-id="044d9-365">[NCache](https://github.com/Alachisoft/NCache) is an open source in-memory distributed cache developed natively in .NET and .NET Core.</span></span> <span data-ttu-id="044d9-366">NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="044d9-366">NCache works both locally and configured as a distributed cache cluster for an ASP.NET Core app running in Azure or on other hosting platforms.</span></span>

<span data-ttu-id="044d9-367">若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-367">To install and configure NCache on your local machine, see [NCache Getting Started Guide for Windows](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/).</span></span>

<span data-ttu-id="044d9-368">配置 NCache：</span><span class="sxs-lookup"><span data-stu-id="044d9-368">To configure NCache:</span></span>

1. <span data-ttu-id="044d9-369">安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。</span><span class="sxs-lookup"><span data-stu-id="044d9-369">Install [NCache open source NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/).</span></span>
1. <span data-ttu-id="044d9-370">在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。</span><span class="sxs-lookup"><span data-stu-id="044d9-370">Configure the cache cluster in [client.ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html).</span></span>
1. <span data-ttu-id="044d9-371">将下列代码添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="044d9-371">Add the following code to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a><span data-ttu-id="044d9-372">使用分布式缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-372">Use the distributed cache</span></span>

<span data-ttu-id="044d9-373">若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 从应用程序中的任何构造函数请求的实例。</span><span class="sxs-lookup"><span data-stu-id="044d9-373">To use the <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> interface, request an instance of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> from any constructor in the app.</span></span> <span data-ttu-id="044d9-374">实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。</span><span class="sxs-lookup"><span data-stu-id="044d9-374">The instance is provided by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

<span data-ttu-id="044d9-375">示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 将插入到中 `Startup.Configure` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-375">When the sample app starts, <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is injected into `Startup.Configure`.</span></span> <span data-ttu-id="044d9-376">使用缓存当前时间 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> （有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：</span><span class="sxs-lookup"><span data-stu-id="044d9-376">The current time is cached using <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> (for more information, see [Web Host: IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

<span data-ttu-id="044d9-377">示例应用将注入 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 到中 `IndexModel` 供索引页使用。</span><span class="sxs-lookup"><span data-stu-id="044d9-377">The sample app injects <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> into the `IndexModel` for use by the Index page.</span></span>

<span data-ttu-id="044d9-378">每次加载索引页时，都会在中检查缓存时间的缓存 `OnGetAsync` 。</span><span class="sxs-lookup"><span data-stu-id="044d9-378">Each time the Index page is loaded, the cache is checked for the cached time in `OnGetAsync`.</span></span> <span data-ttu-id="044d9-379">如果缓存的时间未过期，则会显示时间。</span><span class="sxs-lookup"><span data-stu-id="044d9-379">If the cached time hasn't expired, the time is displayed.</span></span> <span data-ttu-id="044d9-380">如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。</span><span class="sxs-lookup"><span data-stu-id="044d9-380">If 20 seconds have elapsed since the last time the cached time was accessed (the last time this page was loaded), the page displays *Cached Time Expired*.</span></span>

<span data-ttu-id="044d9-381">通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。</span><span class="sxs-lookup"><span data-stu-id="044d9-381">Immediately update the cached time to the current time by selecting the **Reset Cached Time** button.</span></span> <span data-ttu-id="044d9-382">按钮触发 `OnPostResetCachedTime` 处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="044d9-382">The button triggers the `OnPostResetCachedTime` handler method.</span></span>

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> <span data-ttu-id="044d9-383">对于实例，无需使用单独的或作用域生存期 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> （至少对于内置实现）。</span><span class="sxs-lookup"><span data-stu-id="044d9-383">There's no need to use a Singleton or Scoped lifetime for <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instances (at least for the built-in implementations).</span></span>
>
> <span data-ttu-id="044d9-384">您还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="044d9-384">You can also create an <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> instance wherever you might need one instead of using DI, but creating an instance in code can make your code harder to test and violates the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

## <a name="recommendations"></a><span data-ttu-id="044d9-385">建议</span><span class="sxs-lookup"><span data-stu-id="044d9-385">Recommendations</span></span>

<span data-ttu-id="044d9-386">确定 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 最适合你的应用的实现时，请考虑以下事项：</span><span class="sxs-lookup"><span data-stu-id="044d9-386">When deciding which implementation of <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> is best for your app, consider the following:</span></span>

* <span data-ttu-id="044d9-387">现有基础结构</span><span class="sxs-lookup"><span data-stu-id="044d9-387">Existing infrastructure</span></span>
* <span data-ttu-id="044d9-388">性能要求</span><span class="sxs-lookup"><span data-stu-id="044d9-388">Performance requirements</span></span>
* <span data-ttu-id="044d9-389">成本</span><span class="sxs-lookup"><span data-stu-id="044d9-389">Cost</span></span>
* <span data-ttu-id="044d9-390">团队体验</span><span class="sxs-lookup"><span data-stu-id="044d9-390">Team experience</span></span>

<span data-ttu-id="044d9-391">缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。</span><span class="sxs-lookup"><span data-stu-id="044d9-391">Caching solutions usually rely on in-memory storage to provide fast retrieval of cached data, but memory is a limited resource and costly to expand.</span></span> <span data-ttu-id="044d9-392">仅将常用数据存储在缓存中。</span><span class="sxs-lookup"><span data-stu-id="044d9-392">Only store commonly used data in a cache.</span></span>

<span data-ttu-id="044d9-393">通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。</span><span class="sxs-lookup"><span data-stu-id="044d9-393">Generally, a Redis cache provides higher throughput and lower latency than a SQL Server cache.</span></span> <span data-ttu-id="044d9-394">但是，通常需要进行基准测试来确定缓存策略的性能特征。</span><span class="sxs-lookup"><span data-stu-id="044d9-394">However, benchmarking is usually required to determine the performance characteristics of caching strategies.</span></span>

<span data-ttu-id="044d9-395">当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。</span><span class="sxs-lookup"><span data-stu-id="044d9-395">When SQL Server is used as a distributed cache backing store, use of the same database for the cache and the app's ordinary data storage and retrieval can negatively impact the performance of both.</span></span> <span data-ttu-id="044d9-396">建议使用分布式缓存后备存储的专用 SQL Server 实例。</span><span class="sxs-lookup"><span data-stu-id="044d9-396">We recommend using a dedicated SQL Server instance for the distributed cache backing store.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="044d9-397">其他资源</span><span class="sxs-lookup"><span data-stu-id="044d9-397">Additional resources</span></span>

* [<span data-ttu-id="044d9-398">Azure 上的 Redis 缓存</span><span class="sxs-lookup"><span data-stu-id="044d9-398">Redis Cache on Azure</span></span>](/azure/azure-cache-for-redis/)
* [<span data-ttu-id="044d9-399">Azure 上的 SQL 数据库</span><span class="sxs-lookup"><span data-stu-id="044d9-399">SQL Database on Azure</span></span>](/azure/sql-database/)
* <span data-ttu-id="044d9-400">[Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）</span><span class="sxs-lookup"><span data-stu-id="044d9-400">[ASP.NET Core IDistributedCache Provider for NCache in Web Farms](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([NCache on GitHub](https://github.com/Alachisoft/NCache))</span></span>
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>

::: moniker-end
 