---
title: ASP.NET Core 中的分布式缓存
author: guardrex
description: 了解如何使用 ASP.NET Core 分布式缓存来改善应用程序的性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 08/27/2019
uid: performance/caching/distributed
ms.openlocfilehash: dbcdfcd07877fabfe6d18cd4d840b5597afa1afd
ms.sourcegitcommit: 215954a638d24124f791024c66fd4fb9109fd380
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/18/2019
ms.locfileid: "71081550"
---
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core 中的分布式缓存

作者： [Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)

分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。 分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。

与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。

当分布式缓存数据时，数据将：

* 跨多个服务器的请求具有*连贯*（一致）。
* 置服务器重启和应用部署。
* 不使用本地内存。

分布式缓存配置是特定于实现的。 本文介绍如何配置 SQL Server 和 Redis 分布式缓存。 第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。 无论选择哪种实现，应用都会使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口与缓存交互。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

::: moniker range=">= aspnetcore-3.0"

若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。 Redis 包不包含在`Microsoft.AspNetCore.App`包中，因此必须在项目文件中单独引用 Redis 包。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。 Redis 包不包含在`Microsoft.AspNetCore.App`包中，因此必须在项目文件中单独引用 Redis 包。

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache 接口

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供以下方法来处理分布式缓存实现中的项：

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>`byte[]` ， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> 接受字符串键，并在缓存中找到缓存项作为数组。&ndash;
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*>使用字符串键将项`byte[]` （作为数组）添加到缓存中&ndash; 。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> ，&ndash;基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> ，&ndash;基于其字符串键删除缓存项。

## <a name="establish-distributed-caching-services"></a>建立分布式缓存服务

在中<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `Startup.ConfigureServices`注册的实现。 本主题中所述的框架提供的实现包括：

* [分布式内存缓存](#distributed-memory-cache)
* [分布式 SQL Server 缓存](#distributed-sql-server-cache)
* [分布式 Redis 缓存](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>分布式内存缓存

分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实现，它将项存储在内存中。 分布式内存缓存不是实际的分布式缓存。 缓存项由应用程序实例存储在运行应用程序的服务器上。

分布式内存缓存是一种有用的实现：

* 用于开发和测试方案。
* 在生产环境中使用单一服务器并且内存消耗不是问题。 实现分布式内存缓存会抽象化缓存的数据存储。 如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。

当应用程序在的开发环境中`Startup.ConfigureServices`运行时，示例应用程序会使用分布式内存缓存：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a>分布式 SQL Server 缓存

分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。 若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用`sql-cache`工具。 该工具将创建一个表，其中包含指定的名称和架构。

通过运行`sql-cache create`命令在 SQL Server 中创建一个表。 提供 SQL Server 实例`Data Source`（）、数据库（`Initial Catalog`）、架构（例如， `dbo`）和表名（ `TestCache`例如）：

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

将记录一条消息，指示该工具已成功：

```console
Table and index were created successfully.
```

该`sql-cache`工具创建的表具有以下架构：

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 应用应使用的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例（而不是<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>）来处理缓存值。

示例应用在中<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache> `Startup.ConfigureServices`的非开发环境中实现：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> /（以及（可选<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理的外部（例如，由[机密管理器](xref:security/app-secrets)或*appsettings*appsettings 中存储）。 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> *环境} json*文件）。 连接字符串可能包含应保留在源代码管理系统之外的凭据。

### <a name="distributed-redis-cache"></a>分布式 Redis 缓存

[Redis](https://redis.io/)是一种开源的内存中数据存储，通常用作分布式缓存。 可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。

::: moniker range=">= aspnetcore-3.0"

应用使用中<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>的非开发环境中的实例（）`Startup.ConfigureServices`配置缓存实现：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

应用使用中<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> <xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>的非开发环境中的实例（）`Startup.ConfigureServices`配置缓存实现：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

应用使用<xref:Microsoft.Extensions.Caching.Redis.RedisCache>实例（<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>）配置缓存实现：

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

若要在本地计算机上安装 Redis：

* 安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。
* 在`redis-server`命令提示符下运行。

## <a name="use-the-distributed-cache"></a>使用分布式缓存

若要使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口，请从应用程序<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>中的任何构造函数请求的实例。 实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。

::: moniker range=">= aspnetcore-3.0"

示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>将插入到中。 `Startup.Configure` 使用<xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime>缓存当前时间（有关详细信息，请参阅[泛型主机：IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)）：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

示例应用启动时， <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>将插入到中。 `Startup.Configure` 使用<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>缓存当前时间（有关详细信息，请参阅[Web 主机：IApplicationLifetime 接口](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

示例应用将注入<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> `IndexModel`到中供索引页使用。

每次加载索引页时，都会在中`OnGetAsync`检查缓存时间的缓存。 如果缓存的时间未过期，则会显示时间。 如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。

通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。 按钮触发`OnPostResetCachedTime`处理程序方法。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> 无需为<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例使用 Singleton 或 Scoped 生命周期（至少对内置实现来说是这样的）。
>
> 您还可以创建<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>一个实例，而不是使用 DI，而是在代码中创建一个实例，从而使代码更难以测试，并违反[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。

## <a name="recommendations"></a>建议

确定最适合你的<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>应用的实现时，请考虑以下事项：

* 现有基础结构
* 性能要求
* 成本
* 团队体验

缓存解决方案通常依赖于内存中的存储以快速检索缓存的数据，但是，内存是有限的资源，并且很昂贵。 仅将常用数据存储在缓存中。

通常，Redis 缓存提供比 SQL Server 缓存更高的吞吐量和更低的延迟。 但是，通常需要进行基准测试来确定缓存策略的性能特征。

当 SQL Server 用作分布式缓存后备存储时，对缓存使用同一数据库，并且应用的普通数据存储和检索会对这两种情况的性能产生负面影响。 建议使用分布式缓存后备存储的专用 SQL Server 实例。

## <a name="additional-resources"></a>其他资源

* [Azure 上的 Redis 缓存](/azure/azure-cache-for-redis/)
* [Azure 上的 SQL 数据库](/azure/sql-database/)
* [Web 场中的 NCache ASP.NET Core IDistributedCache 提供程序](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html)（[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
