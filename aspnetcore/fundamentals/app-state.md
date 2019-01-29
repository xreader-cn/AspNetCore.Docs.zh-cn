---
title: ASP.NET Core 中的会话和应用状态
author: rick-anderson
description: 发现保留请求间会话和应用状态的方法。
ms.author: riande
ms.custom: mvc
ms.date: 06/14/2018
uid: fundamentals/app-state
ms.openlocfilehash: a510e4f49e158203dd7c5e1e0bd28472541f7925
ms.sourcegitcommit: ebf4e5a7ca301af8494edf64f85d4a8deb61d641
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/24/2019
ms.locfileid: "54836332"
---
# <a name="session-and-app-state-in-aspnet-core"></a>ASP.NET Core 中的会话和应用状态

作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Steve Smith](https://ardalis.com/)、[Diana LaRose](https://github.com/DianaLaRose) 和 [Luke Latham](https://github.com/guardrex)

HTTP 是无状态的协议。 不采取其他步骤的情况下，HTTP 请求是不保留用户值或应用状态的独立消息。 本文介绍了几种保留请求间用户数据和应用状态的方法。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/app-state/samples)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="state-management"></a>状态管理

可以使用几种方法存储状态。 本主题稍后将对每个方法进行介绍。

| 存储方法 | 存储机制 |
| ---------------- | ----------------- |
| [Cookie](#cookies) | HTTP Cookie（可能包括使用服务器端应用代码存储的数据） |
| [会话状态](#session-state) | HTTP Cookie 和服务器端应用代码 |
| [TempData](#tempdata) | HTTP Cookie 或会话状态 |
| [查询字符串](#query-strings) | HTTP 查询字符串 |
| [隐藏字段](#hidden-fields) | HTTP 窗体字段 |
| [HttpContext.Items](#httpcontextitems) | 服务器端应用代码 |
| [缓存](#cache) | 服务器端应用代码 |
| [依赖关系注入](#dependency-injection) | 服务器端应用代码 |

## <a name="cookies"></a>Cookie

Cookie 存储所有请求的数据。 因为 Cookie 是随每个请求发送的，所以它们的大小应该保持在最低限度。 理想情况下，仅标识符应存储在 Cookie 中，而数据则由应用存储。 大多数浏览器 Cookie 大小限制为 4096 个字节。 每个域仅有有限数量的 Cookie 可用。

由于 Cookie 易被篡改，因此它们必须由服务器进行验证。 客户端上的 Cookie 可能被用户删除或者过期。 但是，Cookie 通常是客户端上最持久的数据暂留形式。

Cookie 通常用于个性化设置，其中的内容是为已知用户定制的。 大多数情况下，仅标识用户，但不对其进行身份验证。 Cookie 可以存储用户名、帐户名或唯一的用户 ID（例如 GUID）。 然后，可以使用 Cookie 访问用户的个性化设置，例如首选的网站背景色。

发布 Cookie 和处理隐私问题时，请留意[欧盟一般数据保护条例 (GDPR)](https://ec.europa.eu/info/law/law-topic/data-protection)。 有关详细信息，请参阅 [ASP.NET Core 中的一般数据保护条例 (GDPR) 支持](xref:security/gdpr)。

## <a name="session-state"></a>会话状态

会话状态是在用户浏览 Web 应用时用来存储用户数据的 ASP.NET Core 方案。 会话状态使用应用维护的存储来保存客户端所有请求的数据。 会话数据由缓存支持并被视为临时数据 - 站点应在没有会话数据的情况下继续运行。 关键应用程序数据应存储在用户数据库中，并仅作为性能优化缓存在会话中。

> [!NOTE]
> [SignalR](xref:signalr/index) 应用不支持会话，因为 [SignalR 中心](xref:signalr/hubs)可能独立于 HTTP 上下文执行。 例如，当中心打开的长轮询请求超出请求的 HTTP 上下文的生存期时，可能发生这种情况。

ASP.NET Core 通过向客户端提供包含会话 ID 的 Cookie 来维护会话状态，该会话 ID 与每个请求一起发送到应用。 应用使用会话 ID 来获取会话数据。

会话状态具有以下行为：

* 由于会话 Cookie 是特定于浏览器的，因此不能跨浏览器共享会话。
* 浏览器会话结束时删除会话 Cookie。
* 如果收到过期的会话 Cookie，则创建使用相同会话 Cookie 的新会话。
* 不会保留空会话 - 会话中必须设置了至少一个值以保存所有请求的会话。 会话未保留时，为每个新的请求生成新会话 ID。
* 应用在上次请求后保留会话的时间有限。 应用设置会话超时，或者使用 20 分钟的默认值。 会话状态适用于存储特定于特定会话的用户数据，但该数据无需永久的会话存储。
* 调用 [ISession.Clear](/dotnet/api/microsoft.aspnetcore.http.isession.clear) 实现或者会话过期时，会删除会话数据。
* 没有默认机制告知客户端浏览器已关闭或者客户端上的会话 Cookie 被删除或过期的应用代码。
ASP.NET Core MVC 和 Razor 页面模板包括对[一般数据保护条例 (GDPR)](xref:security/gdpr) 的支持。 [会话状态 cookie 不是必需的](xref:security/gdpr#tempdata-provider-and-session-state-cookies-are-not-essential)，如果禁用跟踪，则会话状态不起作用。

> [!WARNING]
> 请勿将敏感数据存储在会话状态中。 用户可能不会关闭浏览器并清除会话 Cookie。 某些浏览器会保留所有浏览器窗口中的有效会话 Cookie。 会话可能不限于单个用户 - 下一个用户可能继续使用同一会话 Cookie 浏览应用。

内存中缓存提供程序在应用驻留的服务器内存中存储会话数据。 在服务器场方案中：

* 使用粘性会话将每个会话加入到单独服务器上的特定应用实例。 默认情况下，[Azure 应用服务](https://azure.microsoft.com/services/app-service/)使用[应用程序请求路由 (ARR)](/iis/extensions/planning-for-arr/using-the-application-request-routing-module) 强制实施粘性会话。 然而，粘性会话可能会影响可伸缩性，并使 Web 应用更新变得复杂。 更好的方法是使用 Redis 或 SQL Server 分布式缓存，它们不需要粘性会话。 有关更多信息，请参见<xref:performance/caching/distributed>。
* 通过 [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) 加密会话 Cookie。 必须正确配置数据保护，以在每台计算机上读取会话 Cookie。 有关详细信息，请参阅 <xref:security/data-protection/introduction> 和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)。

### <a name="configure-session-state"></a>配置会话状态

::: moniker range=">= aspnetcore-2.0"

[Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) 中包含的 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包提供中间件来管理会话状态。 若要启用会话中间件，`Startup` 必须包含：

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包提供中间件来管理会话状态。 若要启用会话中间件，`Startup` 必须包含：

::: moniker-end

* 任一 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) 内存缓存。 `IDistributedCache` 实现用作会话后备存储。 有关更多信息，请参见<xref:performance/caching/distributed>。
* 对 `ConfigureServices` 中 [AddSession](/dotnet/api/microsoft.extensions.dependencyinjection.sessionservicecollectionextensions.addsession) 的调用。
* 对 `Configure` 中 [UseSession](/dotnet/api/microsoft.aspnetcore.builder.sessionmiddlewareextensions#methods_) 的调用。

以下代码演示如何使用 `IDistributedCache` 的默认内存中实现设置内存中会话提供程序：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13-18,39)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5,7-12,19)]

::: moniker-end

中间件的顺序很重要。 在前面的示例中，在 `UseMvc` 之后调用 `UseSession` 时会发生 `InvalidOperationException` 异常。 有关详细信息，请参阅[中间件排序](xref:fundamentals/middleware/index#order)。

配置会话状态后，[HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) 可用。

调用 `UseSession` 以前无法访问 `HttpContext.Session`。

在应用已经开始写入到响应流之后，不能创建有新会话 Cookie 的新会话。 此异常记录在 Web 服务器日志中但不显示在浏览器中。

### <a name="load-session-state-asynchronously"></a>以异步方式加载会话状态

只有在 [TryGetValue](/dotnet/api/microsoft.aspnetcore.http.isession.trygetvalue)、[Set](/dotnet/api/microsoft.aspnetcore.http.isession.set) 或 [Remove](/dotnet/api/microsoft.aspnetcore.http.isession.remove) 方法之前显式调用 [ISession.LoadAsync](/dotnet/api/microsoft.aspnetcore.http.isession.loadasync) 方法，ASP.NET Core 中的默认会话提供程序才会从基础 [IDistributedCache](/dotnet/api/microsoft.extensions.caching.distributed.idistributedcache) 后备存储以异步方式加载会话记录。 如果未先调用 `LoadAsync`，则会同步加载基础会话记录，这可能对性能产生大规模影响。

若要让应用强制实施此模式，如果未在 `TryGetValue`、`Set` 或 `Remove` 之前调用 `LoadAsync` 方法，那么使用引起异常的版本包装 [DistributedSessionStore](/dotnet/api/microsoft.aspnetcore.session.distributedsessionstore) 和 [DistributedSession](/dotnet/api/microsoft.aspnetcore.session.distributedsession) 实现。 在服务容器中注册的已包装的版本。

### <a name="session-options"></a>会话选项

若要替代会话默认值，请使用 [SessionOptions](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions)。

::: moniker range=">= aspnetcore-2.0"

| 选项 | 说明 |
| ------ | ----------- |
| [Cookie](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookie) | 确定用于创建 Cookie 的设置。 [名称](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.name)默认为 [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`)。 [路径](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.path)默认为 [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`)。 [SameSite](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.samesite) 默认为 [SameSiteMode.Lax](/dotnet/api/microsoft.aspnetcore.http.samesitemode) (`1`)。 [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) 默认为 `true`。 [IsEssential](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.isessential) 默认为 `false`。 |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | `IdleTimeout` 显示放弃其内容前，内容可以空闲多长时间。 每个会话访问都会重置超时。 此设置仅适用于会话内容，不适用于 Cookie。 默认为 20 分钟。 |
| [IOTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.iotimeout) | 允许从存储加载会话或者将其提交回存储的最大时长。 此设置可能仅适用于异步操作。 可以使用 [InfiniteTimeSpan](/dotnet/api/system.threading.timeout.infinitetimespan) 禁用超时。 默认值为 1 分钟。 |

会话使用 Cookie 跟踪和标识来自单个浏览器的请求。 默认情况下，此 Cookie 名为 `.AspNetCore.Session` ，并使用路径 `/`。 由于 Cookie 默认值不指定域，因此它不提供页上的客户端脚本（因为 [HttpOnly](/dotnet/api/microsoft.aspnetcore.http.cookiebuilder.httponly) 默认为 `true`）。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

| 选项 | 说明 |
| ------ | ----------- |
| [CookieDomain](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiedomain) | 确定用于创建 Cookie 的域。 默认情况下不设置 `CookieDomain`。 |
| [CookieHttpOnly](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiehttponly) | 确定浏览器是否应该允许客户端 JavaScript 访问 Cookie。 默认为 `true`，这意味着 Cookie 仅传递到 HTTP 请求，且不适用于页面上的脚本。 |
| [CookieName](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiename) | 确定用来保留会话 ID 的 Cookie 名称。 默认为 [SessionDefaults.CookieName](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiename) (`.AspNetCore.Session`)。 |
| [CookiePath](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiepath) | 确定用于创建 Cookie 的路径。 默认为 [SessionDefaults.CookiePath](/dotnet/api/microsoft.aspnetcore.session.sessiondefaults.cookiepath) (`/`)。 |
| [CookieSecure](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.cookiesecure) | 确定 Cookie 是否应仅在 HTTPS 请求上传输。 默认为 [CookieSecurePolicy.None](/dotnet/api/microsoft.aspnetcore.http.cookiesecurepolicy) (`2`)。 |
| [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) | `IdleTimeout` 显示放弃其内容前，内容可以空闲多长时间。 每个会话访问都会重置超时。 请注意，这仅适用于会话内容，不适用于 Cookie。 默认为 20 分钟。 |

会话使用 Cookie 跟踪和标识来自单个浏览器的请求。 默认情况下，此 Cookie 名为 `.AspNet.Session` ，并使用路径 `/`。

::: moniker-end

若要替换 Cookie 会话默认值，请使用 `SessionOptions`：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/2.x/SessionSample/Startup.cs?name=snippet1&highlight=13-18)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples_snapshot/1.x/SessionSample/Startup.cs?name=snippet1&highlight=5-9)]

::: moniker-end

应用使用 [IdleTimeout](/dotnet/api/microsoft.aspnetcore.builder.sessionoptions.idletimeout) 属性确定放弃服务器缓存中的内容前，内容可以空闲多长时间。 此属性独立于 Cookie 到期时间。 通过[会话中间件](/dotnet/api/microsoft.aspnetcore.session.sessionmiddleware)传递的每个请求都会重置超时。

会话状态为“非锁定”。 如果两个请求同时尝试修改同一会话的内容，则后一个请求替代前一个请求。 `Session` 是作为一个连贯会话实现的，这意味着所有内容都存储在一起。 两个请求试图修改不同的会话值时，后一个请求可能替代前一个做出的会话更改。

### <a name="set-and-get-session-values"></a>设置和获取会话值

使用 [HttpContext.Session](/dotnet/api/microsoft.aspnetcore.http.httpcontext.session) 从 Razor Pages [PageModel](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel) 类或 MVC [控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)类访问会话状态。 此属性是 [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) 实现。

::: moniker range=">= aspnetcore-2.0"

`ISession` 实现提供用于设置和检索整数和字符串值的若干扩展方法。 项目引用 [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) 包时，扩展方法位于 [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) 命名空间中（添加 `using Microsoft.AspNetCore.Http;` 语句获取对扩展方法的访问权限）。 这两个包均包括在 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

`ISession` 实现提供设置和检索整数和字符串值的若干扩展方法。 项目引用 [Microsoft.AspNetCore.Http.Extensions](https://www.nuget.org/packages/Microsoft.AspNetCore.Http.Extensions/) 包时，扩展方法位于 [Microsoft.AspNetCore.Http](/dotnet/api/microsoft.aspnetcore.http) 命名空间中（添加 `using Microsoft.AspNetCore.Http;` 语句获取对扩展方法的访问权限）。

::: moniker-end

`ISession` 扩展方法：

* [Get(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.get)
* [GetInt32(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getint32)
* [GetString(ISession, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.getstring)
* [SetInt32(ISession, String, Int32)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setint32)
* [SetString(ISession, String, String)](/dotnet/api/microsoft.aspnetcore.http.sessionextensions.setstring)

以下示例在 Razor Pages 页中检索 `IndexModel.SessionKeyName` 键（示例应用中的 `_Name`）的会话值：

```csharp
@page
@using Microsoft.AspNetCore.Http
@model IndexModel

...

Name: @HttpContext.Session.GetString(IndexModel.SessionKeyName)
```

以下示例显示如何设置和获取整数和字符串：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet1&highlight=18-19,22-23)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet1&highlight=10-11,18-19)]

::: moniker-end

必须对所有会话数据进行序列化以启用分布式缓存方案，即使是在使用内存中缓存的时候。 提供最小的字符串和数字序列化程序（请参阅 [ISession](/dotnet/api/microsoft.aspnetcore.http.isession) 的方法和扩展方法）。 用户必须使用另一种机制（例如 JSON）序列化复杂类型。

添加以下扩展方法以设置和获取可序列化的对象：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Extensions/SessionExtensions.cs?name=snippet1)]

::: moniker-end

以下示例演示如何使用扩展方法设置和获取可序列化的对象：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet2)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet2&highlight=4,12)]

::: moniker-end

## <a name="tempdata"></a>TempData

ASP.NET Core 公开 [Razor Pages 页模型的 TempData 属性](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.tempdata)或 [MVC 控制器的 TempData](/dotnet/api/microsoft.aspnetcore.mvc.controller.tempdata)。 此属性存储未读取的数据。 [Keep](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.keep) 和 [Peek](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.itempdatadictionary.peek) 方法可用于检查数据，而不执行删除。 多个请求需要数据时，TempData 非常有助于进行重定向。 使用 Cookie 或会话状态通过 TempData 提供程序实现 TempData。

### <a name="tempdata-providers"></a>TempData 提供程序

::: moniker range=">= aspnetcore-2.0"

ASP.NET Core 2.0 或更高版本中，默认使用基于 Cookie 的 TempData 提供程序，以将 TempData 存储在 Cookie 中。

使用由 [Base64UrlTextEncoder](/dotnet/api/microsoft.aspnetcore.webutilities.base64urltextencoder) 编码的 [IDataProtector](/dotnet/api/microsoft.aspnetcore.dataprotection.idataprotector) 对 Cookie 数据进行加密，然后进行分块。 因为 Cookie 进行了分块，所以 ASP.NET Core 1.x 中的单个 Cookie 大小限制不适用。 未压缩 Cookie 数据，因为压缩加密的数据会导致安全问题，如 [CRIME](https://wikipedia.org/wiki/CRIME_(security_exploit)) 和 [BREACH](https://wikipedia.org/wiki/BREACH_(security_exploit)) 攻击。 有关基于 Cookie 的 TempData 提供程序的详细信息，请参阅 [CookieTempDataProvider](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.cookietempdataprovider)。

::: moniker-end

::: moniker range="< aspnetcore-2.0"

在 ASP.NET Core 1.0 和 1.1 中，会话状态 TempData 提供程序是默认提供程序。

::: moniker-end

### <a name="choose-a-tempdata-provider"></a>选择 TempData 提供程序

选择 TempData 提供程序涉及几个注意事项，例如：

1. 应用是否已使用会话状态？ 如果是，使用会话状态 TempData 提供程序对应用没有额外的成本（除了数据的大小）。
2. 应用是否只对相对较小的数据量（最多 500 个字节）使用 TempData？ 如果是，Cookie TempData 提供程序将为每个携带 TempData 的请求增加较小的成本。 如果不是，会话状态 TempData 提供程序有助于在使用 TempData 前，避免在每个请求中来回切换大量数据。
3. 应用是否在多个服务器上的服务器场中运行？ 如果是，无需其他任何配置，即可在数据保护外使用 Cookie TempData 提供程序（请参阅 <xref:security/data-protection/introduction> 和[密钥存储提供程序](xref:security/data-protection/implementation/key-storage-providers)）。

> [!NOTE]
> 大多数 Web 客户端（如 Web 浏览器）针对每个 Cookie 的最大大小和/或 Cookie 总数强制实施限制。 使用 Cookie TempData 提供程序时，请验证应用未超过这些限制。 考虑数据的总大小。 解释加密和分块导致的 Cookie 大小增加。

### <a name="configure-the-tempdata-provider"></a>配置 TempData 提供程序

::: moniker range=">= aspnetcore-2.0"

默认情况下启用基于 Cookie 的 TempData 提供程序。

若要启用基于会话的 TempData 提供程序，请使用 [AddSessionStateTempDataProvider](/dotnet/api/microsoft.extensions.dependencyinjection.mvcviewfeaturesmvcbuilderextensions.addsessionstatetempdataprovider) 扩展方法：

[!code-csharp[](app-state/samples_snapshot_2/2.x/SessionSample/Startup.cs?name=snippet1&highlight=11,13,32)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

以下 `Startup` 类代码配置基于会话的 TempData 提供程序：

[!code-csharp[](app-state/samples_snapshot_2/1.x/SessionSample/Startup.cs?name=snippet1&highlight=4,9)]

::: moniker-end

中间件的顺序很重要。 在前面的示例中，在 `UseMvc` 之后调用 `UseSession` 时会发生 `InvalidOperationException` 异常。 有关详细信息，请参阅[中间件排序](xref:fundamentals/middleware/index#order)。

> [!IMPORTANT]
> 如果面向 .NET Framework 并使用基于会话的 TempData 提供程序，请将 [Microsoft.AspNetCore.Session](https://www.nuget.org/packages/Microsoft.AspNetCore.Session/) 包添加到项目。

## <a name="query-strings"></a>查询字符串

可以将有限的数据从一个请求传递到另一个请求，方法是将其添加到新请求的查询字符串中。 这有利于以一种持久的方式捕获状态，这种方式允许通过电子邮件或社交网络共享嵌入式状态的链接。 由于 URL 查询字符串是公共的，因此请勿对敏感数据使用查询字符串。

除了意外的共享，在查询字符串中包含数据还会为[跨站点请求伪造 (CSRF)](https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)) 攻击创造机会，从而欺骗用户在通过身份验证时访问恶意网站。 然后，攻击者可以从应用中窃取用户数据，或者代表用户采取恶意操作。 任何保留的应用或会话状态必须防止 CSRF 攻击。 有关详细信息，请参阅[预防跨网站请求伪造 (XSRF/CSRF) 攻击](xref:security/anti-request-forgery)。

## <a name="hidden-fields"></a>隐藏字段

数据可以保存在隐藏的表单域中，并在下一个请求上回发。 这在多页窗体中很常见。 由于客户端可能篡改数据，因此应用必须始终重新验证存储在隐藏字段中的数据。

## <a name="httpcontextitems"></a>HttpContext.Items

处理单个请求时，使用 [HttpContext.Items](/dotnet/api/microsoft.aspnetcore.http.httpcontext.items) 集合存储数据。 处理请求后，放弃集合的内容。 通常使用 `Items` 集合允许组件或中间件在请求期间在不同时间点操作且没有直接传递参数的方法时进行通信。

在下面示例中，[中间件](xref:fundamentals/middleware/index)将 `isVerified` 添加到 `Items` 集合。

```csharp
app.Use(async (context, next) =>
{
    // perform some verification
    context.Items["isVerified"] = true;
    await next.Invoke();
});
```

然后，在管道中，另一个中间件可以访问 `isVerified` 的值：

```csharp
app.Run(async (context) =>
{
    await context.Response.WriteAsync($"Verified: {context.Items["isVerified"]}");
});
```

对于只供单个应用使用的中间件，`string` 键是可以接受的。 应用实例间共享的中间件应使用唯一的对象键以避免键冲突。 以下示例演示如何使用中间件类中定义的唯一对象键：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=4,13)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Middleware/HttpContextItemsMiddleware.cs?name=snippet1&highlight=5,14)]

::: moniker-end

其他代码可以使用通过中间件类公开的键访问存储在 `HttpContext.Items` 中的值：

::: moniker range=">= aspnetcore-2.0"

[!code-csharp[](app-state/samples/2.x/SessionSample/Pages/Index.cshtml.cs?name=snippet3)]

::: moniker-end

::: moniker range="< aspnetcore-2.0"

[!code-csharp[](app-state/samples/1.x/SessionSample/Controllers/HomeController.cs?name=snippet3)]

::: moniker-end

此方法还有避免在代码中使用关键字符串的优势。

## <a name="cache"></a>缓存

缓存是存储和检索数据的有效方法。 应用可以控制缓存项的生存期。

缓存数据未与特定请求、用户或会话相关联。 **请注意不要缓存可能由其他用户请求检索的特定于用户的数据。**

有关更多信息，请参见<xref:performance/caching/response>。

## <a name="dependency-injection"></a>依赖关系注入

使用[依赖关系注入](xref:fundamentals/dependency-injection)可向所有用户提供数据：

1. 定义一项包含数据的服务。 例如，定义一个名为 `MyAppData` 的类：

    ```csharp
    public class MyAppData
    {
        // Declare properties and methods
    }
    ```

2. 将服务类添加到 `Startup.ConfigureServices`：

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddSingleton<MyAppData>();
    }
    ```

3. 使用数据服务类：

    ::: moniker range=">= aspnetcore-2.0"

    ```csharp
    public class IndexModel : PageModel
    {
        public IndexModel(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

    ::: moniker range="< aspnetcore-2.0"

    ```csharp
    public class HomeController : Controller
    {
        public HomeController(MyAppData myService)
        {
            // Do something with the service
            //    Examples: Read data, store in a field or property
        }
    }
    ```

    ::: moniker-end

## <a name="common-errors"></a>常见错误

* “在尝试激活‘Microsoft.AspNetCore.Session.DistributedSessionStore’时无法为类型‘Microsoft.Extensions.Caching.Distributed.IDistributedCache’解析服务。”

  这通常是由于不能配置至少一个 `IDistributedCache` 实现而造成的。 有关详细信息，请参阅 <xref:performance/caching/distributed> 和 <xref:performance/caching/memory>。

* 在会话中间件保存会话失败的事件中（例如，如果后备存储不可用），中间件记录异常而请求继续正常进行。 这会导致不可预知的行为。

  例如，用户将购物车存储在会话中。 用户将商品添加到购物车，但提交失败。 应用不知道有此失败，因此它向用户报告商品已添加到购物车，但事实并非如此。

  检查此类错误的建议方法是完成将应用写入到该会话后，从应用代码调用 `await feature.Session.CommitAsync();`。 如果后备存储不可用，则 `CommitAsync` 引发异常。 如果 `CommitAsync` 失败，应用可以处理异常。 在与数据存储不可用的相同的条件下，`LoadAsync` 引发异常。

## <a name="additional-resources"></a>其他资源

<xref:host-and-deploy/web-farm>
