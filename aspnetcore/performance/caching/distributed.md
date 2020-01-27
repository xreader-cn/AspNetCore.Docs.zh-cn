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
# <a name="distributed-caching-in-aspnet-core"></a>ASP.NET Core 中的分布式缓存

作者： [Luke Latham](https://github.com/guardrex)、 [Mohsin Nasir](https://github.com/mohsinnasir)和[Steve Smith](https://ardalis.com/)

分布式缓存是由多个应用服务器共享的缓存，通常作为外部服务在访问它的应用服务器上维护。 分布式缓存可以提高 ASP.NET Core 应用程序的性能和可伸缩性，尤其是在应用程序由云服务或服务器场托管时。

与其他缓存方案相比，分布式缓存具有多项优势，其中缓存的数据存储在单个应用服务器上。

当分布式缓存数据时，数据将：

* 跨多个服务器的请求具有*连贯*（一致）。
* 置服务器重启和应用部署。
* 不使用本地内存。

分布式缓存配置是特定于实现的。 本文介绍如何配置 SQL Server 和 Redis 分布式缓存。 第三方实现也可用，例如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) （[GitHub 上的 NCache](https://github.com/Alachisoft/NCache)）。 无论选择哪种实现，应用都使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口与缓存交互。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>先决条件

::: moniker range=">= aspnetcore-3.0"

若要使用 SQL Server 分布式缓存，请[将包引用添加到 "](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分布式缓存，请将包引用添加到[StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。

若要使用 NCache 分布式缓存，请将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包。

::: moniker-end

::: moniker range="= aspnetcore-2.2"

若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)添加到包。 Redis 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 Redis 包。

若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。 NCache 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 NCache 包。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

若要使用 SQL Server 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，或添加对[包的包引用。](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)

若要使用 Redis 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并[将包引用](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)添加到包。 Redis 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 Redis 包。

若要使用 NCache 分布式缓存，请参考[AspNetCore 元包](xref:fundamentals/metapackage-app)，并将包引用添加到[NCache](https://www.nuget.org/packages/NCache.Microsoft.Extensions.Caching.OpenSource)包中。 NCache 包不包含在 `Microsoft.AspNetCore.App` 包中，因此必须在项目文件中单独引用 NCache 包。

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache 接口

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口提供以下方法来处理分布式缓存实现中的项：

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash; 接受字符串键，并检索缓存项作为 `byte[]` 数组（如果在缓存中找到）。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash; 使用字符串键将项（作为 `byte[]` 数组）添加到缓存中。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*>中，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash; 基于其键刷新缓存中的项，并重置其可调过期超时值（如果有）。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*>，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash; 会根据其字符串键删除缓存项。

## <a name="establish-distributed-caching-services"></a>建立分布式缓存服务

在 `Startup.ConfigureServices`中注册 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现。 本主题中所述的框架提供的实现包括：

* [分布式内存缓存](#distributed-memory-cache)
* [分布式 SQL Server 缓存](#distributed-sql-server-cache)
* [分布式 Redis 缓存](#distributed-redis-cache)
* [分布式 NCache 缓存](#distributed-ncache-cache)

### <a name="distributed-memory-cache"></a>分布式内存缓存

分布式内存缓存（<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>）是一个框架提供的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实现，用于将项存储在内存中。 分布式内存缓存不是实际的分布式缓存。 缓存项由应用程序实例存储在运行应用程序的服务器上。

分布式内存缓存是一种有用的实现：

* 用于开发和测试方案。
* 在生产环境中使用单一服务器并且内存消耗不是问题。 实现分布式内存缓存会抽象化缓存的数据存储。 如果需要多个节点或容错，可以在将来实现真正的分布式缓存解决方案。

当应用程序在 `Startup.ConfigureServices`的开发环境中运行时，示例应用程序会使用分布式内存缓存：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

::: moniker-end

### <a name="distributed-sql-server-cache"></a>分布式 SQL Server 缓存

分布式 SQL Server 缓存实现（<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>）允许分布式缓存使用 SQL Server 数据库作为其后备存储。 若要在 SQL Server 实例中创建 SQL Server 缓存的项表，可以使用 `sql-cache` 工具。 该工具将创建一个表，其中包含指定的名称和架构。

通过运行 `sql-cache create` 命令在 SQL Server 中创建一个表。 提供 SQL Server 实例（`Data Source`）、数据库（`Initial Catalog`）、架构（例如 `dbo`）和表名（例如，`TestCache`）：

```dotnetcli
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

将记录一条消息，指示该工具已成功：

```console
Table and index were created successfully.
```

`sql-cache` 工具创建的表具有以下架构：

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 应用应使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>的实例而不是 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>来操作缓存值。

示例应用在 `Startup.ConfigureServices`的非开发环境中实施 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>：

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

::: moniker-end

> [!NOTE]
> <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*> （并且可以选择 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*> 和 <xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>）通常存储在源代码管理之外（例如，由[机密管理器](xref:security/app-secrets)存储或在*appsettings*/appsettings 中存储） *。环境} json*文件）。 连接字符串可能包含应保留在源代码管理系统之外的凭据。

### <a name="distributed-redis-cache"></a>分布式 Redis 缓存

[Redis](https://redis.io/)是一种开源的内存中数据存储，通常用作分布式缓存。 可以在本地使用 Redis，也可以为 Azure 托管的 ASP.NET Core 应用配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)。

::: moniker range=">= aspnetcore-3.0"

应用使用 `Startup.ConfigureServices`的非开发环境中的 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>）配置缓存实现：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="= aspnetcore-2.2"

应用使用 `Startup.ConfigureServices`的非开发环境中的 <xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>）配置缓存实现：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

应用使用 <xref:Microsoft.Extensions.Caching.Redis.RedisCache> 实例（<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>）配置缓存实现：

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

若要在本地计算机上安装 Redis：

1. 安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。
1. 在命令提示符下运行 `redis-server`。

### <a name="distributed-ncache-cache"></a>分布式 NCache 缓存

[NCache](https://github.com/Alachisoft/NCache)是在 .NET 和 .net Core 中以本机方式开发的开源内存中分布式缓存。 NCache 在本地工作并配置为分布式缓存群集，适用于在 Azure 或其他托管平台上运行的 ASP.NET Core 应用。

若要在本地计算机上安装和配置 NCache，请参阅[适用于 Windows 的 NCache 入门指南](https://www.alachisoft.com/resources/docs/ncache-oss/getting-started-guide-windows/)。

配置 NCache：

1. 安装[NCache 开放源代码 NuGet](https://www.nuget.org/packages/Alachisoft.NCache.OpenSource.SDK/)。
1. 在[ncconf](https://www.alachisoft.com/resources/docs/ncache-oss/admin-guide/client-config.html)中配置缓存群集。
1. 将下列代码添加到 `Startup.ConfigureServices`：

   ```csharp
   services.AddNCacheDistributedCache(configuration =>    
   {        
       configuration.CacheName = "demoClusteredCache";
       configuration.EnableLogs = true;
       configuration.ExceptionsEnabled = true;
   });
   ```

## <a name="use-the-distributed-cache"></a>使用分布式缓存

若要使用 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 接口，请从应用程序中的任何构造函数请求 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 的实例。 实例通过[依赖关系注入（DI）](xref:fundamentals/dependency-injection)来提供。

::: moniker range=">= aspnetcore-3.0"

示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。 当前时间使用 <xref:Microsoft.Extensions.Hosting.IHostApplicationLifetime> 进行缓存（有关详细信息，请参阅[泛型 Host： IHostApplicationLifetime](xref:fundamentals/host/generic-host#ihostapplicationlifetime)）：

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

示例应用启动时，<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 被注入到 `Startup.Configure`中。 当前时间使用 <xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime> 进行缓存（有关详细信息，请参阅[Web Host： IApplicationLifetime interface](xref:fundamentals/host/web-host#iapplicationlifetime-interface)）：

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

::: moniker-end

示例应用将 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 插入到 `IndexModel` 中，供索引页使用。

每次加载索引页时，都会在 `OnGetAsync`中检查缓存时间的缓存。 如果缓存的时间未过期，则会显示时间。 如果自上次访问缓存时间之后经过了20秒（最后一次加载此页），则页面显示缓存的时间已*过期*。

通过选择 "**重置缓存时间**" 按钮立即将缓存的时间更新为当前时间。 按钮触发 `OnPostResetCachedTime` 处理程序方法。

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](distributed/samples/3.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

::: moniker-end

> [!NOTE]
> 无需为<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例使用 Singleton 或 Scoped 生命周期（至少对内置实现来说是这样的）。
>
> 你还可以创建一个 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实例，而不必使用 DI，而是使用代码创建一个实例，从而使代码更难以测试，并违反了[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。

## <a name="recommendations"></a>建议

确定最适合你的应用的 <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache> 实现时，请考虑以下事项：

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
