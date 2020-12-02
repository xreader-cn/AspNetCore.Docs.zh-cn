---
title: 中 ASP.NET Core 的基本 JSON Api Route-to-code
author: jamesnk
description: 了解如何使用 Route-to-code 和 JSON 扩展方法创建轻型 JSON Web api。
monikerRange: '>= aspnetcore-5.0'
ms.author: jamesnk
ms.custom: mvc
ms.date: 11/30/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
- Route-to-code
uid: web-api/route-to-code
ms.openlocfilehash: 1f5f532053f8f5ca7f73df8c1a910a484e2488d9
ms.sourcegitcommit: 0bcc0d6df3145a0727da7c4be2f4bda8f27eeaa3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/02/2020
ms.locfileid: "96513091"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a>中 ASP.NET Core 的基本 JSON Api Route-to-code

作者：[James Newton-King](https://github.com/jamesnk)

ASP.NET Core 支持多种方法来创建 JSON web Api：

* [ASP.NET Core WEB API](xref:web-api/index) 提供了一个完整的框架，用于创建 api。 服务通过从继承来创建 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。 框架提供的一些功能包括模型绑定、验证、内容协商、输入和输出格式设置以及 OpenAPI。
* Route-to-code 是 ASP.NET Core web API 的非框架替代项。 Route-to-code 将 [ASP.NET Core 路由](xref:fundamentals/routing) 直接连接到你的代码。 你的代码从请求中读取并写入响应。 Route-to-code 没有 web API 的高级功能，但也没有使用它所需的配置。

Route-to-code 构建小型和基本的 JSON web Api 时，是一种很好的方法。

## <a name="create-json-web-apis"></a>创建 JSON web Api

ASP.NET Core 提供了有助于创建 JSON web Api 的帮助器方法：

* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> 检查 `Content-Type` JSON 内容类型的标头。
* <xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 从请求中读取 JSON，并将其反序列化为指定的类型。
* <xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 将指定的值作为 JSON 写入响应正文，并将响应内容类型设置为 `application/json` 。

在 *Startup.cs* 中指定基于路由的轻型 JSON api。 路由和 API 逻辑在中配置 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 为应用的请求管道的一部分。

### <a name="write-json-response"></a>写入 JSON 响应

请考虑以下用于配置应用的 JSON API 的代码：

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

上述代码：

* 添加作为路由模板的 HTTP GET API 终结点 `/hello/{name:alpha}` 。
* 当路由匹配时，API `name` 从请求中读取路由值。
* 使用作为 JSON 响应写入匿名类型 `WriteAsJsonAsync` 。

### <a name="read-json-request"></a>读取 JSON 请求

`HasJsonContentType` 和 `ReadFromJsonAsync` 可用于在基于路由的 JSON API 中反序列化 json 响应：

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

上述代码：

* 添加 HTTP POST API 终结点 `/weather` 作为路由模板。
* 当路由匹配时， `HasJsonContentType` 验证请求内容类型。 非 JSON 内容类型返回415状态代码。
* 如果内容类型为 JSON，则请求内容由进行反序列化 `ReadFromJsonAsync` 。

### <a name="configure-json-serialization"></a>配置 JSON 序列化

可通过两种方式自定义 JSON 序列化：

* 默认序列化选项可 `JsonOptions` 在方法中进行配置 `Startup.ConfigureServices` 。
* `WriteAsJsonAsync` 和 `ReadFromJsonAsync` 具有接受对象的重载 `JsonSerializerOptions` 。 此 `JsonSerializerOptions` 对象将重写默认选项。

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a>身份验证和授权

Route-to-code 支持身份验证和授权。 特性（如 `[Authorize]` 和 `[AllowAnonymous]` ）不能放置在映射到请求委托的终结点上。 相反，授权元数据是使用 `RequireAuthorization` 和 `AllowAnonymous` 扩展方法添加的。

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a>依赖关系注入

不能使用构造函数[ (DI) 的依赖关系注入](xref:fundamentals/dependency-injection) Route-to-code 。 Web API 使用注入构造函数中的服务创建一个控制器。 执行终结点时不会创建类型，因此必须手动解析服务。

基于路由的 Api 可用于 <xref:System.IServiceProvider> 解析服务：

* `DbContext`必须从终结点请求委托中的[RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)解析暂时性和作用域内的生存期服务（如）。
* `ILogger`可以从[IEndpointRouteBuilder](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)解析单独生存期服务（如）。 可在请求委托之外解析服务，并在终结点之间共享服务。

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

广泛使用 DI 的 Api 应考虑使用支持 DI 的 ASP.NET Core 应用类型。 例如，ASP.NET Core web API。 使用控制器构造函数的服务注入比手动解析服务更容易。

## <a name="api-project-structure"></a>API 项目结构

基于路由的 Api 不必位于 *Startup.cs* 中。 Api 可以放在其他文件中，并在启动时映射到 `UseEndpoints` 。 此方法减少了启动配置文件的大小。

请考虑以下 `UserApi` 定义方法的静态类 `Map` 。 方法映射基于路由的 Api。

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

在 `Startup.Configure` 方法中，在 `Map` 中调用方法和其他类的静态方法 `UseEndpoints` ：

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="notable-missing-features-compared-to-web-api"></a>与 Web API 相比明显缺少的功能

Route-to-code 适用于基本 JSON Api。 它不支持 ASP.NET Core Web API 提供的许多高级功能。

不是由提供的功能 Route-to-code 包括：

* 模型绑定
* 模型验证
* OpenAPI/Swagger
* 内容协商
* 构造函数依赖关系注入
* `ProblemDetails` ([https://tools.ietf.org/html/rfc7807](RFC 7807))

如果需要上述列表中的某些功能，请考虑使用 [ASP.NET Core WEB api](xref:web-api/index) 创建 API。

## <a name="additional-resources"></a>其他资源

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
