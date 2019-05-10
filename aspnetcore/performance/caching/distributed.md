---
title: 分布式缓存在 ASP.NET Core 中
author: guardrex
description: 了解如何使用 ASP.NET Core 分布式缓存来提高应用性能和可伸缩性，尤其是在云或服务器场环境中。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/30/2019
uid: performance/caching/distributed
ms.openlocfilehash: 871e27c068de3a465cedb517418a35a87f992300
ms.sourcegitcommit: dd9c73db7853d87b566eef136d2162f648a43b85
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2019
ms.locfileid: "65085828"
---
# <a name="distributed-caching-in-aspnet-core"></a>分布式缓存在 ASP.NET Core 中

通过[Luke Latham](https://github.com/guardrex)和[Steve Smith](https://ardalis.com/)

分布式的缓存是由多个应用程序服务器，通常作为对其进行访问的应用程序服务器的外部服务维护共享缓存。 分布式的缓存可以提高性能和可伸缩性的 ASP.NET Core 应用，尤其是当应用程序托管的云服务或服务器场。

分布式的缓存具有几大优势，其中缓存的数据存储在单个应用程序服务器其他缓存方案。

当分布式缓存的数据，则数据：

* 是*连贯*（一致） 跨多个服务器的请求。
* 服务器重新启动和应用部署仍然有效。
* 不使用本地内存。

分布式的缓存配置是特定于实现的。 本文介绍如何配置 SQL Server 和 Redis 分布式的缓存。 第三方实现也是可用，如[NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([GitHub 上的 NCache](https://github.com/Alachisoft/NCache))。 无论选择哪一种实现，该应用程序与使用缓存进行交互<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/distributed/samples/)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="prerequisites"></a>系统必备

::: moniker range=">= aspnetcore-2.2"

若要使用的 SQL Server 分布式缓存，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或添加到的包引用[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)包。

若要使用 Redis 分布式缓存，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)并添加到的包引用[Microsoft.Extensions.Caching.StackExchangeRedis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.StackExchangeRedis)包。 Redis 包不包括在`Microsoft.AspNetCore.App`包，因此您必须在项目文件中分别引用 Redis 包。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

若要使用的 SQL Server 分布式缓存，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或添加到的包引用[Microsoft.Extensions.Caching.SqlServer](https://www.nuget.org/packages/Microsoft.Extensions.Caching.SqlServer)包。

若要使用 Redis 分布式缓存，引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)并添加到的包引用[Microsoft.Extensions.Caching.Redis](https://www.nuget.org/packages/Microsoft.Extensions.Caching.Redis)包。 Redis 包不包括在`Microsoft.AspNetCore.App`包，因此您必须在项目文件中分别引用 Redis 包。

::: moniker-end

## <a name="idistributedcache-interface"></a>IDistributedCache 接口

<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口提供了以下方法操作的分布式的缓存实现中的项：

* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Get*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.GetAsync*> &ndash;接受字符串键和检索缓存的项作为`byte[]`数组如果在缓存中找到。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Set*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.SetAsync*> &ndash;中添加项 (作为`byte[]`数组) 到使用字符串键的缓存。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Refresh*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RefreshAsync*> &ndash;刷新缓存基于其密钥，重置其滑动到期超时值 （如果有） 中的项。
* <xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.Remove*><xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache.RemoveAsync*> &ndash;移除缓存项根据其字符串键值。

## <a name="establish-distributed-caching-services"></a>建立分布式缓存服务

注册的实现<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>在`Startup.ConfigureServices`。 本主题中所述的框架提供实现包括：

* [分布式的内存缓存](#distributed-memory-cache)
* [SQL Server 的分布式的缓存](#distributed-sql-server-cache)
* [分布式的 Redis 缓存](#distributed-redis-cache)

### <a name="distributed-memory-cache"></a>分布式的内存缓存

分布式内存缓存 (<xref:Microsoft.Extensions.DependencyInjection.MemoryCacheServiceCollectionExtensions.AddDistributedMemoryCache*>) 是框架提供的实现<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>，在内存中存储项。 分布式内存缓存不是实际的分布式的缓存。 缓存的项存储在运行该应用程序的服务器上的应用程序实例。

分布式内存缓存是一个有用的实现：

* 在开发和测试方案。
* 生产和内存消耗情况中使用一台服务器时不会产生问题。 实现分布式内存缓存摘要缓存数据存储。 它允许实现真正的分布式缓存解决方案在将来如果多个节点或容错能力变得非常必要。

示例应用将在开发环境中运行应用时使用的分布式内存缓存`Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedMemoryCache)]

### <a name="distributed-sql-server-cache"></a>分布式的 SQL 服务器缓存

SQL Server 的分布式缓存实现 (<xref:Microsoft.Extensions.DependencyInjection.SqlServerCachingServicesExtensions.AddDistributedSqlServerCache*>) 允许使用 SQL Server 数据库作为其后备存储分布式的缓存。 若要在 SQL Server 实例中创建的 SQL Server 缓存的项表中，可以使用`sql-cache`工具。 该工具使用名称和指定的架构创建一个表。

通过运行 SQL Server 中创建一个表`sql-cache create`命令。 提供 SQL Server 实例 (`Data Source`)，数据库 (`Initial Catalog`)，架构 (例如， `dbo`)，以及表名 (例如， `TestCache`):

```console
dotnet sql-cache create "Data Source=(localdb)\MSSQLLocalDB;Initial Catalog=DistCache;Integrated Security=True;" dbo TestCache
```

记录一条消息以指示该工具已成功：

```console
Table and index were created successfully.
```

创建的表`sql-cache`工具具有以下架构：

![SqlServer 缓存表](distributed/_static/SqlServerCacheTable.png)

> [!NOTE]
> 应用应操作使用的实例的缓存值<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>，而不<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>。

本示例应用程序实现<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCache>中的非开发环境中`Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddDistributedSqlServerCache)]

> [!NOTE]
> 一个<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.ConnectionString*>(和 （可选）<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.SchemaName*>并<xref:Microsoft.Extensions.Caching.SqlServer.SqlServerCacheOptions.TableName*>) 通常存储在源代码管理之外 (例如，通过存储[机密管理器](xref:security/app-secrets)中或在*appsettings.json* /*appsettings。{ENVIRONMENT}.json*文件)。 连接字符串可能包含应从源代码管理系统的凭据。

### <a name="distributed-redis-cache"></a>分布式的 Redis 缓存

[Redis](https://redis.io/)是一种开源的内存中数据存储，通常用作分布式缓存。 您可以使用 Redis 本地，并且您可以配置[Azure Redis 缓存](https://azure.microsoft.com/services/cache/)Azure 托管 ASP.NET Core 应用。

::: moniker range=">= aspnetcore-2.2"

应用配置缓存实现使用<xref:Microsoft.Extensions.Caching.StackExchangeRedis.RedisCache>实例 (<xref:Microsoft.Extensions.DependencyInjection.StackExchangeRedisCacheServiceCollectionExtensions.AddStackExchangeRedisCache*>) 中的非开发环境中`Startup.ConfigureServices`:

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_AddStackExchangeRedisCache)]

::: moniker-end

::: moniker range="< aspnetcore-2.2"

应用配置缓存实现使用<xref:Microsoft.Extensions.Caching.Redis.RedisCache>实例 (<xref:Microsoft.Extensions.DependencyInjection.RedisCacheServiceCollectionExtensions.AddDistributedRedisCache*>):

```csharp
services.AddDistributedRedisCache(options =>
{
    options.Configuration = "localhost";
    options.InstanceName = "SampleInstance";
});
```

::: moniker-end

若要在本地计算机上安装 Redis:

* 安装[Chocolatey Redis 包](https://chocolatey.org/packages/redis-64/)。
* 运行`redis-server`从命令提示符。

## <a name="use-the-distributed-cache"></a>使用分布式的缓存

若要使用<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>接口，请求的实例<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>从任何应用程序中的构造函数。 实例由提供[依赖关系注入 (DI)](xref:fundamentals/dependency-injection)。

当应用启动时<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>注入到`Startup.Configure`。 使用缓存的当前时间<xref:Microsoft.AspNetCore.Hosting.IApplicationLifetime>(有关详细信息，请参阅[Web 主机：IApplicationLifetime 接口](xref:fundamentals/host/web-host#iapplicationlifetime-interface)):

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Startup.cs?name=snippet_Configure&highlight=10)]

示例应用程序注入<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>到`IndexModel`以供索引页。

每次加载索引页时，缓存时间检查缓存`OnGetAsync`。 如果尚未过期的缓存的时间，将显示时间。 如果自上一次访问缓存的时间 （已加载此页的最后一个时间） 已过去 20 秒，该页将显示*缓存时间已过*。

通过选择，立即更新为当前时间的缓存的时间**重置缓存时间**按钮。 按钮触发器`OnPostResetCachedTime`处理程序方法。

[!code-csharp[](distributed/samples/2.x/DistCacheSample/Pages/Index.cshtml.cs?name=snippet_IndexModel&highlight=7,14-20,25-29)]

> [!NOTE]
> 无需为<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例使用 Singleton 或 Scoped 生命周期（至少对内置实现来说是这样的）。
>
> 此外可以创建<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>实例可能需要某一个而不是使用 DI，但在代码中创建实例会使代码难以测试和违反[显式依赖关系原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。

## <a name="recommendations"></a>建议

确定哪一种实现的时<xref:Microsoft.Extensions.Caching.Distributed.IDistributedCache>最适合于您的应用程序，请考虑以下：

* 现有的基础结构
* 性能要求
* 成本
* 团队体验

缓存解决方案通常依赖于内存中存储提供快速检索的缓存数据，但内存是有限的资源，而且成本展开。 仅存储通常用于缓存中的数据。

通常情况下，Redis 缓存提供更高的吞吐量和延迟低于 SQL Server 缓存。 但是，进行基准测试时通常需要确定的缓存策略的性能特征。

当 SQL Server 用作分布式的缓存后备存储时，使用的同一个数据库缓存与应用程序的普通数据存储和检索可以对这两者的性能产生负面影响。 我们建议使用专用的 SQL Server 实例为分布式缓存后备存储。

## <a name="additional-resources"></a>其他资源

* [Redis 缓存在 Azure 上](/azure/azure-cache-for-redis/)
* [在 Azure 上的 SQL 数据库](/azure/sql-database/)
* [ASP.NET Core IDistributedCache 提供程序的 Web 场中的 NCache](http://www.alachisoft.com/ncache/aspnet-core-idistributedcache-ncache.html) ([在 GitHub 上的 NCache](https://github.com/Alachisoft/NCache))
* <xref:performance/caching/memory>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
* <xref:host-and-deploy/web-farm>
