---
title: 启用 ASP.NET Core 中的跨域请求 (CORS)
author: rick-anderson
description: 了解如何在 ASP.NET Core 应用中允许或拒绝跨源请求的标准。
ms.author: riande
ms.custom: mvc
ms.date: 01/23/2020
uid: security/cors
ms.openlocfilehash: 57098be73164c71d1b0d1fe2f3aee7ec41a32346
ms.sourcegitcommit: eca76bd065eb94386165a0269f1e95092f23fa58
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2020
ms.locfileid: "76727322"
---
# <a name="enable-cross-origin-requests-cors-in-aspnet-core"></a>启用 ASP.NET Core 中的跨域请求 (CORS)

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)

本文介绍如何在 ASP.NET Core 的应用程序中启用 CORS。

浏览器安全可以防止网页向其他域发送请求，而不是为网页提供服务。 此限制称为*相同源策略*。 同一源策略可防止恶意站点读取另一个站点中的敏感数据。 有时，你可能想要允许其他站点对你的应用进行跨域请求。 有关详细信息，请参阅[MOZILLA CORS 一文](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)。

[跨源资源共享](https://www.w3.org/TR/cors/)（CORS）：

* 是一种 W3C 标准，可让服务器放宽相同的源策略。
* **不**是一项安全功能，CORS 放宽 security。 API 不能通过允许 CORS 来更安全。 有关详细信息，请参阅[CORS 的工作](#how-cors)原理。
* 允许服务器明确允许一些跨源请求，同时拒绝其他请求。
* 比早期的技术（如[JSONP](/dotnet/framework/wcf/samples/jsonp)）更安全且更灵活。

[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="same-origin"></a>同一原点

如果两个 Url 具有相同的方案、主机和端口（[RFC 6454](https://tools.ietf.org/html/rfc6454)），则它们具有相同的源。

这两个 Url 具有相同的源：

* `https://example.com/foo.html`
* `https://example.com/bar.html`

这些 Url 的起源不同于前两个 Url：

* `https://example.net` &ndash; 不同的域
* `https://www.example.com/foo.html` &ndash; 不同的子域
* &ndash; 不同方案 `http://example.com/foo.html`
* `https://example.com:9000/foo.html` &ndash; 不同的端口

比较来源时，Internet Explorer 不会考虑该端口。

## <a name="cors-with-named-policy-and-middleware"></a>具有命名策略和中间件的 CORS

CORS 中间件处理跨域请求。 以下代码通过指定源为整个应用启用 CORS：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet&highlight=8,14-23,38)]

前面的代码：

* 将策略名称设置为 "\_myAllowSpecificOrigins"。 策略名称为任意名称。
* 调用 <xref:Microsoft.AspNetCore.Builder.CorsMiddlewareExtensions.UseCors*> 扩展方法，该方法启用 CORS。
* 使用[lambda 表达式](/dotnet/csharp/programming-guide/statements-expressions-operators/lambda-expressions)调用 <xref:Microsoft.Extensions.DependencyInjection.CorsServiceCollectionExtensions.AddCors*>。 Lambda 采用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 的对象。 本文稍后将介绍[配置选项](#cors-policy-options)，如 `WithOrigins`。

<xref:Microsoft.Extensions.DependencyInjection.MvcCorsMvcCoreBuilderExtensions.AddCors*> 方法调用将 CORS 服务添加到应用的服务容器：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup.cs?name=snippet2)]

有关详细信息，请参阅本文档中的[CORS 策略选项](#cpo)。

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder> 方法可以链接方法，如以下代码所示：

[!code-csharp[](cors/sample/Cors/WebAPI/Startup2.cs?name=snippet2)]

注意： URL**不得包含尾随**斜杠（`/`）。 如果 URL 以 `/`终止，比较将返回 `false` 并且不返回任何标头。

::: moniker range=">= aspnetcore-3.0"

<a name="acpall"></a>

### <a name="apply-cors-policies-to-all-endpoints"></a>将 CORS 策略应用到所有终结点

以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：
```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    // Preceding code ommitted.
    app.UseRouting();

    app.UseCors();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });

    // Following code ommited.
}
```

> [!WARNING]
> 通过终结点路由，CORS 中间件必须配置为在对 `UseRouting` 和 `UseEndpoints`的调用之间执行。 配置不正确将导致中间件停止正常运行。

::: moniker-end

::: moniker range="<= aspnetcore-2.2"
以下代码通过 CORS 中间件将 CORS 策略应用到所有应用终结点：
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
注意：必须在 `UseMvc`之前调用 `UseCors`。

::: moniker-end

请参阅[在 Razor Pages、控制器和操作方法中启用 cors，](#ecors)以在页面/控制器/操作级别应用 cors 策略。

有关测试上述代码的说明，请参阅[测试 CORS](#test) 。

<a name="ecors"></a>

::: moniker range=">= aspnetcore-3.0"

## <a name="enable-cors-with-endpoint-routing"></a>使用终结点路由启用 Cors

使用终结点路由，可以根据每个终结点使用 `RequireCors` 的扩展方法集来启用 CORS。

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapGet("/echo", async context => context.Response.WriteAsync("echo"))
    .RequireCors("policy-name");
});

```

同样，也可以为所有控制器启用 CORS：

```csharp
app.UseEndpoints(endpoints =>
{
  endpoints.MapControllers().RequireCors("policy-name");
});
```
::: moniker-end

## <a name="enable-cors-with-attributes"></a>使用属性启用 CORS

[&lbrack;EnableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.EnableCorsAttribute)属性提供了一种用于全局应用 CORS 的替代方法。 `[EnableCors]` 属性可为所选终结点启用 CORS，而不是所有终结点。

使用 `[EnableCors]` 指定默认策略，并 `[EnableCors("{Policy String}")]` 指定策略。

`[EnableCors]` 特性可应用于：

* Razor 页面 `PageModel`
* 控制器
* 控制器操作方法

可以将不同的策略应用于控制器/页面模型/操作，并使用 `[EnableCors]` 特性。 将 `[EnableCors]` 特性应用到控制器/页模型/操作方法，并在中间件中启用 CORS 时，将应用这两种策略。 建议不要结合策略。 使用 `[EnableCors]` 特性或中间件，而不是在同一应用中。

下面的代码将不同的策略应用于每个方法：

[!code-csharp[](cors/sample/Cors/WebAPI/Controllers/WidgetController.cs?name=snippet&highlight=6,14)]

以下代码创建 CORS 默认策略和名为 `"AnotherPolicy"`的策略：

[!code-csharp[](cors/sample/Cors/WebAPI/StartupMultiPolicy.cs?name=snippet&highlight=12-28)]

### <a name="disable-cors"></a>禁用 CORS

[&lbrack;DisableCors&rbrack;](xref:Microsoft.AspNetCore.Cors.DisableCorsAttribute)属性对控制器/页模型/操作禁用 CORS。

<a name="cpo"></a>

## <a name="cors-policy-options"></a>CORS 策略选项

本部分介绍可在 CORS 策略中设置的各种选项：

* [设置允许的来源](#set-the-allowed-origins)
* [设置允许的 HTTP 方法](#set-the-allowed-http-methods)
* [设置允许的请求标头](#set-the-allowed-request-headers)
* [设置公开的响应标头](#set-the-exposed-response-headers)
* [跨域请求中的凭据](#credentials-in-cross-origin-requests)
* [设置预检过期时间](#set-the-preflight-expiration-time)

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsOptions.AddPolicy*> 在 `Startup.ConfigureServices`中调用。 对于某些选项，最好先阅读[CORS 如何工作](#how-cors)部分。

## <a name="set-the-allowed-origins"></a>设置允许的来源

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyOrigin*> &ndash; 允许所有来源的 CORS 请求和任何方案（`http` 或 `https`）。 `AllowAnyOrigin` 不安全，因为*任何网站*都可以向应用程序发出跨域请求。

::: moniker range=">= aspnetcore-2.2"

> [!NOTE]
> 指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的配置，可能导致跨站点请求伪造。 使用这两种方法配置应用时，CORS 服务将返回无效的 CORS 响应。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

> [!NOTE]
> 指定 `AllowAnyOrigin` 和 `AllowCredentials` 是不安全的配置，可能导致跨站点请求伪造。 对于安全应用，如果客户端必须授权自身访问服务器资源，请指定准确的来源列表。

::: moniker-end

`AllowAnyOrigin` 影响预检请求和 `Access-Control-Allow-Origin` 标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

::: moniker range=">= aspnetcore-2.0"

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetIsOriginAllowedToAllowWildcardSubdomains*> &ndash; 将策略的 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.IsOriginAllowed*> 属性设置为在评估是否允许源时允许源与配置的通配符域匹配的函数。

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=100-105&highlight=4-5)]

::: moniker-end

### <a name="set-the-allowed-http-methods"></a>设置允许的 HTTP 方法

<xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyMethod*>设置用户帐户 ：

* 允许任何 HTTP 方法：
* 影响预检请求和 `Access-Control-Allow-Methods` 标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

### <a name="set-the-allowed-request-headers"></a>设置允许的请求标头

若要允许在 CORS 请求中发送特定标头（称为*作者请求标头*），请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*> 并指定允许的标头：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

此设置会影响预检请求和 `Access-Control-Request-Headers` 标头。 有关详细信息，请参阅[预检请求](#preflight-requests)部分。

::: moniker range=">= aspnetcore-2.2"

仅当在 `Access-Control-Request-Headers` 中发送的标头与 `WithHeaders`中所述的标头完全匹配时，才可以使用 CORS 中间件策略匹配 `WithHeaders` 指定的特定标头。

例如，考虑按如下方式配置的应用：

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 中间件使用以下请求标头拒绝预检请求，因为 `WithHeaders`中未列出 `Content-Language` （[HeaderNames. ContentLanguage](xref:Microsoft.Net.Http.Headers.HeaderNames.ContentLanguage)）：

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

应用返回*200 OK*响应，但不会向后发送 CORS 标头。 因此，浏览器不会尝试跨域请求。

::: moniker-end

::: moniker range="< aspnetcore-2.2"

CORS 中间件始终允许发送 `Access-Control-Request-Headers` 中的四个标头，而不考虑在 CorsPolicy 中配置的值。 此标头列表包括：

* `Accept`
* `Accept-Language`
* `Content-Language`
* `Origin`

例如，考虑按如下方式配置的应用：

```csharp
app.UseCors(policy => policy.WithHeaders(HeaderNames.CacheControl));
```

CORS 中间件使用以下请求标头成功响应预检请求，因为 `Content-Language` 始终为允许列表：

```
Access-Control-Request-Headers: Cache-Control, Content-Language
```

::: moniker-end

### <a name="set-the-exposed-response-headers"></a>设置公开的响应标头

默认情况下，浏览器不会向应用程序公开所有的响应标头。 有关详细信息，请参阅[W3C 跨域资源共享（术语）：简单的响应标头](https://www.w3.org/TR/cors/#simple-response-header)。

默认情况下可用的响应标头包括：

* `Cache-Control`
* `Content-Language`
* `Content-Type`
* `Expires`
* `Last-Modified`
* `Pragma`

CORS 规范将这些标头称为*简单的响应标头*。 若要使其他标头可用于应用程序，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithExposedHeaders*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=73-78&highlight=5)]

### <a name="credentials-in-cross-origin-requests"></a>跨域请求中的凭据

凭据需要在 CORS 请求中进行特殊处理。 默认情况下，浏览器不会使用跨域请求发送凭据。 凭据包括 cookie 和 HTTP 身份验证方案。 若要使用跨域请求发送凭据，客户端必须将 `XMLHttpRequest.withCredentials` 设置为 `true`。

直接使用 `XMLHttpRequest`：

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

使用[提取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)：

```javascript
fetch('https://www.example.com/api/test', {
    credentials: 'include'
});
```

服务器必须允许凭据。 若要允许跨域凭据，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowCredentials*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=82-87&highlight=5)]

HTTP 响应包含一个 `Access-Control-Allow-Credentials` 标头，该标头通知浏览器服务器允许跨源请求的凭据。

如果浏览器发送凭据，但响应不包含有效的 `Access-Control-Allow-Credentials` 标头，则浏览器不会向应用程序公开响应，而且跨源请求会失败。

允许跨域凭据会带来安全风险。 另一个域中的网站可以代表用户将登录用户的凭据发送给该应用程序，而无需用户的知识。 <!-- TODO Review: When using `AllowCredentials`, all CORS enabled domains must be trusted.
I don't like "all CORS enabled domains must be trusted", because it implies that if you're not using  `AllowCredentials`, domains don't need to be trusted. -->

CORS 规范还指出，如果 `Access-Control-Allow-Credentials` 标头存在，则将源设置为 `"*"` （所有源）是无效的。

### <a name="preflight-requests"></a>预检请求

对于某些 CORS 请求，浏览器会在发出实际请求之前发送其他请求。 此请求称为*预检请求*。 如果满足以下条件，浏览器可以跳过预检请求：

* 请求方法为 GET、HEAD 或 POST。
* 应用不会设置 `Accept`、`Accept-Language`、`Content-Language`、`Content-Type`或 `Last-Event-ID`以外的请求标头。
* `Content-Type` 标头（如果已设置）具有以下值之一：
  * `application/x-www-form-urlencoded`
  * `multipart/form-data`
  * `text/plain`

为客户端请求设置的请求标头上的规则适用于应用通过调用 `XMLHttpRequest` 对象的 `setRequestHeader` 来设置的标头。 CORS 规范调用这些标头*作者请求标头*。 此规则不适用于浏览器可以设置的标头，如 `User-Agent`、`Host`或 `Content-Length`。

下面是预检请求的示例：

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

预航班请求使用 HTTP OPTIONS 方法。 它包括两个特殊标头：

* `Access-Control-Request-Method`：将用于实际请求的 HTTP 方法。
* `Access-Control-Request-Headers`：应用在实际请求上设置的请求标头的列表。 如前文所述，这不包含浏览器设置的标头，如 `User-Agent`。

CORS 预检请求可能包含一个 `Access-Control-Request-Headers` 标头，该标头向服务器指示与实际请求一起发送的标头。

若要允许特定标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.WithHeaders*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=55-60&highlight=5)]

若要允许所有作者请求标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.AllowAnyHeader*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=64-69&highlight=5)]

浏览器在设置 `Access-Control-Request-Headers`的方式上并不完全一致。 如果将标头设置为 `"*"` 以外的任何内容（或使用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicy.AllowAnyHeader*>），则应至少包含 `Accept`、`Content-Type`和 `Origin`，以及要支持的任何自定义标头。

下面是针对预检请求的示例响应（假定服务器允许该请求）：

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

响应包括一个 `Access-Control-Allow-Methods` 标头，该标头列出了允许的方法，还可以选择一个 `Access-Control-Allow-Headers` 标头，其中列出了允许的标头。 如果预检请求成功，则浏览器发送实际请求。

如果预检请求被拒绝，应用将返回*200 OK*响应，但不会向后发送 CORS 标头。 因此，浏览器不会尝试跨域请求。

### <a name="set-the-preflight-expiration-time"></a>设置预检过期时间

`Access-Control-Max-Age` 标头指定可缓存对预检请求的响应的时间长度。 若要设置此标头，请调用 <xref:Microsoft.AspNetCore.Cors.Infrastructure.CorsPolicyBuilder.SetPreflightMaxAge*>：

[!code-csharp[](cors/sample/CorsExample4/Startup.cs?range=91-96&highlight=5)]

<a name="how-cors"></a>

## <a name="how-cors-works"></a>CORS 如何工作

本部分介绍 HTTP 消息级别的[CORS](https://developer.mozilla.org/en-US/docs/Web/HTTP/CORS)请求中发生的情况。

* CORS**不**是一种安全功能。 CORS 是一种 W3C 标准，可让服务器放宽相同的源策略。
  * 例如，恶意执行组件可能会对站点使用[阻止跨站点脚本（XSS）](xref:security/cross-site-scripting) ，并对启用了 CORS 的站点执行跨站点请求，以窃取信息。
* API 不能通过允许 CORS 来更安全。
  * 它由客户端（浏览器）来强制执行 CORS。 服务器执行请求并返回响应，这是返回错误并阻止响应的客户端。 例如，以下任何工具都将显示服务器响应：
    * [Fiddler](https://www.telerik.com/fiddler)
    * [Postman](https://www.getpostman.com/)
    * [.NET HttpClient](/dotnet/csharp/tutorials/console-webapiclient)
    * Web 浏览器，方法是在地址栏中输入 URL。
* 这是一种方法，使服务器能够允许浏览器执行跨源[XHR](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest)或[获取 API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)请求，否则将被禁止。
  * 浏览器（不含 CORS）不能执行跨域请求。 在 CORS 之前，使用[JSONP](https://www.w3schools.com/js/js_json_jsonp.asp)来绕过此限制。 JSONP 不使用 XHR，它使用 `<script>` 标记接收响应。 允许跨源加载脚本。

[CORS 规范](https://www.w3.org/TR/cors/)介绍了几个新的 HTTP 标头，它们启用了跨域请求。 如果浏览器支持 CORS，则会自动为跨域请求设置这些标头。 若要启用 CORS，无需自定义 JavaScript 代码。

下面是一个跨源请求的示例。 `Origin` 标头提供发出请求的站点的域。 `Origin` 标头是必需的，并且必须与主机不同。

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

如果服务器允许该请求，则会在响应中设置 `Access-Control-Allow-Origin` 标头。 此标头的值可以与请求中的 `Origin` 标头匹配，也可以是通配符值 `"*"`，这意味着允许任何源：

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

如果响应不包括 `Access-Control-Allow-Origin` 标头，则跨域请求会失败。 具体而言，浏览器不允许该请求。 即使服务器返回成功的响应，浏览器也不会将响应提供给客户端应用程序。

<a name="test"></a>

## <a name="test-cors"></a>测试 CORS

测试 CORS：

1. [创建 API 项目](xref:tutorials/first-web-api)。 或者，您也可以[下载该示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/security/cors/sample/Cors)。
1. 使用本文档中的方法之一启用 CORS。 例如：

  [!code-csharp[](cors/sample/Cors/WebAPI/StartupTest.cs?name=snippet2&highlight=13-18)]

  > [!WARNING]
  > `WithOrigins("https://localhost:<port>");` 应仅用于测试类似于[下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/live/aspnetcore/security/cors/sample/Cors)的示例应用。

1. 创建 web 应用项目（Razor Pages 或 MVC）。 该示例使用 Razor Pages。 可以在与 API 项目相同的解决方案中创建 web 应用。
1. 将以下突出显示的代码添加到*索引 cshtml*文件中：

  [!code-csharp[](cors/sample/Cors/ClientApp/Pages/Index2.cshtml?highlight=7-99)]

1. 在上面的代码中，将 `url: 'https://<web app>.azurewebsites.net/api/values/1',` 替换为已部署应用的 URL。
1. 部署 API 项目。 例如，[部署到 Azure](xref:host-and-deploy/azure-apps/index)。
1. 从桌面运行 Razor Pages 或 MVC 应用，然后单击 "**测试**" 按钮。 使用 F12 工具查看错误消息。
1. 从 `WithOrigins` 中删除 localhost 源并部署应用。 或者，使用其他端口运行客户端应用。 例如，在 Visual Studio 中运行。
1. 与客户端应用程序进行测试。 CORS 故障返回一个错误，但错误消息不能用于 JavaScript。 使用 F12 工具中的 "控制台" 选项卡查看错误。 根据浏览器，你会收到类似于以下内容的错误（在 F12 工具控制台中）：

   * 使用 Microsoft Edge：

     **SEC7120： [CORS] 源 `https://localhost:44375` 在 `https://webapi.azurewebsites.net/api/values/1` 上找不到跨源资源的访问控制允许源响应标头中 `https://localhost:44375`**

   * 使用 Chrome：

     **`https://webapi.azurewebsites.net/api/values/1` 源 `https://localhost:44375` 中对 XMLHttpRequest 的访问已被 CORS 策略阻止：请求的资源上没有 "访问控制-允许" 标头。**
     
可以使用工具（如[Fiddler](https://www.telerik.com/fiddler)或[Postman](https://www.getpostman.com/)）测试启用 CORS 的终结点。 使用工具时，`Origin` 标头指定的请求源必须与接收请求的主机不同。 如果请求不是基于 `Origin` 标头的值*跨*域的，则：

* 不需要 CORS 中间件来处理请求。
* 不会在响应中返回 CORS 标头。

## <a name="cors-in-iis"></a>IIS 中的 CORS

部署到 IIS 时，如果未将服务器配置为允许匿名访问，则必须在 Windows 身份验证之前运行 CORS。 若要支持此方案，需要为应用安装和配置[IIS CORS 模块](https://www.iis.net/downloads/microsoft/iis-cors-module)。

## <a name="additional-resources"></a>其他资源

* [跨域资源共享（CORS）](https://developer.mozilla.org/docs/Web/HTTP/CORS)
* [IIS CORS 模块入门](https://blogs.iis.net/iisteam/getting-started-with-the-iis-cors-module)
