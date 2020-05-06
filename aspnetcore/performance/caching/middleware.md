---
title: ASP.NET Core 中的响应缓存中间件
author: rick-anderson
description: 了解如何在 ASP.NET Core 中配置和使用响应缓存中间件。
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
uid: performance/caching/middleware
ms.openlocfilehash: 360b85d642ad3104d2662a38acd8c86e3c56f292
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775045"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>ASP.NET Core 中的响应缓存中间件

作者：[John Luo](https://github.com/JunTaoLuo)

::: moniker range=">= aspnetcore-3.0"

本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。 中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。 有关 HTTP 缓存和[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性的介绍，请参阅[响应缓存](xref:performance/caching/response)。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="configuration"></a>配置

响应缓存中间件可通过共享框架隐式地用于 ASP.NET Core 应用。

在`Startup.ConfigureServices`中，将响应缓存中间件添加到服务集合：

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

将应用程序配置为将中间件与<xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>扩展方法一起使用，该扩展方法将中间件添加到`Startup.Configure`中的请求处理管道：

[!code-csharp[](middleware/samples/3.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=16)]

示例应用添加标头以在后续请求时控制缓存：

* [缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;将可缓存的响应缓存多达10秒。
* 如果后续请求的接受编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配，则[Vary 会](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash;将中间件配置为提供缓存的响应。

[!code-csharp[](middleware/samples_snippets/3.x/AddHeaders.cs)]

响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。 中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。

> [!WARNING]
> 包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。 有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。

## <a name="options"></a>选项

响应缓存选项如下表中所示。

| 选项 | 说明 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | 响应正文的最大可缓存大小（以字节为单位）。 默认值为`64 * 1024 * 1024` （64 MB）。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 响应缓存中间件的大小限制（以字节为单位）。 默认值为`100 * 1024 * 1024` （100 MB）。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 确定是否将响应缓存在区分大小写的路径上。 默认值为 `false`。 |

下面的示例将中间件配置为：

* 大小小于或等于1024字节的缓存响应。
* 将响应存储为区分大小写的路径。 例如， `/page1`和`/Page1`分别存储。

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

使用 MVC/web API 控制器或 Razor Pages 页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性指定为响应缓存设置适当的标头所需的参数。 严格要求中间件的`[ResponseCache]`属性的唯一参数是<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>，它不对应于实际 HTTP 标头。 有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。

如果不使用`[ResponseCache]`属性，响应缓存可能会随而变化`VaryByQueryKeys`。 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

使用等于`*`中`VaryByQueryKeys`的单个值会根据所有请求查询参数改变缓存。

## <a name="http-headers-used-by-response-caching-middleware"></a>响应缓存中间件使用的 HTTP 标头

下表提供了有关影响响应缓存的 HTTP 标头的信息。

| 标头 | 详细信息 |
| ------ | ------- |
| `Authorization` | 如果标头存在，则不会缓存响应。 |
| `Cache-Control` | 中间件仅考虑用`public`缓存指令标记的缓存响应。 具有以下参数的控件缓存：<ul><li>最大期限</li><li>最大过期&#8224;</li><li>最小-新</li><li>must-revalidate</li><li>no-cache</li><li>无-商店</li><li>仅限-缓存</li><li>private</li><li>public</li><li>s-maxage</li><li>代理重新验证&#8225;</li></ul>&#8224;如果未指定任何限制`max-stale`，则中间件不会执行任何操作。<br>&#8225;`proxy-revalidate`的效果与相同`must-revalidate`。<br><br>有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。 |
| `Pragma` | 请求`Pragma: no-cache`中的标头将产生与相同的`Cache-Control: no-cache`效果。 `Cache-Control`标头中的相关指令（如果存在）将重写此标头。 考虑向后兼容 HTTP/1.0。 |
| `Set-Cookie` | 如果标头存在，则不会缓存响应。 请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。  |
| `Vary` | `Vary`标头用于根据另一个标头改变缓存的响应。 例如，通过包含标头来缓存响应， `Vary: Accept-Encoding`此标头将使用标头`Accept-Encoding: gzip`和`Accept-Encoding: text/plain`单独的请求来缓存响应。 永远不会存储标头值`*`为的响应。 |
| `Expires` | 除非由其他`Cache-Control`标头重写，否则不会存储或检索此标头过时的响应。 |
| `If-None-Match` | 如果值不`*`为，并且`ETag`响应的与提供的任何值都不匹配，则将从缓存中提供完整响应。 否则，将提供304（未修改）响应。 |
| `If-Modified-Since` | 如果`If-None-Match`标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。 否则，将提供*304-未修改*响应。 |
| `Date` | 从缓存提供时，如果`Date`未在原始响应中提供标头，中间件将设置标头。 |
| `Content-Length` | 从缓存提供时，如果`Content-Length`未在原始响应中提供标头，中间件将设置标头。 |
| `Age` | 忽略`Age`原始响应中发送的标头。 中间件在为缓存的响应提供服务时计算一个新值。 |

## <a name="caching-respects-request-cache-control-directives"></a>缓存遵从请求缓存控制指令

中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。 规则要求使用缓存来服从客户端发送`Cache-Control`的有效标头。 在规范下，客户端可以使用`no-cache`标头值发出请求，并强制服务器为每个请求生成新的响应。 目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。

为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。 请参阅下列主题：

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>疑难解答

如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。 检查请求的传入标头和响应的传出标头。 启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。

在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。 例如，浏览器可以在刷新页面`Cache-Control`时将`no-cache`标题`max-age=0`设置为或。 以下工具可以显式设置请求标头，并优先于测试缓存：

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>缓存条件

* 请求必须导致服务器响应，状态代码为200（正常）。
* 请求方法必须为 GET 或 HEAD。
* 在`Startup.Configure`中，必须将响应缓存中间件置于需要缓存的中间件之前。 有关详细信息，请参阅 <xref:fundamentals/middleware/index>。
* `Authorization`标题不得存在。
* `Cache-Control`标头参数必须是有效的，并且响应必须标记`public`且未标记`private`。
* 如果`Pragma: no-cache` `Cache-Control`标头不存在，则标头不得出现，因为`Cache-Control`标头会在`Pragma`存在时覆盖标头。
* `Set-Cookie`标题不得存在。
* `Vary`标头参数必须有效并且不等于`*`。
* `Content-Length`标头值（如果已设置）必须与响应正文的大小匹配。
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。
* `Expires`标头和`max-age`和`s-maxage`缓存指令指定的响应不能过时。
* 响应缓冲必须成功。 响应的大小必须小于配置的或默认值<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>。 响应的正文大小必须小于配置的或默认值<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>。
* 必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。 例如， `no-store`指令在请求或响应标头字段中不得存在。 有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。

> [!NOTE]
> 用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将`Cache-Control`和`Pragma`标头设置为`no-cache` ，以便不缓存响应。 有关如何为 HTML 窗体元素禁用防伪标记的信息，请<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>参阅。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

本文介绍如何在 ASP.NET Core 应用程序中配置响应缓存中间件。 中间件确定响应何时可缓存、存储响应，并提供来自缓存的响应。 有关 HTTP 缓存和[`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性的介绍，请参阅[响应缓存](xref:performance/caching/response)。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="configuration"></a>配置

使用[AspNetCore 元包](xref:fundamentals/metapackage-app)或添加对[AspNetCore. ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包的包引用。

在`Startup.ConfigureServices`中，将响应缓存中间件添加到服务集合：

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=3)]

将应用程序配置为将中间件与<xref:Microsoft.AspNetCore.Builder.ResponseCachingExtensions.UseResponseCaching*>扩展方法一起使用，该扩展方法将中间件添加到`Startup.Configure`中的请求处理管道：

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=14)]

示例应用添加标头以在后续请求时控制缓存：

* [缓存控制](https://tools.ietf.org/html/rfc7234#section-5.2) &ndash;将可缓存的响应缓存多达10秒。
* 如果后续请求的接受编码标头与原始请求的[接受编码](https://tools.ietf.org/html/rfc7231#section-5.3.4)标头匹配，则[Vary 会](https://tools.ietf.org/html/rfc7231#section-7.1.4) &ndash;将中间件配置为提供缓存的响应。

[!code-csharp[](middleware/samples_snippets/2.x/AddHeaders.cs)]

响应缓存中间件仅缓存服务器响应，导致了200（正常）状态代码。 中间件将忽略任何其他响应，包括[错误页](xref:fundamentals/error-handling)。

> [!WARNING]
> 包含经过身份验证的客户端的内容的响应必须标记为不可缓存，以防中间件存储和服务这些响应。 有关中间件如何确定响应是否可缓存的详细信息，请参阅[缓存的条件](#conditions-for-caching)。

## <a name="options"></a>选项

响应缓存选项如下表中所示。

| 选项 | 说明 |
| ------ | ----------- |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize> | 响应正文的最大可缓存大小（以字节为单位）。 默认值为`64 * 1024 * 1024` （64 MB）。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit> | 响应缓存中间件的大小限制（以字节为单位）。 默认值为`100 * 1024 * 1024` （100 MB）。 |
| <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.UseCaseSensitivePaths> | 确定是否将响应缓存在区分大小写的路径上。 默认值为 `false`。 |

下面的示例将中间件配置为：

* 大小小于或等于1024字节的缓存响应。
* 将响应存储为区分大小写的路径。 例如， `/page1`和`/Page1`分别存储。

```csharp
services.AddResponseCaching(options =>
{
    options.MaximumBodySize = 1024;
    options.UseCaseSensitivePaths = true;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

使用 MVC/web API 控制器或 Razor Pages 页面模型时， [`[ResponseCache]`](xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute)属性指定为响应缓存设置适当的标头所需的参数。 严格要求中间件的`[ResponseCache]`属性的唯一参数是<xref:Microsoft.AspNetCore.Mvc.ResponseCacheAttribute.VaryByQueryKeys>，它不对应于实际 HTTP 标头。 有关详细信息，请参阅 <xref:performance/caching/response#responsecache-attribute>。

如果不使用`[ResponseCache]`属性，响应缓存可能会随而变化`VaryByQueryKeys`。 <xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingFeature>直接从[HttpContext](xref:Microsoft.AspNetCore.Http.HttpContext.Features)使用：

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();

if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

使用等于`*`中`VaryByQueryKeys`的单个值会根据所有请求查询参数改变缓存。

## <a name="http-headers-used-by-response-caching-middleware"></a>响应缓存中间件使用的 HTTP 标头

下表提供了有关影响响应缓存的 HTTP 标头的信息。

| 标头 | 详细信息 |
| ------ | ------- |
| `Authorization` | 如果标头存在，则不会缓存响应。 |
| `Cache-Control` | 中间件仅考虑用`public`缓存指令标记的缓存响应。 具有以下参数的控件缓存：<ul><li>最大期限</li><li>最大过期&#8224;</li><li>最小-新</li><li>must-revalidate</li><li>no-cache</li><li>无-商店</li><li>仅限-缓存</li><li>private</li><li>public</li><li>s-maxage</li><li>代理重新验证&#8225;</li></ul>&#8224;如果未指定任何限制`max-stale`，则中间件不会执行任何操作。<br>&#8225;`proxy-revalidate`的效果与相同`must-revalidate`。<br><br>有关详细信息，请参阅[RFC 7231：请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。 |
| `Pragma` | 请求`Pragma: no-cache`中的标头将产生与相同的`Cache-Control: no-cache`效果。 `Cache-Control`标头中的相关指令（如果存在）将重写此标头。 考虑向后兼容 HTTP/1.0。 |
| `Set-Cookie` | 如果标头存在，则不会缓存响应。 请求处理管道中设置一个或多个 cookie 的任何中间件会阻止响应缓存中间件缓存响应（例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata)）。  |
| `Vary` | `Vary`标头用于根据另一个标头改变缓存的响应。 例如，通过包含标头来缓存响应， `Vary: Accept-Encoding`此标头将使用标头`Accept-Encoding: gzip`和`Accept-Encoding: text/plain`单独的请求来缓存响应。 永远不会存储标头值`*`为的响应。 |
| `Expires` | 除非由其他`Cache-Control`标头重写，否则不会存储或检索此标头过时的响应。 |
| `If-None-Match` | 如果值不`*`为，并且`ETag`响应的与提供的任何值都不匹配，则将从缓存中提供完整响应。 否则，将提供304（未修改）响应。 |
| `If-Modified-Since` | 如果`If-None-Match`标头不存在，则在缓存的响应日期比提供的值更新时，将从缓存中提供完整响应。 否则，将提供*304-未修改*响应。 |
| `Date` | 从缓存提供时，如果`Date`未在原始响应中提供标头，中间件将设置标头。 |
| `Content-Length` | 从缓存提供时，如果`Content-Length`未在原始响应中提供标头，中间件将设置标头。 |
| `Age` | 忽略`Age`原始响应中发送的标头。 中间件在为缓存的响应提供服务时计算一个新值。 |

## <a name="caching-respects-request-cache-control-directives"></a>缓存遵从请求缓存控制指令

中间件遵循[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)的规则。 规则要求使用缓存来服从客户端发送`Cache-Control`的有效标头。 在规范下，客户端可以使用`no-cache`标头值发出请求，并强制服务器为每个请求生成新的响应。 目前，在使用中间件时，不存在对此缓存行为的开发人员控制，因为中间件遵循官方缓存规范。

为了更好地控制缓存行为，浏览 ASP.NET Core 的其他缓存功能。 请参阅下列主题：

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>疑难解答

如果缓存行为与预期不符，请确认响应是可缓存的并且能够通过缓存提供服务。 检查请求的传入标头和响应的传出标头。 启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。

在对缓存行为进行测试和故障排除时，浏览器可能会以不需要的方式设置影响缓存的请求标头。 例如，浏览器可以在刷新页面`Cache-Control`时将`no-cache`标题`max-age=0`设置为或。 以下工具可以显式设置请求标头，并优先于测试缓存：

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>缓存条件

* 请求必须导致服务器响应，状态代码为200（正常）。
* 请求方法必须为 GET 或 HEAD。
* 在`Startup.Configure`中，必须将响应缓存中间件置于需要缓存的中间件之前。 有关详细信息，请参阅 <xref:fundamentals/middleware/index>。
* `Authorization`标题不得存在。
* `Cache-Control`标头参数必须是有效的，并且响应必须标记`public`且未标记`private`。
* 如果`Pragma: no-cache` `Cache-Control`标头不存在，则标头不得出现，因为`Cache-Control`标头会在`Pragma`存在时覆盖标头。
* `Set-Cookie`标题不得存在。
* `Vary`标头参数必须有效并且不等于`*`。
* `Content-Length`标头值（如果已设置）必须与响应正文的大小匹配。
* <xref:Microsoft.AspNetCore.Http.Features.IHttpSendFileFeature>不使用。
* `Expires`标头和`max-age`和`s-maxage`缓存指令指定的响应不能过时。
* 响应缓冲必须成功。 响应的大小必须小于配置的或默认值<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.SizeLimit>。 响应的正文大小必须小于配置的或默认值<xref:Microsoft.AspNetCore.ResponseCaching.ResponseCachingOptions.MaximumBodySize>。
* 必须根据[RFC 7234](https://tools.ietf.org/html/rfc7234)规范来缓存响应。 例如， `no-store`指令在请求或响应标头字段中不得存在。 有关详细信息，请参阅*第3部分：将响应存储在* [RFC 7234](https://tools.ietf.org/html/rfc7234)的缓存中。

> [!NOTE]
> 用于生成安全令牌以防止跨站点请求伪造（CSRF）攻击的防伪系统将`Cache-Control`和`Pragma`标头设置为`no-cache` ，以便不缓存响应。 有关如何为 HTML 窗体元素禁用防伪标记的信息，请<xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration>参阅。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

::: moniker-end
