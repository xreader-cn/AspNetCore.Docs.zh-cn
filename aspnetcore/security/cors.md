---
title: 在 ASP.NET 核心中启用跨源请求 （CORS）
author: rick-anderson
description: 了解 CORS 如何作为在 ASP.NET 酷应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 04/17/2020
uid: security/cors
ms.openlocfilehash: 56a339d9018f619af38aecc6f4c2ff40c3c43d2f
ms.sourcegitcommit: 3d07e21868dafc503530ecae2cfa18a7490b58a6
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/18/2020
ms.locfileid: "81642699"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>在 ASP.NET 核心中启用跨源请求 （CORS）

::: moniker range=">= aspnetcore-3.0"

由[里克·安德森](https://twitter.com/RickAndMSFT)和[柯克·拉金](https://twitter.com/serpent5)

本文演示如何在ASP.NET核心应用中启用 CORS。

浏览器安全性可防止网页向与服务网页的域不同的域发出请求。 此限制称为*同源策略*。 同源策略可防止恶意站点从其他站点读取敏感数据。 有时，您可能希望允许其他网站向你的应用发出交叉源请求。 有关详细信息，请参阅[Mozilla CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。

[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：

* 是允许服务器放松同源策略的 W3C 标准。
* **CORS 不是**安全功能，可放松安全性。 通过允许 CORS，API 并不更安全。 有关详细信息，请参阅[CORS 的工作原理](#how-cors)。
* 允许服务器显式允许某些跨源请求，同时拒绝其他请求。
* 比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全、更灵活。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="same-origin"></a>同一源

如果两个 URL 具有相同的方案、主机和端口[（RFC 6454），](https://tools.ietf.org/html/rfc6454)则它们具有相同的源源。

这两个 URL 具有相同的来源：

* `https://example.com/foo.html`
* `https://example.com/bar.html`

这些 URL 与前两个 URL 具有不同的来源：

* `https://example.net`&ndash;不同的域
* `https://www.example.com/foo.html`&ndash;不同的子域
* `http://example.com/foo.html`&ndash;不同的方案
* `https://example.com:9000/foo.html`&ndash;不同的端口

## <a name="enable-cors"></a>启用 CORS

有三种方法可以启用 CORS：

* 在中间件中使用[命名策略](#np)或[默认策略](#dp)。
* 使用[终结点路由](#ecors)。
* 使用[[启用 Cors]](#attr)属性。

将[[EnableCors]](#attr)属性与命名策略一起使用，可在限制支持 CORS 的终结点时提供最佳控制。

每种方法都详见以下各节。

<a name="np"></a>

## <a name="cors-with-named-policy-and-middleware"></a>带有命名策略和中间件的 CORS

CORS 中间件处理跨源请求。 以下代码将 CORS 策略应用于具有指定来源的所有应用终结点：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=3,9,31)]

前面的代码：

* 将策略名称设置到`_myAllowSpecificOrigins`。 策略名称是任意的。
* 调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法并指定`_myAllowSpecificOrigins`CORS 策略。 `UseCors`添加 CORS 中间件。 调用 后`UseCors`必须发出`UseRouting`，但之前`UseAuthorization`。 有关详细信息，请参阅[中间件订单](xref:fundamentals/middleware/index#middleware-order)。
* 使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的调用。 lambda 取一<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>个对象。 [配置选项](#cors-policy-options)（如`WithOrigins`）在本文的后面部分介绍。
* 为所有控制器`_myAllowSpecificOrigins`终结点启用 CORS 策略。 请参阅[终结点路由](#ecors)，将 CORS 策略应用于特定终结点。

使用端点路由时，***必须***将 CORS 中间件配置为在调用`UseRouting`和`UseEndpoints`之间执行。

有关测试代码的说明，请参阅[测试 CORS，](#testc)这些说明与前面的代码类似。

方法<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>调用将 CORS 服务添加到应用的服务容器：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup.cs?name=snippet2)]

有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。

这些方法<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>可以链接，如以下代码所示：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup2.cs?name=snippet)]

注意： 指定的 URL**不能**包含尾随斜杠`/`（ 。 如果 URL 终止与`/`，则比较`false`返回，并且不返回任何标头。

<a name="dp"></a>

### <a name="cors-with-default-policy-and-middleware"></a>具有默认策略和中间件的 CORS

以下突出显示的代码启用默认 CORS 策略：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupDefaultPolicy.cs?name=snippet2&highlight=7,29)]

前面的代码将默认的 CORS 策略应用于所有控制器终结点。

<a name="ecors"></a>

## <a name="enable-cors-with-endpoint-routing"></a>通过终结点路由启用 Cors

使用`RequireCors`当前基于每个终结点启用 CORS***不支持***[自动预检请求](#apf)。 有关详细信息，请参阅此[GitHub 问题和](https://github.com/dotnet/aspnetcore/issues/20709)[使用终结点路由和 [HttpOptions] 测试 CORS。](#tcer)

使用端点路由，可以使用扩展方法<xref:Microsoft.AspNetCore.Builder.CorsEndpointConventionBuilderExtensions.RequireCors*>集，按终结点启用 CORS：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPt.cs?name=snippet2&highlight=3,7-15,32,40,43)]

在上述代码中：

* `app.UseCors`启用 CORS 中间件。 由于尚未配置默认策略，`app.UseCors()`因此单独无法启用 CORS。
* `/echo`和 控制器终结点允许使用指定的策略进行交叉源请求。
* `/echo2`和 Razor Pages 终结点***不允许***跨源请求，因为未指定默认策略。

[[禁用 Cors]](#dc)属性***不会***禁用由 终结点路由启用的`RequireCors`CORS。

有关测试代码的指令，请参阅[使用端点路由和 [HttpOptions] 测试 CORS。](#tcer)

<a name="attr"></a>

## <a name="enable-cors-with-attributes"></a>使用属性启用 CORS

使用[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性启用 CORS 并将命名策略应用于仅对需要 CORS 的终结点提供最佳控制。

[[EnableCors]](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了全局应用 CORS 的替代方法。 该`[EnableCors]`属性为选定的终结点启用 CORS，而不是所有终结点：

* `[EnableCors]`指定默认策略。
* `[EnableCors("{Policy String}")]`指定命名策略。

该`[EnableCors]`属性可应用于：

* 剃刀页面`PageModel`
* 控制器
* 控制器操作方法

可以将不同的策略应用于具有属性的`[EnableCors]`控制器、页面模型或操作方法。 当该`[EnableCors]`属性应用于控制器、页面模型或操作方法，并在中间件中启用 CORS 时，将应用***这两个***策略。 ***我们建议不要合并策略。使用相同的应用程序的属性***`[EnableCors]`***或中间件，而不是两者在同一应用中。***

以下代码对每种方法应用不同的策略：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

以下代码创建两个 CORS 策略：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Startup3.cs?name=snippet&highlight=12-28,44)]

为了最好地控制限制 CORS 请求：

* 与`[EnableCors("MyPolicy")]`命名策略一起使用。
* 不要定义默认策略。
* 不要使用[终结点路由](#ecors)。

下一节中的代码与前面的列表一应合。

有关测试代码的说明，请参阅[测试 CORS，](#testc)这些说明与前面的代码类似。

<a name="dc"></a>

### <a name="disable-cors"></a>禁用 CORS

[[禁用 Cors]](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性***不会***禁用终结点[路由](#ecors)已启用的 CORS。

以下代码定义 CORS 策略`"MyPolicy"`：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTestMyPolicy.cs?name=snippet)]

以下代码禁用操作的`GetValues2`CORS：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet&highlight=1,23)]

前面的代码：

* 不支持带[终结点路由](#ecors)的 CORS。
* 不定义[默认的 CORS 策略](#dp)。
* 使用[[启用 Cors（"我的策略"）]](#attr) `"MyPolicy"`为控制器启用 CORS 策略。
* 禁用方法的`GetValues2`CORS。

有关测试上述代码的说明，请参阅[测试 CORS。](#testc)

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 策略选项

本节介绍可在 CORS 策略中设置的各种选项：

* [设置允许的原点](#set-the-allowed-origins)
* [设置允许的 HTTP 方法](#set-the-allowed-http-methods)
* [设置允许的请求标头](#set-the-allowed-request-headers)
* [设置公开的响应标头](#set-the-exposed-response-headers)
* [跨源请求中的凭据](#credentials-in-cross-origin-requests)
* [设置预检过期时间](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在 中`Startup.ConfigureServices`调用 。 对于某些选项，首先阅读["CORS 的工作原理"](#how-cors)部分可能会有所帮助。

## <a name="set-the-allowed-origins"></a>设置允许的原点

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;允许使用任何方案 （或`http``https`） 从所有来源请求 CORS 请求。 `AllowAnyOrigin`不安全，因为*任何网站*都可以向应用发出交叉源请求。

> [!NOTE]
> 指定`AllowAnyOrigin``AllowCredentials`和 是不安全的配置，可能会导致跨站点请求伪造。 当应用同时配置这两种方法时，CORS 服务返回无效的 CORS 响应。

`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;将<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>策略的属性设为一个函数，允许源在评估是否允许原点时匹配配置的通配符域。

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet)]

### <a name="set-the-allowed-http-methods"></a>设置允许的 HTTP 方法

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 允许任何 HTTP 方法：
* 影响印前请求和`Access-Control-Allow-Methods`标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

### <a name="set-the-allowed-request-headers"></a>设置允许的请求标头

要允许在 CORS 请求中发送特定标头，请调用作者[请求标头](https://xhr.spec.whatwg.org/#request)，调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>并指定允许的标头：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

`AllowAnyHeader`影响预检请求和[访问控制请求标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

仅当中`Access-Control-Request-Headers`发送的标头与 中`WithHeaders`所述标头完全匹配时`WithHeaders`，才能与 指定的特定标头匹配 CORS 中间件策略。

例如，请考虑配置如下的应用：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet4)]

CORS 中间件拒绝具有以下请求标头的预检请求，因为`Content-Language`（[标题名称.Content语言](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)） 未在`WithHeaders`中列出：

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

该应用程序返回*200 OK*响应，但不会将 CORS 标头发送回。 因此，浏览器不会尝试跨源请求。

### <a name="set-the-exposed-response-headers"></a>设置公开的响应标头

默认情况下，浏览器不会向应用公开所有响应标头。 有关详细信息，请参阅[W3C 跨源资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。

默认情况下可用的响应标头是：

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 规范调用这些标头*为简单响应标头*。 要使其他标头可供应用使用，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet5)]
### <a name="credentials-in-cross-origin-requests"></a>跨源请求中的凭据

凭据需要在 CORS 请求中特殊处理。 默认情况下，浏览器不会发送具有跨源请求的凭据。 凭据包括 Cookie 和 HTTP 身份验证方案。 要使用跨源请求发送凭据，客户端必须设置为`XMLHttpRequest.withCredentials``true`。

直接`XMLHttpRequest`使用：

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

使用 jQuery：

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

服务器必须允许凭据。 要允许跨源凭据，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet6)]

HTTP 响应包括一`Access-Control-Allow-Credentials`个标头，该标头告诉浏览器服务器允许跨源请求的凭据。

如果浏览器发送凭据，但响应不包含有效的`Access-Control-Allow-Credentials`标头，则浏览器不会向应用公开响应，并且跨源请求将失败。

允许跨源凭据存在安全风险。 另一个域中的网站可以在用户不知情的情况下代表用户向应用发送登录用户的凭据。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 规范还规定，如果标头存在，`"*"``Access-Control-Allow-Credentials`则将原点设置为（所有源）无效。

<a name="pref"></a>

## <a name="preflight-requests"></a>预检请求

对于某些 CORS 请求，浏览器在发出实际请求之前会发送其他[OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)请求。 此请求称为[预检请求](https://developer.mozilla.org/docs/Glossary/Preflight_request)。 如果以下***所有条件都***为 true，浏览器可以跳过预检请求：

* 请求方法是 GET、头或 POST。
* 应用不设置请求`Accept`标头以外的 、、、、`Accept-Language``Content-Language``Content-Type`或`Last-Event-ID`。
* 标头`Content-Type`（如果已设置）具有以下值之一：
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

为客户端请求设置的请求标头规则适用于应用通过调用`setRequestHeader`对象设置的`XMLHttpRequest`标头。 CORS 规范调用这些标头[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)。 该规则不适用于浏览器可以设置的标头，例如`User-Agent`。 `Host` `Content-Length`

下面是与本文档["测试"](#testc)部分中的 **[放置测试]** 按钮所做的预检请求类似的示例响应。

```
General:
Request URL: https://cors3.azurewebsites.net/api/values/5
Request Method: OPTIONS
Status Code: 204 No Content

Response Headers:
Access-Control-Allow-Methods: PUT,DELETE,GET
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f8...8;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Vary: Origin

Request Headers:
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Method: PUT
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

预检请求使用[HTTP OPTIONS](https://developer.mozilla.org/docs/Web/HTTP/Methods/OPTIONS)方法。 它可能包括以下标头：

* [访问控制-请求方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Request-Method)：将用于实际请求的 HTTP 方法。
* [访问控制-请求-标头](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Headers)：应用在实际请求上设置的请求标头的列表。 如前所述，这不包括浏览器设置的标头，如`User-Agent`。
* [访问控制-允许方法](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Methods)

如果预检请求被拒绝，应用将返回响应`200 OK`，但不会设置 CORS 标头。 因此，浏览器不会尝试跨源请求。 有关被拒绝的印前请求的示例，请参阅本文档的["测试 CORS"](#testc)部分。

使用 F12 工具，控制台应用会显示类似于以下错误之一的错误，具体取决于浏览器：

* Firefox： 跨源请求被阻止： 同一源策略不允许读取 中的`https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5`远程资源。 （原因：CORS 请求未成功）。 [了解详细信息](https://developer.mozilla.org/docs/Web/HTTP/CORS/Errors/CORSDidNotSucceed)
* 基于铬：CORS 策略阻止了以'https://cors1.azurewebsites.net/api/TodoItems1/MyDelete2/5来自源'https://cors3.azurewebsites.net获取的访问：对印前请求的响应未通过访问控制检查：请求的资源上不存在"访问-控制-允许-原点"标头。 如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。

要允许特定标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet2)]

要允许所有[作者请求标头](https://www.w3.org/TR/cors/#author-request-headers)，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet3)]

浏览器的设置`Access-Control-Request-Headers`方式不一致。 如果任一：

* 标头设置为`"*"`
* <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>调用： 至少`Accept`包括 ，`Content-Type`和`Origin`， 以及要支持的任何自定义标头。

<a name="apf"></a>

### <a name="automatic-preflight-request-code"></a>自动预检请求代码

应用 CORS 策略时，

* 通过 调用`app.UseCors``Startup.Configure`在全球范围内调用 。
* 使用`[EnableCors]`属性。

ASP.NET核心响应预检选项请求。

使用`RequireCors`当前基于每个终结点启用 CORS***不支持***自动预检请求。

本文档的["测试 CORS"](#testc)部分演示了此行为。

<a name="pro"></a>

### <a name="httpoptions-attribute-for-preflight-requests"></a>预检请求的 [HttpOptions] 属性

当使用适当的策略启用 CORS 时，ASP.NET核心通常会自动响应 CORS 预检请求。 在某些情况下，情况可能并非如此。 例如，将[CORS 与终结点路由一起使用](#ecors)。

以下代码使用[[HttpOptions]](xref:Microsoft.AspNetCore.Mvc.HttpOptionsAttribute)属性为 OPTIONS 请求创建终结点：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet&highlight=5-17)]

有关测试上述代码的说明，请参阅[使用终结点路由和 [HttpOptions] 测试 CORS。](#tcer)

### <a name="set-the-preflight-expiration-time"></a>设置预检过期时间

标头`Access-Control-Max-Age`指定对预检请求的响应可以缓存多长时间。 要设置此标头，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>调用 ：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupAllowSubdomain.cs?name=snippet7)]
<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 的工作原理

本节介绍在 HTTP 消息级别发生的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。

* CORS**不是**一个安全功能。 CORS 是一种 W3C 标准，允许服务器放松同源策略。
  * 例如，恶意参与者可能会对您的网站使用[跨站点脚本 （XSS），](xref:security/cross-site-scripting)并对其启用 CORS 的网站执行跨站点请求以窃取信息。
* 通过允许 CORS，API 并不更安全。
  * 由客户端（浏览器）来强制实施 CORS。 服务器执行请求并返回响应，返回错误的是客户端并阻止响应。 例如，以下任何工具将显示服务器响应：
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET httpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 通过在地址栏中输入 URL 来访问 Web 浏览器。
* 这是服务器允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求的一种方式，否则将禁止这样做。
  * 没有 CORS 的浏览器无法执行跨源请求。 在 CORS 之前[，JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)曾用于规避此限制。 JSONP 不使用 XHR，它使用`<script>`标记来接收响应。 允许跨源加载脚本。

[CORS 规范](https://www.w3.org/TR/cors/)引入了几个支持跨源请求的新 HTTP 标头。 如果浏览器支持 CORS，它将自动为跨源请求设置这些标头。 启用 CORS 不需要自定义 JavaScript 代码。

已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)上的[PUT 测试按钮](https://cors3.azurewebsites.net/test)

下面是从["值](https://cors3.azurewebsites.net/)"测试按钮到`https://cors1.azurewebsites.net/api/values`的跨源请求的示例。 标头`Origin`：

* 提供发出请求的站点的域。
* 是必需的，并且必须与主机不同。

**常规标头**

```
Request URL: https://cors1.azurewebsites.net/api/values
Request Method: GET
Status Code: 200 OK
```

**响应标头**

```
Content-Encoding: gzip
Content-Type: text/plain; charset=utf-8
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors1.azurewebsites.net
Transfer-Encoding: chunked
Vary: Accept-Encoding
X-Powered-By: ASP.NET
```

**请求标头**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Connection: keep-alive
Host: cors1.azurewebsites.net
Origin: https://cors3.azurewebsites.net
Referer: https://cors3.azurewebsites.net/
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0 ...
```

在`OPTIONS`请求中，服务器在响应中设置**响应**`Access-Control-Allow-Origin: {allowed origin}`标头标头。 例如，已部署[的示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI) [，删除 [启用 Cors]](https://cors1.azurewebsites.net/test?number=2)按钮`OPTIONS`请求包含以下标头：

**常规标头**

```
Request URL: https://cors3.azurewebsites.net/api/TodoItems2/MyDelete2/5
Request Method: OPTIONS
Status Code: 204 No Content
```

**响应标头**

```
Access-Control-Allow-Headers: Content-Type,x-custom-header
Access-Control-Allow-Methods: PUT,DELETE,GET,OPTIONS
Access-Control-Allow-Origin: https://cors1.azurewebsites.net
Server: Microsoft-IIS/10.0
Set-Cookie: ARRAffinity=8f...;Path=/;HttpOnly;Domain=cors3.azurewebsites.net
Vary: Origin
X-Powered-By: ASP.NET
```

**请求标头**

```
Accept: */*
Accept-Encoding: gzip, deflate, br
Accept-Language: en-US,en;q=0.9
Access-Control-Request-Headers: content-type
Access-Control-Request-Method: DELETE
Connection: keep-alive
Host: cors3.azurewebsites.net
Origin: https://cors1.azurewebsites.net
Referer: https://cors1.azurewebsites.net/test?number=2
Sec-Fetch-Dest: empty
Sec-Fetch-Mode: cors
Sec-Fetch-Site: cross-site
User-Agent: Mozilla/5.0
```

在前面的**响应标头中**，服务器在响应中设置[访问控制允许源](https://developer.mozilla.org/docs/Web/HTTP/Headers/Access-Control-Allow-Origin)标头。 此`https://cors1.azurewebsites.net`标头的值与请求中的`Origin`标头匹配。

如果<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>调用 ，`Access-Control-Allow-Origin: *`则返回 的通配符值。 `AllowAnyOrigin`允许任何来源。

如果响应不包括标头，`Access-Control-Allow-Origin`则跨源请求将失败。 具体来说，浏览器不允许请求。 即使服务器返回成功的响应，浏览器也不会使响应对客户端应用可用。

<a name="options"></a>

### <a name="display-options-requests"></a>显示选项请求

默认情况下，Chrome 和边缘浏览器不会在 F12 工具的网络选项卡上显示选项请求。 要在这些浏览器中显示选项请求，

* `chrome://flags/#out-of-blink-cors` 或 `edge://flags/#out-of-blink-cors`
* 禁用标志。
* 重新 启动。

默认情况下，Firefox 会显示选项请求。

## <a name="cors-in-iis"></a>IIS 中的 CORS

部署到 IIS 时，如果服务器未配置为允许匿名访问，则 CORS 必须在 Windows 身份验证之前运行。 为了支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。

<a name="testc"></a>

## <a name="test-cors"></a>测试 CORS

[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)具有用于测试 CORS 的代码。 请参阅[如何下载](xref:index#how-to-download-a-sample)。 该示例是添加了 Razor 页面的 API 项目：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupTest2.cs?name=snippet2)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`应仅用于测试类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/3.1sample/Cors)的示例应用。

下面`ValuesController`提供了用于测试的终结点：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/ValuesController.cs?name=snippet)]

[MyDisplayRouteInfo](https://github.com/Rick-Anderson/RouteInfo/blob/master/Microsoft.Docs.Samples.RouteInfo/ControllerContextExtensions.cs)由[Rick.Docs.sample.RouteInfo](https://www.nuget.org/packages/Rick.Docs.Samples.RouteInfo) NuGet 包提供，并显示路线信息。

使用以下方法之一测试前面的示例代码：

* 在 中使用 部署的示例[https://cors3.azurewebsites.net/](https://cors3.azurewebsites.net/)应用。 无需下载示例。
* `dotnet run`使用`https://localhost:5001`的默认 URL 运行示例。
* 从 Visual Studio 运行示例，端口设置为 44398，`https://localhost:44398`用于 URL。

将浏览器与 F12 工具一起使用：

* 选择"**值"** 按钮并查看 **"网络"** 选项卡中的标头。
* 选择**PUT 测试**按钮。 有关显示 OPTIONS 请求的说明，请参阅[显示选项请求](#options)。 **PUT 测试**创建两个请求，一个选项预检请求和 PUT 请求。
* 选择按钮**`GetValues2 [DisableCors]`** 以触发失败的 CORS 请求。 如文档中所述，响应返回 200 成功，但未发出 CORS 请求。 选择 **"控制台"** 选项卡以查看 CORS 错误。 根据浏览器的不同，将显示类似于以下内容的错误：

     CORS 策略`'https://cors1.azurewebsites.net/api/values/GetValues2'`阻止了从`'https://cors3.azurewebsites.net'`源提取的访问：请求的资源上不存在"访问-控制-允许源"标头。 如果非跳转响应可满足需求，请将请求的模式设置为“no-cors”，以便在禁用 CORS 的情况下提取资源。
     
支持 CORS 的终结点可以使用工具进行测试，例如[卷曲](https://curl.haxx.se/)[、Fiddler](https://www.telerik.com/fiddler)或[Postman。](https://www.getpostman.com/) 使用工具时，`Origin`标头指定的请求的来源必须不同于接收请求的主机。 如果请求不是基于标头的值*的跨源*： `Origin`

* CORS 中间件无需处理请求。
* 响应中未返回 CORS 标头。

以下命令用于`curl`发出带有信息的 OPTIONS 请求：

```bash
curl -X OPTIONS https://cors3.azurewebsites.net/api/TodoItems2/5 -i
```

<!--
curl come with Git. Add to path variable
C:\Program Files\Git\mingw64\bin\
-->

<a name="tcer"></a>

### <a name="test-cors-with-endpoint-routing-and-httpoptions"></a>使用端点路由和 [httpOptions] 测试 CORS

使用`RequireCors`当前基于每个终结点启用 CORS***不支持***[自动预检请求](#apf)。 请考虑以下使用[终结点路由启用 CORS 的代码](#ecors)：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/StartupEndPointBugTest.cs?name=snippet2)]

以下内容`TodoItems1Controller`提供了用于测试的终结点：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems1Controller.cs?name=snippet2)]

从已部署[示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI)的[测试页](https://cors1.azurewebsites.net/test?number=1)测试前面的代码。

**删除 [启用 Cors]** 和**GET [启用Cors]** 按钮成功，因为`[EnableCors]`端点具有并响应预检请求。 其他终结点失败。 **GET**按钮失败，因为[JavaScript](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/3.1sample/Cors/WebAPI/wwwroot/js/MyJS.js)发送：

```javascript
 headers: {
      "Content-Type": "x-custom-header"
 },
```

以下内容`TodoItems2Controller`提供了类似的终结点，但包括用于响应 OPTIONS 请求的显式代码：

[!code-csharp[](cors/3.1sample/Cors/WebAPI/Controllers/TodoItems2Controller.cs?name=snippet2)]

从已部署示例的[测试页](https://cors1.azurewebsites.net/test?number=2)测试前面的代码。 在 **"控制器**下拉列表"中，选择 **"预检**"，然后**设置控制器**。 对`TodoItems2Controller`终结点的所有 CORS 调用都成功。

## <a name="additional-resources"></a>其他资源

* [跨源资源共享 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [开始使用 IIS CORS 模块](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文演示如何在ASP.NET核心应用中启用 CORS。

浏览器安全性可防止网页向与服务网页的域不同的域发出请求。 此限制称为*同源策略*。 同源策略可防止恶意站点从其他站点读取敏感数据。 有时，您可能希望允许其他网站向你的应用发出交叉源请求。 有关详细信息，请参阅[Mozilla CORS 文章](https://developer.mozilla.org/docs/Web/HTTP/CORS)。

[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：

* 是允许服务器放松同源策略的 W3C 标准。
* **CORS 不是**安全功能，可放松安全性。 通过允许 CORS，API 并不更安全。 有关详细信息，请参阅[CORS 的工作原理](#how-cors)。
* 允许服务器显式允许某些跨源请求，同时拒绝其他请求。
* 比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全、更灵活。

[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="same-origin"></a>同一源

如果两个 URL 具有相同的方案、主机和端口[（RFC 6454），](https://tools.ietf.org/html/rfc6454)则它们具有相同的源源。

这两个 URL 具有相同的来源：

* `https://example.com/foo.html`
* `https://example.com/bar.html`

这些 URL 与前两个 URL 具有不同的来源：

* `https://example.net`&ndash;不同的域
* `https://www.example.com/foo.html`&ndash;不同的子域
* `http://example.com/foo.html`&ndash;不同的方案
* `https://example.com:9000/foo.html`&ndash;不同的端口

在比较源时，Internet Explorer 不考虑端口。

## <a name="cors-with-named-policy-and-middleware"></a>带有命名策略和中间件的 CORS

CORS 中间件处理跨源请求。 以下代码支持具有指定来源的整个应用的 CORS：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

前面的代码：

* 将策略名称设为"myAllow\_特定原点"。 策略名称是任意的。
* 调用<xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*>扩展方法，该方法启用 CORS。
* 使用<xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*> [lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)的调用。 lambda 取一<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>个对象。 [配置选项](#cors-policy-options)（如`WithOrigins`）在本文的后面部分介绍。

方法<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*>调用将 CORS 服务添加到应用的服务容器：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。

该方法<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder>可以链接方法，如以下代码所示：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

注意： URL**不得**包含尾随斜杠`/`（ 。 如果 URL 终止与`/`，则比较`false`返回，并且不返回任何标头。

以下代码通过 CORS 中间件将 CORS 策略应用于所有应用终结点：
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }
    else
    {
        app.UseHsts();
    }

    app.UseCors();

    app.UseHttpsRedirection();
    app.UseMvc();
}
```
注意：`UseCors`必须在 之前`UseMvc`调用 。

请参阅[在 Razor 页面、控制器和操作方法中启用 CORS，](#ecors)以便在页面/控制器/操作级别应用 CORS 策略。

有关测试代码的说明，请参阅[测试 CORS，](#test)这些说明与前面的代码类似。

## <a name="enable-cors-with-attributes"></a>使用属性启用 CORS

[&lbrack;启用 Cors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了全局应用 CORS 的替代方法。 该`[EnableCors]`属性为选定的端点启用 CORS，而不是所有端点。

用于`[EnableCors]`指定默认策略和`[EnableCors("{Policy String}")]`指定策略。

该`[EnableCors]`属性可应用于：

* 剃刀页面`PageModel`
* 控制器
* 控制器操作方法

您可以使用`[EnableCors]`属性对控制器/页面模型/操作应用不同的策略。 当该`[EnableCors]`属性应用于控制器/页面模型/操作方法，并在中间件中启用 CORS 时，将应用***这两个***策略。 我们建议***不要***合并策略。 使用属性`[EnableCors]`或中间件，***而不是两者**。 使用`[EnableCors]`时 **，不要**定义默认策略。

以下代码对每种方法应用不同的策略：

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

以下代码创建 CORS 默认策略和名为 的策略`"AnotherPolicy"`：

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>禁用 CORS

[&lbrack;禁用 Cors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性禁用控制器/页面模型/操作的 CORS。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 策略选项

本节介绍可在 CORS 策略中设置的各种选项：

* [设置允许的原点](#set-the-allowed-origins)
* [设置允许的 HTTP 方法](#set-the-allowed-http-methods)
* [设置允许的请求标头](#set-the-allowed-request-headers)
* [设置公开的响应标头](#set-the-exposed-response-headers)
* [跨源请求中的凭据](#credentials-in-cross-origin-requests)
* [设置预检过期时间](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*>在 中`Startup.ConfigureServices`调用 。 对于某些选项，首先阅读["CORS 的工作原理"](#how-cors)部分可能会有所帮助。

## <a name="set-the-allowed-origins"></a>设置允许的原点

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*>&ndash;允许使用任何方案 （或`http``https`） 从所有来源请求 CORS 请求。 `AllowAnyOrigin`不安全，因为*任何网站*都可以向应用发出交叉源请求。

> [!NOTE]
> 指定`AllowAnyOrigin``AllowCredentials`和 是不安全的配置，可能会导致跨站点请求伪造。 对于安全应用，如果客户端必须授权自己访问服务器资源，请指定确切的来源列表。

`AllowAnyOrigin`影响预检请求和`Access-Control-Allow-Origin`标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*>&ndash;将<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*>策略的属性设为一个函数，允许源在评估是否允许原点时匹配配置的通配符域。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

### <a name="set-the-allowed-http-methods"></a>设置允许的 HTTP 方法

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>:

* 允许任何 HTTP 方法：
* 影响印前请求和`Access-Control-Allow-Methods`标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

### <a name="set-the-allowed-request-headers"></a>设置允许的请求标头

要允许在 CORS 请求中发送特定标头，请调用作者*请求标头*，调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>并指定允许的标头：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

要允许所有作者请求标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

此设置会影响预检请求和`Access-Control-Request-Headers`标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

无论 CorsPolicy.header 中配置的值`Access-Control-Request-Headers`如何，CORS 中间件始终允许发送 中的四个标头。 此标头列表包括：

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

例如，请考虑配置如下的应用：

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 中间件使用以下请求标头成功响应预检请求，因为`Content-Language`始终被列入白名单：

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

### <a name="set-the-exposed-response-headers"></a>设置公开的响应标头

默认情况下，浏览器不会向应用公开所有响应标头。 有关详细信息，请参阅[W3C 跨源资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。

默认情况下可用的响应标头是：

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 规范调用这些标头*为简单响应标头*。 要使其他标头可供应用使用，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>跨源请求中的凭据

凭据需要在 CORS 请求中特殊处理。 默认情况下，浏览器不会发送具有跨源请求的凭据。 凭据包括 Cookie 和 HTTP 身份验证方案。 要使用跨源请求发送凭据，客户端必须设置为`XMLHttpRequest.withCredentials``true`。

直接`XMLHttpRequest`使用：

```javascript
var xhr = new XMLHttpRequest();
xhr.open('get', 'https://www.example.com/api/test');
xhr.withCredentials = true;
```

使用 jQuery：

```javascript
$.ajax({
  type: 'get',
  url: 'https://www.example.com/api/test',
  xhrFields: {
    withCredentials: true
  }
});
```

使用[提取 API](https://developer.mozilla.org/docs/Web/API/Fetch_API)：

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

服务器必须允许凭据。 要允许跨源凭据，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 响应包括一`Access-Control-Allow-Credentials`个标头，该标头告诉浏览器服务器允许跨源请求的凭据。

如果浏览器发送凭据，但响应不包含有效的`Access-Control-Allow-Credentials`标头，则浏览器不会向应用公开响应，并且跨源请求将失败。

允许跨源凭据存在安全风险。 另一个域中的网站可以在用户不知情的情况下代表用户向应用发送登录用户的凭据。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 规范还规定，如果标头存在，`"*"``Access-Control-Allow-Credentials`则将原点设置为（所有源）无效。

### <a name="preflight-requests"></a>预检请求

对于某些 CORS 请求，浏览器在发出实际请求之前发送其他请求。 此请求称为*预检请求*。 如果以下条件为 true，浏览器可以跳过预检请求：

* 请求方法是 GET、头或 POST。
* 应用不设置请求`Accept`标头以外的 、、、、`Accept-Language``Content-Language``Content-Type`或`Last-Event-ID`。
* 标头`Content-Type`（如果已设置）具有以下值之一：
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

为客户端请求设置的请求标头规则适用于应用通过调用`setRequestHeader`对象设置的`XMLHttpRequest`标头。 CORS 规范调用这些标头*作者请求标头*。 该规则不适用于浏览器可以设置的标头，例如`User-Agent`。 `Host` `Content-Length`

以下是预检请求的示例：

```
OPTIONS https://myservice.azurewebsites.net/api/test HTTP/1.1
Accept: */*
Origin: https://myclient.azurewebsites.net
Access-Control-Request-Method: PUT
Access-Control-Request-Headers: accept, x-my-custom-header
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
Content-Length: 0
```

飞行前请求使用 HTTP OPTIONS 方法。 它包括两个特殊的标头：

* `Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。
* `Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。 如前所述，这不包括浏览器设置的标头，如`User-Agent`。

<!-- I think this needs to be removed, was put here accidently -->

当使用适当的策略启用 CORS 时，ASP.NET核心通常会自动响应 CORS 预检请求。 [有关预检请求，请参阅 [HttpOptions] 属性](#pro)。

CORS 预检请求可能包含一个`Access-Control-Request-Headers`标头，该标头向服务器指示与实际请求一起发送的标头。

要允许特定标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

要允许所有作者请求标头，请调用<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

浏览器的设置`Access-Control-Request-Headers`方式并不完全一致。 如果将标头设置为 （或使用`"*"` <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>） 以外的任何内容，则至少应包括`Accept`和`Content-Type` `Origin`，以及要支持的任何自定义标头。

以下是对预检请求的示例响应（假设服务器允许该请求）：

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 0
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Access-Control-Allow-Headers: x-my-custom-header
Access-Control-Allow-Methods: PUT
Date: Wed, 20 May 2015 06:33:22 GMT
```

响应包括列出`Access-Control-Allow-Methods`允许的方法的标头，并选择一个`Access-Control-Allow-Headers`标头，其中列出了允许的标头。 如果预检请求成功，浏览器将发送实际请求。

如果预检请求被拒绝，应用将返回*200 OK*响应，但不会将 CORS 标头发送回。 因此，浏览器不会尝试跨源请求。

### <a name="set-the-preflight-expiration-time"></a>设置预检过期时间

标头`Access-Control-Max-Age`指定对预检请求的响应可以缓存多长时间。 要设置此标头，请<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>调用 ：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 的工作原理

本节介绍在 HTTP 消息级别发生的[CORS](https://developer.mozilla.org/docs/Web/HTTP/CORS)请求中发生的情况。

* CORS**不是**一个安全功能。 CORS 是一种 W3C 标准，允许服务器放松同源策略。
  * 例如，恶意参与者可以使用[防止跨站点脚本 （XSS）](xref:security/cross-site-scripting)攻击您的网站，并对其启用 CORS 的网站执行跨站点请求以窃取信息。
* 通过允许 CORS，您的 API 并不更安全。
  * 由客户端（浏览器）来强制实施 CORS。 服务器执行请求并返回响应，返回错误的是客户端并阻止响应。 例如，以下任何工具将显示服务器响应：
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET httpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * 通过在地址栏中输入 URL 来访问 Web 浏览器。
* 这是服务器允许浏览器执行跨源[XHR](https://developer.mozilla.org/docs/Web/API/XMLHttpRequest)或[Fetch API](https://developer.mozilla.org/docs/Web/API/Fetch_API)请求的一种方式，否则将禁止这样做。
  * 浏览器（没有 CORS）不能执行交叉源请求。 在 CORS 之前[，JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)曾用于规避此限制。 JSONP 不使用 XHR，它使用`<script>`标记来接收响应。 允许跨源加载脚本。

[CORS 规范](https://www.w3.org/TR/cors/)引入了几个支持跨源请求的新 HTTP 标头。 如果浏览器支持 CORS，它将自动为跨源请求设置这些标头。 启用 CORS 不需要自定义 JavaScript 代码。

下面是跨源请求的示例。 标头`Origin`提供发出请求的站点的域。 标头`Origin`是必需的，并且必须与主机不同。

```
GET https://myservice.azurewebsites.net/api/test HTTP/1.1
Referer: https://myclient.azurewebsites.net/
Accept: */*
Accept-Language: en-US
Origin: https://myclient.azurewebsites.net
Accept-Encoding: gzip, deflate
User-Agent: Mozilla/5.0 (compatible; MSIE 10.0; Windows NT 6.2; WOW64; Trident/6.0)
Host: myservice.azurewebsites.net
```

如果服务器允许请求，它将在`Access-Control-Allow-Origin`响应中设置标头。 此标头的值与请求中的`Origin`标头匹配，或者是通配符值`"*"`，这意味着允许任何源：

```
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Type: text/plain; charset=utf-8
Access-Control-Allow-Origin: https://myclient.azurewebsites.net
Date: Wed, 20 May 2015 06:27:30 GMT
Content-Length: 12

Test message
```

如果响应不包括标头，`Access-Control-Allow-Origin`则跨源请求将失败。 具体来说，浏览器不允许请求。 即使服务器返回成功的响应，浏览器也不会使响应对客户端应用可用。

<a name="test"></a>

## <a name="test-cors"></a>测试 CORS

要测试 CORS：

1. [创建 API 项目](xref:tutorials/first-web-api)。 或者，您可以[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。
1. 使用本文档中的一种方法启用 CORS。 例如：

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");`应仅用于测试类似于[下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)的示例应用。

1. 创建 Web 应用项目（剃须页面或 MVC）。 该示例使用剃刀页。 您可以在与 API 项目相同的解决方案中创建 Web 应用。
1. 将以下突出显示的代码添加到*Index.cshtml*文件：

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 在前面的代码中，替换为`url: 'https://<web app>.azurewebsites.net/api/values/1',`已部署应用的 URL。
1. 部署 API 项目。 例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。
1. 从桌面运行剃刀页面或 MVC 应用，然后单击 **"测试**"按钮。 使用 F12 工具查看错误消息。
1. 从`WithOrigins`中删除本地主机源并部署应用。 或者，使用其他端口运行客户端应用。 例如，从可视化工作室运行。
1. 使用客户端应用进行测试。 CORS 失败返回错误，但错误消息对 JavaScript 不可用。 使用 F12 工具中的控制台选项卡查看错误。 根据浏览器的不同，您会收到类似于以下内容的错误（在 F12 工具控制台中）：

   * 使用微软边缘：

     **SEC7120： [CORS]`https://localhost:44375`在"访问`https://localhost:44375`-控制-允许-原点"响应标头中未找到跨源资源`https://webapi.azurewebsites.net/api/values/1`**

   * 使用铬：

     **CORS 策略阻止了从`https://webapi.azurewebsites.net/api/values/1`源`https://localhost:44375`处对 XMLHttpRequest 的访问：请求的资源上不存在"访问-控制-允许源"标头。**
     
支持 CORS 的终结点可以使用工具进行测试，例如[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)。 使用工具时，`Origin`标头指定的请求的来源必须不同于接收请求的主机。 如果请求不是基于标头的值*的跨源*： `Origin`

* CORS 中间件无需处理请求。
* 响应中未返回 CORS 标头。

## <a name="cors-in-iis"></a>IIS 中的 CORS

部署到 IIS 时，如果服务器未配置为允许匿名访问，则 CORS 必须在 Windows 身份验证之前运行。 为了支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。

## <a name="additional-resources"></a>其他资源

* [跨源资源共享 (CORS)](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [开始使用 IIS CORS 模块](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)

::: moniker-end
