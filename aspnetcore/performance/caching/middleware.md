---
title: 响应缓存在 ASP.NET Core 中的中间件
author: guardrex
description: 了解如何配置和 ASP.NET Core 中使用缓存响应的中间件。
monikerRange: '>= aspnetcore-1.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/16/2019
uid: performance/caching/middleware
ms.openlocfilehash: bb265d04022ec2f8fdb3f2f3bc42f6b3f0b2b338
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410318"
---
# <a name="response-caching-middleware-in-aspnet-core"></a>响应缓存在 ASP.NET Core 中的中间件

通过[Luke Latham](https://github.com/guardrex)和[John 卢奥语](https://github.com/JunTaoLuo)

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/performance/caching/middleware/samples)（[如何下载](xref:index#how-to-download-a-sample)）。

此文章介绍了如何在 ASP.NET Core 应用程序中配置缓存响应的中间件。 中间件确定何时可缓存的响应、 存储响应和从缓存提供服务响应。 有关 HTTP 缓存的介绍和`ResponseCache`属性，请参阅[响应缓存](xref:performance/caching/response)。

## <a name="package"></a>package

::: moniker range=">= aspnetcore-2.1"

引用[Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)或添加到的包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。

::: moniker-end

::: moniker range="= aspnetcore-2.0"

引用[Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)或添加到的包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。

::: moniker-end

::: moniker range="= aspnetcore-1.1"

添加到包引用[Microsoft.AspNetCore.ResponseCaching](https://www.nuget.org/packages/Microsoft.AspNetCore.ResponseCaching/)包。

::: moniker-end

## <a name="configuration"></a>配置

在`Startup.ConfigureServices`，将中间件添加到服务集合。

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet1&highlight=9)]

将应用配置为使用与中间件`UseResponseCaching`扩展方法，它将中间件添加到请求处理管道。 该示例应用将添加[ `Cache-Control` ](https://tools.ietf.org/html/rfc7234#section-5.2)缓存最多 10 秒的可缓存响应的响应标头。 该示例将发送[ `Vary` ](https://tools.ietf.org/html/rfc7231#section-7.1.4)标头用于配置中间件来提供缓存的响应才[ `Accept-Encoding` ](https://tools.ietf.org/html/rfc7231#section-5.3.4)后续请求标头匹配的原始请求。 中的代码示例中， [CacheControlHeaderValue](/dotnet/api/microsoft.net.http.headers.cachecontrolheadervalue)并[HeaderNames](/dotnet/api/microsoft.net.http.headers.headernames)需要`using`语句[Microsoft.Net.Http.Headers](/dotnet/api/microsoft.net.http.headers)命名空间。

[!code-csharp[](middleware/samples/2.x/ResponseCachingMiddleware/Startup.cs?name=snippet2&highlight=17,22-29)]

响应缓存中间件仅缓存导致 200 （正常） 状态代码的服务器响应。 任何其他响应，其中包括[错误页](xref:fundamentals/error-handling)，将忽略由中间件。

> [!WARNING]
> 响应包含经过身份验证的客户端的内容必须标记为不可缓存，以防止从存储和处理这些响应中间件。 请参阅[缓存的条件](#conditions-for-caching)有关中间件如何确定响应是否可缓存的详细信息。

## <a name="options"></a>选项

中间件提供了三个选项用于控制响应缓存。

| 选项                | 描述 |
| --------------------- | ----------- |
| UseCaseSensitivePaths | 确定是否在区分大小写的路径上会缓存响应。 默认值为 `false`。 |
| MaximumBodySize       | 以字节为单位的响应正文最大缓存大小。 默认值是`64 * 1024 * 1024`(64 MB)。 |
| SizeLimit             | 以字节为单位的响应缓存中间件大小限制。 默认值是`100 * 1024 * 1024`(100 MB)。 |

下面的示例配置到中间件：

* 小于或等于 1024 字节的缓存响应。
* 将响应存储通过区分大小写的路径 (例如，`/page1`和`/Page1`单独存储)。

```csharp
services.AddResponseCaching(options =>
{
    options.UseCaseSensitivePaths = true;
    options.MaximumBodySize = 1024;
});
```

## <a name="varybyquerykeys"></a>VaryByQueryKeys

在使用 MVC/Web API 控制器或 Razor 页面页模型`ResponseCache`属性指定设置适当的标头为响应缓存所需的参数。 唯一参数`ResponseCache`严格需要中间件的属性是`VaryByQueryKeys`，这并不对应于实际的 HTTP 标头。 有关详细信息，请参阅[ResponseCache 属性](xref:performance/caching/response#responsecache-attribute)。

不使用时`ResponseCache`属性中，响应缓存可以与变化`VaryByQueryKeys`功能。 使用`ResponseCachingFeature`直接从`IFeatureCollection`的`HttpContext`:

```csharp
var responseCachingFeature = context.HttpContext.Features.Get<IResponseCachingFeature>();
if (responseCachingFeature != null)
{
    responseCachingFeature.VaryByQueryKeys = new[] { "MyKey" };
}
```

使用单个值等于`*`在`VaryByQueryKeys`随缓存所有请求查询参数而都变化。

## <a name="http-headers-used-by-response-caching-middleware"></a>响应缓存中间件所使用的 HTTP 标头

使用 HTTP 标头配置中间件的响应缓存。

| Header | 详细信息 |
| ------ | ------- |
| 授权 | 如果标头存在，不缓存响应。 |
| Cache-Control | 中间件只考虑缓存响应标有`public`缓存指令。 控制缓存使用以下参数：<ul><li>max-age</li><li>max-stale&#8224;</li><li>min-fresh</li><li>must-revalidate</li><li>无缓存</li><li>无存储</li><li>only-if-cached</li><li>private</li><li>public</li><li>s maxage</li><li>proxy-revalidate&#8225;</li></ul>&#8224;如果没有限制指定为`max-stale`，中间件不执行任何操作。<br>&#8225;`proxy-revalidate`具有相同的效果`must-revalidate`。<br><br>有关详细信息，请参阅[RFC 7231:请求缓存控制指令](https://tools.ietf.org/html/rfc7234#section-5.2.1)。 |
| 杂注 | 一个`Pragma: no-cache`请求标头中的生成相同的效果`Cache-Control: no-cache`。 此标头中的相关指令重写`Cache-Control`标头，如果存在。 考虑使用 HTTP/1.0 的向后兼容性。 |
| Set-Cookie | 如果标头存在，不缓存响应。 设置一个或多个 cookie 在请求处理管道中的任何中间件可防止响应缓存中间件缓存响应 (例如，[基于 cookie 的 TempData 提供程序](xref:fundamentals/app-state#tempdata))。  |
| 改变 | `Vary`标头可用于不同的缓存的响应的另一个标头。 例如，缓存响应的编码，方法是包括`Vary: Accept-Encoding`标头，来缓存响应的请求的标头`Accept-Encoding: gzip`和`Accept-Encoding: text/plain`单独。 响应标头值为`*`永远不会存储。 |
| 过期 | 此标头被视为陈旧的响应不是存储或检索除非重写由其他`Cache-Control`标头。 |
| -None-If-match | 如果值不是完整的响应从缓存提供`*`和`ETag`的响应中不匹配任何提供的值。 否则，就会提供 304 （未修改） 响应。 |
| If-Modified-Since | 如果`If-None-Match`标头不存在，如果缓存的响应日期比提供的值的完整响应从缓存提供。 否则，就会提供 304 （未修改） 响应。 |
| 日期 | 从缓存中，提供服务时`Date`由中间件设置标头，如果它未提供原始响应。 |
| 内容长度 | 从缓存中，提供服务时`Content-Length`由中间件设置标头，如果它未提供原始响应。 |
| 年龄 | `Age`忽略原始响应中发送的标头。 提供缓存的响应时，中间件会计算新值。 |

## <a name="caching-respects-request-cache-control-directives"></a>缓存遵循请求缓存控制指令

中间件遵从的规则[HTTP 1.1 缓存规范](https://tools.ietf.org/html/rfc7234#section-5.2)。 规则需要遵守是有效的缓存`Cache-Control`客户端发送的标头。 在规范中，客户端可以发出请求的`no-cache`标头值和强制服务器生成的每个请求的新响应。 目前，没有任何开发人员可以控制此缓存行为时使用中间件，因为中间件符合官方缓存规范。

为了更好地控制缓存行为，将介绍其他缓存功能的 ASP.NET Core。 请参见下面的主题：

* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>

## <a name="troubleshooting"></a>疑难解答

如果缓存行为不会按预期方式，，确认响应是可缓存，能够从缓存中提供。 检查请求的传入标头和响应的传出标头。 启用[日志记录](xref:fundamentals/logging/index)以帮助进行调试。

当测试和故障排除的缓存行为，浏览器可以设置影响缓存产生不利的请求标头。 例如，设置浏览器可能`Cache-Control`标头`no-cache`或`max-age=0`刷新页面时。 以下工具可以显式设置请求标头，并且是测试缓存的首选：

* [Fiddler](https://www.telerik.com/fiddler)
* [Postman](https://www.getpostman.com/)

### <a name="conditions-for-caching"></a>缓存的条件

* 请求必须导致服务器响应 200 （正常） 状态代码。
* 请求方法必须是 GET 或 HEAD。
* 终端中间件必须处理前响应缓存中间件的响应。
* `Authorization`标头不能存在。
* `Cache-Control` 标头参数必须是有效的并且必须标记为响应`public`且未标记为`private`。
* `Pragma: no-cache`标头不能存在如果`Cache-Control`标头不存在，作为`Cache-Control`标头重写`Pragma`标头时存在。
* `Set-Cookie`标头不能存在。
* `Vary` 标头参数必须是有效且不等于`*`。
* `Content-Length`标头值 (如果设置) 必须与匹配的响应正文的大小。
* [IHttpSendFileFeature](/dotnet/api/microsoft.aspnetcore.http.features.ihttpsendfilefeature)未使用。
* 响应不能由指定陈旧`Expires`标头和`max-age`和`s-maxage`缓存指令。
* 响应缓冲必须成功，并且响应的大小必须小于已配置或默认`SizeLimit`。
* 响应必须是可根据缓存[RFC 7234](https://tools.ietf.org/html/rfc7234)规范。 例如，`no-store`指令不能存在请求或响应标头字段中。 请参阅*第 3 部分：在缓存中存储响应*的[RFC 7234](https://tools.ietf.org/html/rfc7234)有关详细信息。

> [!NOTE]
> 防伪系统用于生成安全令牌，以防止跨站点请求伪造 (CSRF) 攻击集`Cache-Control`并`Pragma`标头`no-cache`，以便不会缓存响应。 有关如何禁用 antiforgery 令牌 HTML 窗体元素的信息，请参阅[ASP.NET Core antiforgery 配置](xref:security/anti-request-forgery#aspnet-core-antiforgery-configuration)。

## <a name="additional-resources"></a>其他资源

* <xref:fundamentals/startup>
* <xref:fundamentals/middleware/index>
* <xref:performance/caching/memory>
* <xref:performance/caching/distributed>
* <xref:fundamentals/change-tokens>
* <xref:performance/caching/response>
* <xref:mvc/views/tag-helpers/builtin-th/cache-tag-helper>
* <xref:mvc/views/tag-helpers/builtin-th/distributed-cache-tag-helper>
