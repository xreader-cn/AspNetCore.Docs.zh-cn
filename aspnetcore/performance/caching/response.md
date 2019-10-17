---
title: ASP.NET Core 中的响应缓存
author: rick-anderson
description: 了解如何通过响应缓存降低带宽要求和提高 ASP.NET Core 应用性能。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 10/15/2019
uid: performance/caching/response
ms.openlocfilehash: 4ebac97689347245d25e0954b33729d78dd1b516
ms.sourcegitcommit: dd026eceee79e943bd6b4a37b144803b50617583
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/15/2019
ms.locfileid: "72378836"
---
# <a name="response-caching-in-aspnet-core"></a>ASP.NET Core 中的响应缓存

作者： [John Luo](https://github.com/JunTaoLuo)、 [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Steve Smith](https://ardalis.com/)和[Luke Latham](https://github.com/guardrex)

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/response/samples)（[如何下载](xref:index#how-to-download-a-sample)）

响应缓存可减少客户端或代理对 web 服务器发出的请求数。 响应缓存还减少了 web 服务器生成响应所需的工作量。 响应缓存由指定你希望客户端、代理和中间件缓存响应的方式的标头控制。

[ResponseCache 属性](#responsecache-attribute)参与设置响应缓存标头，客户端在缓存响应时可能会服从这些标头。 [响应缓存中间件](xref:performance/caching/middleware)可用于在服务器上缓存响应。 中间件可以使用 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 属性来影响服务器端的缓存行为。

## <a name="http-based-response-caching"></a>基于 HTTP 的响应缓存

[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234)描述了 Internet 缓存的行为方式。 用于缓存的主 HTTP 标头是[缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2)，它用于指定缓存*指令*。 指令控制缓存行为作为请求从客户端发送到服务器，而作为响应，使其从服务器到客户端的方式。 请求和响应在代理服务器之间移动，并且代理服务器还必须符合 HTTP 1.1 缓存规范。

下表显示了常见 @no__t 0 指令。

| 指令                                                       | 操作 |
| --------------------------------------------------------------- | ------ |
| [public](https://tools.ietf.org/html/rfc7234#section-5.2.2.5)   | 缓存可以存储响应。 |
| [private](https://tools.ietf.org/html/rfc7234#section-5.2.2.6)  | 共享缓存不能存储响应。 专用缓存可以存储和重用响应。 |
| [最大期限](https://tools.ietf.org/html/rfc7234#section-5.2.1.1)  | 客户端不接受其期限大于指定秒数的响应。 示例： `max-age=60` （60秒），`max-age=2592000` （1个月） |
| [非缓存](https://tools.ietf.org/html/rfc7234#section-5.2.1.4) | **请求时**：缓存不能使用存储的响应来满足请求。 源服务器重新生成客户端的响应，中间件更新其缓存中存储的响应。<br><br>**响应：在**源服务器上没有验证的后续请求不得使用响应。 |
| [无-商店](https://tools.ietf.org/html/rfc7234#section-5.2.1.5) | **请求时**：缓存不能存储请求。<br><br>**响应**：缓存不能存储响应的任何部分。 |

下表显示了在缓存中扮演角色的其他缓存标头。

| Header                                                     | 函数 |
| ---------------------------------------------------------- | -------- |
| [年](https://tools.ietf.org/html/rfc7234#section-5.1)     | 在源服务器上生成或成功验证响应以来的时间量（以秒为单位）。 |
| [完](https://tools.ietf.org/html/rfc7234#section-5.3) | 响应被视为过时的时间。 |
| [杂](https://tools.ietf.org/html/rfc7234#section-5.4)  | 存在，用于设置 `no-cache` 行为的向后兼容 HTTP/1.0 缓存。 如果 @no__t 的标头存在，则将忽略 @no__t 的标头。 |
| [大](https://tools.ietf.org/html/rfc7231#section-7.1.4)  | 指定不能发送缓存的响应，除非缓存响应的原始请求和新请求中的所有 `Vary` 标头字段都匹配。 |

## <a name="http-based-caching-respects-request-cache-control-directives"></a>基于 HTTP 的缓存遵循请求缓存控制指令

[缓存控制标头的 HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)要求使用缓存来服从客户端发送的有效 @no__t 1 标头。 客户端可以使用 @no__t 的标头值发出请求，并强制服务器为每个请求生成新的响应。

如果考虑到 HTTP 缓存的目标，则应始终考虑客户端 @no__t 0 请求标头。 在官方规范下，缓存旨在减少在客户端、代理和服务器网络中满足请求的延迟和网络开销。 它不一定是控制源服务器上的负载的一种方法。

使用[响应缓存中间件](xref:performance/caching/middleware)时，无开发人员对此缓存行为的控制，因为中间件遵循官方缓存规范。 [中间件的计划增强功能](https://github.com/aspnet/AspNetCore/issues/2612)是在决定为缓存的响应提供服务时，将中间件配置为忽略请求的 @no__t 1 标头的机会。 计划的增强功能提供了一种更好地控制服务器负载的机会。

## <a name="other-caching-technology-in-aspnet-core"></a>ASP.NET Core 中的其他缓存技术

### <a name="in-memory-caching"></a>内存中缓存

内存中缓存使用服务器内存来存储缓存的数据。 这种类型的缓存适用于单个服务器或使用*粘滞会话*的多台服务器。 粘滞会话表示来自客户端的请求始终路由到同一服务器进行处理。

有关更多信息，请参见<xref:performance/caching/memory>。

### <a name="distributed-cache"></a>分布式缓存

当应用程序托管在云或服务器场中时，使用分布式缓存将数据存储在内存中。 缓存在处理请求的服务器之间共享。 如果客户端的缓存数据可用，则客户端可以提交由组中的任何服务器处理的请求。 ASP.NET Core 提供 SQL Server 和 Redis 分布式缓存。

有关更多信息，请参见<xref:performance/caching/distributed>。

### <a name="cache-tag-helper"></a>缓存标记帮助程序

使用缓存标记帮助程序从 MVC 视图或 Razor 页面缓存内容。 缓存标记帮助程序使用内存中缓存来存储数据。

有关更多信息，请参见<xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>。

### <a name="distributed-cache-tag-helper"></a>分布式缓存标记帮助程序

使用分布式缓存标记帮助程序在分布式云和 web 场方案中，通过 MVC 视图或 Razor 页面缓存内容。 分布式缓存标记帮助程序使用 SQL Server 或 Redis 来存储数据。

有关更多信息，请参见<xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>。

## <a name="responsecache-attribute"></a>ResponseCache 特性

@No__t 指定在响应缓存中设置适当的标头所需的参数。

> [!WARNING]
> 禁用包含经过身份验证的客户端信息的内容的缓存。 只应为不会根据用户身份更改或用户是否已登录的内容启用缓存。

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 会根据给定的查询密钥列表值改变存储的响应。 当提供了 `*` 的单个值时，中间件将根据所有请求查询字符串参数来改变响应。

必须启用[响应缓存中间件](xref:performance/caching/middleware)才能设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByQueryKeys> 属性。 否则，会引发运行时异常。 @No__t-0 属性没有相应的 HTTP 标头。 属性是由响应缓存中间件处理的 HTTP 功能。 对于用于缓存响应的中间件，查询字符串和查询字符串值必须与上一个请求匹配。 例如，请考虑下表中显示的请求和结果的顺序。

| 请求                          | 结果                    |
| -------------------------------- | ------------------------- |
| `http://example.com?key1=value1` | 从服务器返回的。 |
| `http://example.com?key1=value1` | 从中间件返回。 |
| `http://example.com?key1=value2` | 从服务器返回的。 |

第一个请求由服务器返回，并缓存在中间件中。 第二个请求是由中间件返回的，因为查询字符串与上一个请求匹配。 第三个请求不在中间件缓存中，因为查询字符串值与以前的请求不匹配。

@No__t-0 用于配置和创建（通过 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>） `Microsoft.AspNetCore.Mvc.Internal.ResponseCacheFilter`。 @No__t-0 执行更新相应 HTTP 标头和响应功能的工作。 筛选器：

* 删除 `Vary`、`Cache-Control` 和 `Pragma` 的任何现有标头。
* 根据 <xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute> 中设置的属性写出适当的标头。
* 如果设置 @no__t，则更新响应缓存 HTTP 功能。

### <a name="vary"></a>大

仅当设置了 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 属性时才写入此标头。 属性设置为 @no__t 的属性值。 下面的示例使用 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.VaryByHeader> 属性：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache1.cshtml.cs?name=snippet)]

使用示例应用程序，使用浏览器的网络工具查看响应标头。 以下响应标头随 Cache1 页响应一起发送：

```
Cache-Control: public,max-age=30
Vary: User-Agent
```

### <a name="nostore-and-locationnone"></a>NoStore 和 Location。 None

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 替代其他所有属性。 如果将此属性设置为 `true`，则 @no__t 的标头将设置为 `no-store`。 如果 @no__t 设置为 `None`：

* 将 `Cache-Control` 设置为 `no-store,no-cache`。
* 将 `Pragma` 设置为 `no-cache`。

如果 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> @no__t 为-1，<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 为 `None`、`Cache-Control`，并且 @no__t 设置为 `no-cache`。

<xref:Microsoft.AspNetCore.Mvc.CacheProfile.NoStore> 通常设置为 @no__t 错误页的-1。 示例应用中的 Cache2 页面生成响应标头，以指示客户端不存储响应。

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache2.cshtml.cs?name=snippet)]

示例应用返回具有以下标头的 Cache2 页：

```
Cache-Control: no-store,no-cache
Pragma: no-cache
```

### <a name="location-and-duration"></a>位置和持续时间

若要启用缓存，必须将 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 设置为正值，<xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 必须是 `Any` （默认值）或 @no__t 为3。 在这种情况下，@no__t 的标头设置为位置值，后跟响应的 `max-age`。

> [!NOTE]
> @no__t 的 @no__t 的选项 @no__t，分别转换为 `public` 和 @no__t 的 @no__t 3 个标头值。 如前文所述，将 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 设置为 `None` 会将 @no__t 2 和 3 @no__t 标头设置为 @no__t。

下面的示例演示示例应用中的 Cache3 页模型以及通过设置 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Duration> 并保留默认 <xref:Microsoft.AspNetCore.Mvc.CacheProfile.Location> 值而生成的标头：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache3.cshtml.cs?name=snippet)]

示例应用返回具有以下标头的 Cache3 页：

```
Cache-Control: public,max-age=10
```

### <a name="cache-profiles"></a>缓存配置文件

在 @no__t 中设置 MVC/Razor Pages 时，可以将缓存配置文件配置为选项，而不是在多个控制器操作属性上复制响应缓存设置。 在引用的缓存配置文件中找到的值将用作 @no__t 的默认值，并由特性上指定的任何属性重写。

设置缓存配置文件。 以下示例显示示例应用的 @no__t 中的30秒缓存配置文件：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Startup.cs?name=snippet1)]

示例应用的 Cache4 页面模型引用 `Default30` 缓存配置文件：

[!code-csharp[](response/samples/2.x/ResponseCacheSample/Pages/Cache4.cshtml.cs?name=snippet)]

@No__t-0 可应用于：

* Razor 页面处理程序（类） &ndash; 特性不能应用于处理程序方法。
* MVC 控制器（类）。
* MVC 操作（方法） @no__t 0 方法级特性覆盖类级特性中指定的设置。

Cache4 缓存配置文件将生成的标头应用到页 @no__t 响应：

```
Cache-Control: public,max-age=30
```

## <a name="additional-resources"></a>其他资源

* [在缓存中存储响应](https://tools.ietf.org/html/rfc7234#section-3)
* [缓存-控制](https://www.w3.org/Protocols/rfc2616/rfc2616-sec14.html#sec14.9)
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/middleware>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
