---
title: 响应缓存在 ASP.NET Core
author: rick-anderson
description: 了解如何使用缓存到较低带宽要求的响应，并增加的 ASP.NET Core 应用的性能。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 02/28/2019
uid: performance/caching/response
ms.openlocfilehash: efcf443b1487827fe6cf4d43b6dda69adf4d61fb
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345741"
---
# <a name="response-caching-in-aspnet-core"></a>响应缓存在 ASP.NET Core

通过[John 卢奥语](https://github.com/JunTaoLuo)， [Rick Anderson](https://twitter.com/RickAndMSFT)， [Steve Smith](https://ardalis.com/)，和[Luke Latham](https://github.com/guardrex)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/response/samples)（[如何下载](xref:index#how-to-download-a-sample)）

响应缓存可减少客户端或代理到 web 服务器发出的请求数。 响应缓存还减少了工作的 web 服务器执行以生成响应。 响应缓存控制标头，指定要如何客户端、 代理和响应缓存中间件。

[ResponseCache 属性](#responsecache-attribute)参与设置缓存标头，哪些客户端可能会接受缓存的响应时的响应。 [响应缓存中间件](xref:performance/caching/middleware)可用于在服务器上的缓存响应。 可以使用中间件<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>属性，以改变服务器端的缓存行为。

## <a name="http-based-response-caching"></a>基于 HTTP 的响应缓存

[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)介绍 Internet 缓存的行为方式。 是用于缓存的主 HTTP 标头[Cache-control](https://tools.ietf.org/html/rfc7234#section-5.2)，用于指定缓存*指令*。 指令控制缓存行为，随着请求来自客户端对服务器进行自己的方式以及响应回客户端从服务器进行自己的方式。 请求和响应将通过代理服务器和代理服务器还必须符合 HTTP 1.1 缓存规范。

常见`Cache-Control`指令下表中所示。

| 指令                                                       | 操作 |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | 缓存可能会存储响应。 |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | 响应不是由共享缓存存储。 专用缓存可能会存储和重用响应。 |
| [max-age](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | 客户端不接受其年龄大于指定的秒数的响应。 示例：`max-age=60` （60 秒）， `max-age=2592000` （1 个月） |
| [no-cache](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **在请求**:缓存必须使用存储的响应来满足请求。 源服务器重新响应生成的客户端和中间件更新其缓存中存储的响应。<br><br>**响应**:响应不必须用于不带验证的源服务器上的后续请求。 |
| [no-store](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **在请求**:缓存不得存储请求。<br><br>**响应**:缓存不得存储任何响应的一部分。 |

下表中显示其他播放的角色中缓存的缓存标头。

| Header                                                     | 函数 |
| ---------------------------------------------------------- | -------- |
| [年龄](https://tools.ietf.org/html/rfc7234#section-5.1)     | 以秒为单位因为响应已生成或在源服务器已成功验证的时间量的估计值。 |
| [Expires](https://tools.ietf.org/html/rfc7234#section-5.3) | 过时的内容后，被视为响应的时间。 |
| [杂注](https://tools.ietf.org/html/rfc7234#section-5.4)  | 存在向后兼容性，使用 HTTP/1.0 缓存设置`no-cache`行为。 如果`Cache-Control`标头存在，则`Pragma`忽略标头。 |
| [改变](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | 指定缓存的响应必须不发送除非所有的`Vary`中缓存的响应的原始请求和新的请求标头字段匹配。 |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>基于 HTTP 的缓存方面请求缓存控制指令

[HTTP 1.1 缓存的缓存控制标头的规范](https://tools.ietf.org/html/rfc7234#section-5.2)需要接受是有效的缓存`Cache-Control`客户端发送的标头。 客户端可以发出请求的`no-cache`标头值和强制服务器生成的每个请求的新响应。

始终遵循客户端`Cache-Control`请求标头是有意义，如果您考虑 HTTP 缓存的目标。 在正式规范，缓存旨在减少的满足请求的客户端、 代理和服务器的网络延迟和网络开销。 它不一定是一种方法来控制源服务器上的负载。

没有任何开发人员可以控制此缓存的行为使用时[响应缓存中间件](xref:performance/caching/middleware)因为中间件遵循正式缓存规范。 [计划的中间件的增强功能](https://github.com/aspnet/AspNetCore/issues/2612)是配置要忽略的请求的中间件的机会`Cache-Control`标头决定用于缓存的响应时。 计划内的增强功能提供更好地控制服务器负载的机会。

## <a name="other-caching-technology-in-aspnet-core"></a>在 ASP.NET Core 中其他缓存技术

### <a name="in-memory-caching"></a>内存中缓存

内存中缓存使用的服务器内存来存储缓存的数据。 此类型的缓存是适用于单个服务器或多个服务器使用*粘性会话*。 粘性会话意味着，客户端的请求始终路由到同一个服务器进行处理。

有关详细信息，请参阅 <xref:performance/caching/memory>。

### <a name="distributed-cache"></a>分布式缓存

使用分布式的缓存在云或服务器场中托管应用时，将数据存储在内存中。 处理请求的服务器之间共享缓存。 客户端可以提交的请求，如果客户端的缓存的数据可由任何组中的服务器。 ASP.NET Core 提供 SQL Server 和分布式的 Redis 缓存。

有关详细信息，请参阅 <xref:performance/caching/distributed>。

### <a name="cache-tag-helper"></a>缓存标记帮助程序

与缓存标记帮助程序缓存中的 MVC 视图或 Razor 页面的内容。 缓存标记帮助程序使用内存中缓存来存储数据。

有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>。

### <a name="distributed-cache-tag-helper"></a>分布式缓存标记帮助程序

使用分布式缓存标记帮助程序缓存中的 MVC 视图或 Razor 页面在分布式云或 web 场方案中的内容。 分布式缓存标记帮助程序使用 SQL Server 或 Redis 存储数据。

有关详细信息，请参阅 <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>。

## <a name="responsecache-attribute"></a>ResponseCache 属性

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>指定缓存响应中设置相应的标头所需的参数。

> [!WARNING]
> 禁用缓存的内容，其中包含已经过身份验证的客户端的信息。 仅应为不会更改基于用户的标识或是否在用户登录的内容启用缓存。

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 存储的响应因查询密钥的指定列表的值。 时的单个值`*`是所有的响应请求查询字符串参数提供中间件会有所不同。

[响应缓存中间件](xref:performance/caching/middleware)必须能够设置<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>属性。 否则，会引发运行时异常。 没有为相应的 HTTP 标头<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>属性。 该属性是由响应缓存中间件处理的 HTTP 功能。 中间件来提供缓存的响应，查询字符串和查询字符串值必须匹配上一个请求。 例如，考虑请求和下表中所示的结果的序列。

| 请求                          | 结果                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | 从服务器返回。 |
| `http://example.com?key1=value1` | 返回从中间件。 |
| `http://example.com?key1=value2` | 从服务器返回。 |

第一个请求将由服务器返回并缓存在中间件中。 因为查询字符串匹配上一个请求，中间件会返回第二个请求。 因为查询字符串值不匹配上一个请求，第三个请求不是中间件缓存中。

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>用于配置和创建 (通过<xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>) <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>。 <xref:Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter>执行更新的适当的 HTTP 标头和响应的功能的工作。 筛选器：

* 删除任何现有标头`Vary`， `Cache-Control`，和`Pragma`。
* 写出适当的标头中设置的属性上基于<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>。
* 更新缓存项 HTTP 功能中，如果响应<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys>设置。

### <a name="vary"></a>改变

此标头时仅写入<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader>属性设置。 将属性设置为`Vary`属性的值。 下面的示例使用<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader>属性：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

使用示例应用程序，查看浏览器的网络工具的响应标头。 以下响应标头是与 Cache1 页面响应一起发送：

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore 和 Location.None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 重写的大多数其他属性。 当此属性设置为`true`，则`Cache-Control`标头设置为`no-store`。 如果<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>设置为`None`:

* 将 `Cache-Control` 设置为 `no-store,no-cache`。
* 将 `Pragma` 设置为 `no-cache`。

如果<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore>是`false`并<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>是`None`， `Cache-Control`，并且`Pragma`设置为`no-cache`。

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 通常设置为`true`错误页。 示例应用程序中的 Cache2 页面生成响应标头，指示客户端不存储响应。

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

示例应用程序返回 Cache2 页面包含以下标头：

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>位置和持续时间

若要启用缓存，<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>必须设置为正值和<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>必须是`Any`（默认值） 或`Client`。 在这种情况下，`Cache-Control`标头设置为位置值后, 接`max-age`的响应。

> [!NOTE]
> <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>选项`Any`并`Client`转换为`Cache-Control`标头值的`public`和`private`分别。 按前面所述，设置<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>到`None`设置同时`Cache-Control`并`Pragma`标头`no-cache`。

下面的示例演示 Cache3 页面模型从示例应用和通过如下设置生成的标头<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration>并保留默认值<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location>值：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

示例应用程序返回 Cache3 页面包含以下标头：

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>缓存配置文件

而不是重复响应缓存设置在多个控制器操作属性，设置 MVC/Razor 页面时可作为选项配置缓存配置文件`Startup.ConfigureServices`。 引用的缓存配置文件中找到的值用作默认值由<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>和由属性指定任何属性重写。

设置缓存配置文件。 下面的示例演示 30 的第二个缓存配置文件在示例应用的`Startup.ConfigureServices`:

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

示例应用 Cache4 页面模型引用`Default30`缓存配置文件：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute>可应用于：

* Razor 页面处理程序 （类）&ndash;属性不能应用于处理程序方法。
* MVC 控制器 （类）。
* MVC 操作 （方法）&ndash;方法级属性重写在类级别特性中指定的设置。

生成的标头应用到由该 Cache4 页响应`Default30`缓存配置文件：

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>其他资源

* [在缓存中存储的响应](https://tools.ietf.org/html/rfc7234#section-3)
* [Cache-Control](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
