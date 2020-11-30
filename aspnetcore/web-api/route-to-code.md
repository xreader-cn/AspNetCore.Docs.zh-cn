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
ms.openlocfilehash: 49eaa3ceb47c41226b7a50782436ec270e6e1b7b
ms.sourcegitcommit: 619200f2981656ede6d89adb6a22ad1a0e16da22
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96335592"
---
# <a name="basic-json-apis-with-no-locroute-to-code-in-aspnet-core"></a><span data-ttu-id="2e584-103">中 ASP.NET Core 的基本 JSON Api Route-to-code</span><span class="sxs-lookup"><span data-stu-id="2e584-103">Basic JSON APIs with Route-to-code in ASP.NET Core</span></span>

<span data-ttu-id="2e584-104">作者：[James Newton-King](https://github.com/jamesnk)</span><span class="sxs-lookup"><span data-stu-id="2e584-104">By [James Newton-King](https://github.com/jamesnk)</span></span>

<span data-ttu-id="2e584-105">ASP.NET Core 支持多种方法来创建 JSON web Api：</span><span class="sxs-lookup"><span data-stu-id="2e584-105">ASP.NET Core supports a number of ways of creating JSON web APIs:</span></span>

* <span data-ttu-id="2e584-106">[ASP.NET Core WEB API](xref:web-api/index) 提供了一个完整的框架，用于创建 api。</span><span class="sxs-lookup"><span data-stu-id="2e584-106">[ASP.NET Core web API](xref:web-api/index) provides a complete framework for creating APIs.</span></span> <span data-ttu-id="2e584-107">服务通过从继承来创建 <xref:Microsoft.AspNetCore.Mvc.ControllerBase> 。</span><span class="sxs-lookup"><span data-stu-id="2e584-107">A service is created by inheriting from <xref:Microsoft.AspNetCore.Mvc.ControllerBase>.</span></span> <span data-ttu-id="2e584-108">框架提供的一些功能包括模型绑定、验证、内容协商、输入和输出格式设置以及 OpenAPI。</span><span class="sxs-lookup"><span data-stu-id="2e584-108">Some features provided by the framework include model binding, validation, content negotiation, input and output formatting, and OpenAPI.</span></span>
* <span data-ttu-id="2e584-109">Route-to-code 是 ASP.NET Core web API 的非框架替代项。</span><span class="sxs-lookup"><span data-stu-id="2e584-109">Route-to-code is a non-framework alternative to ASP.NET Core web API.</span></span> <span data-ttu-id="2e584-110">Route-to-code 将 [ASP.NET Core 路由](xref:fundamentals/routing) 直接连接到你的代码。</span><span class="sxs-lookup"><span data-stu-id="2e584-110">Route-to-code connects [ASP.NET Core routing](xref:fundamentals/routing) directly to your code.</span></span> <span data-ttu-id="2e584-111">你的代码从请求中读取并写入响应。</span><span class="sxs-lookup"><span data-stu-id="2e584-111">Your code reads from the request and writes the response.</span></span> <span data-ttu-id="2e584-112">Route-to-code 没有 web API 的高级功能，但也没有使用它所需的配置。</span><span class="sxs-lookup"><span data-stu-id="2e584-112">Route-to-code doesn't have web API's advanced features, but there's also no configuration required to use it.</span></span>

<span data-ttu-id="2e584-113">Route-to-code 构建小型和基本的 JSON web Api 时，是一种很好的方法。</span><span class="sxs-lookup"><span data-stu-id="2e584-113">Route-to-code is a good approach when building small and basic JSON web APIs.</span></span>

## <a name="create-json-web-apis"></a><span data-ttu-id="2e584-114">创建 JSON web Api</span><span class="sxs-lookup"><span data-stu-id="2e584-114">Create JSON web APIs</span></span>

<span data-ttu-id="2e584-115">ASP.NET Core 提供了有助于创建 JSON web Api 的帮助器方法：</span><span class="sxs-lookup"><span data-stu-id="2e584-115">ASP.NET Core provides helper methods that ease the creation of JSON web APIs:</span></span>

* <span data-ttu-id="2e584-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> 检查 `Content-Type` JSON 内容类型的标头。</span><span class="sxs-lookup"><span data-stu-id="2e584-116"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.HasJsonContentType%2A> checks the `Content-Type` header for a JSON content type.</span></span>
* <span data-ttu-id="2e584-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> 从请求中读取 JSON，并将其反序列化为指定的类型。</span><span class="sxs-lookup"><span data-stu-id="2e584-117"><xref:Microsoft.AspNetCore.Http.HttpRequestJsonExtensions.ReadFromJsonAsync%2A> reads JSON from the request and deserializes it to the specified type.</span></span>
* <span data-ttu-id="2e584-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> 将指定的值作为 JSON 写入响应正文，并将响应内容类型设置为 `application/json` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-118"><xref:Microsoft.AspNetCore.Http.HttpResponseJsonExtensions.WriteAsJsonAsync%2A> writes the specified value as JSON to the response body and sets the response content type to `application/json`.</span></span>

<span data-ttu-id="2e584-119">在 *Startup.cs* 中指定基于路由的轻型 JSON api。</span><span class="sxs-lookup"><span data-stu-id="2e584-119">Lightweight, route-based JSON APIs are specified in *Startup.cs*.</span></span> <span data-ttu-id="2e584-120">路由和 API 逻辑在中配置 <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> 为应用的请求管道的一部分。</span><span class="sxs-lookup"><span data-stu-id="2e584-120">The route and the API logic are configured in <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseEndpoints%2A> as part of an app's request pipeline.</span></span>

### <a name="write-json-response"></a><span data-ttu-id="2e584-121">写入 JSON 响应</span><span class="sxs-lookup"><span data-stu-id="2e584-121">Write JSON response</span></span>

<span data-ttu-id="2e584-122">请考虑以下用于配置应用的 JSON API 的代码：</span><span class="sxs-lookup"><span data-stu-id="2e584-122">Consider the following code that configures a JSON API for an app:</span></span>

[!code-csharp[](route-to-code/sample/Startup3.cs?name=snippet&highlight=6)]

<span data-ttu-id="2e584-123">上述代码：</span><span class="sxs-lookup"><span data-stu-id="2e584-123">The preceding code:</span></span>

* <span data-ttu-id="2e584-124">添加作为路由模板的 HTTP GET API 终结点 `/hello/{name:alpha}` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-124">Adds an HTTP GET API endpoint with `/hello/{name:alpha}` as the route template.</span></span>
* <span data-ttu-id="2e584-125">当路由匹配时，API `name` 从请求中读取路由值。</span><span class="sxs-lookup"><span data-stu-id="2e584-125">When the route is matched, the API reads the `name` route value from the request.</span></span>
* <span data-ttu-id="2e584-126">使用作为 JSON 响应写入匿名类型 `WriteAsJsonAsync` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-126">Writes an anonymous type as a JSON response with `WriteAsJsonAsync`.</span></span>

### <a name="read-json-request"></a><span data-ttu-id="2e584-127">读取 JSON 请求</span><span class="sxs-lookup"><span data-stu-id="2e584-127">Read JSON request</span></span>

<span data-ttu-id="2e584-128">`HasJsonContentType` 和 `ReadFromJsonAsync` 可用于在基于路由的 JSON API 中反序列化 json 响应：</span><span class="sxs-lookup"><span data-stu-id="2e584-128">`HasJsonContentType` and `ReadFromJsonAsync` can be used to deserialize a JSON response in a route-based JSON API:</span></span>

[!code-csharp[](route-to-code/sample/Startup2.cs?name=snippet&highlight=5,11)]

<span data-ttu-id="2e584-129">上述代码：</span><span class="sxs-lookup"><span data-stu-id="2e584-129">The preceding code:</span></span>

* <span data-ttu-id="2e584-130">添加 HTTP POST API 终结点 `/weather` 作为路由模板。</span><span class="sxs-lookup"><span data-stu-id="2e584-130">Adds an HTTP POST API endpoint with `/weather` as the route template.</span></span>
* <span data-ttu-id="2e584-131">当路由匹配时， `HasJsonContentType` 验证请求内容类型。</span><span class="sxs-lookup"><span data-stu-id="2e584-131">When the route is matched, `HasJsonContentType` validates the request content type.</span></span> <span data-ttu-id="2e584-132">非 JSON 内容类型返回415状态代码。</span><span class="sxs-lookup"><span data-stu-id="2e584-132">A non-JSON content type returns a 415 status code.</span></span>
* <span data-ttu-id="2e584-133">如果内容类型为 JSON，则请求内容由进行反序列化 `ReadFromJsonAsync` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-133">If the content type is JSON, the request content is deserialized by `ReadFromJsonAsync`.</span></span>

### <a name="configure-json-serialization"></a><span data-ttu-id="2e584-134">配置 JSON 序列化</span><span class="sxs-lookup"><span data-stu-id="2e584-134">Configure JSON serialization</span></span>

<span data-ttu-id="2e584-135">可通过两种方式自定义 JSON 序列化：</span><span class="sxs-lookup"><span data-stu-id="2e584-135">There are two ways to customize JSON serialization:</span></span>

* <span data-ttu-id="2e584-136">默认序列化选项可 `JsonOptions` 在方法中进行配置 `Startup.ConfigureServices` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-136">Default serialization options can be configured with `JsonOptions` in the `Startup.ConfigureServices` method.</span></span>
* <span data-ttu-id="2e584-137">`WriteAsJsonAsync` 和 `ReadFromJsonAsync` 具有接受对象的重载 `JsonSerializerOptions` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-137">`WriteAsJsonAsync` and `ReadFromJsonAsync` have overloads that accept a `JsonSerializerOptions` object.</span></span> <span data-ttu-id="2e584-138">此 `JsonSerializerOptions` 对象将重写默认选项。</span><span class="sxs-lookup"><span data-stu-id="2e584-138">This `JsonSerializerOptions` object overrides the default options.</span></span>

[!code-csharp[](route-to-code/sample/Startup6.cs?name=snippet)]

## <a name="authentication-and-authorization"></a><span data-ttu-id="2e584-139">身份验证和授权</span><span class="sxs-lookup"><span data-stu-id="2e584-139">Authentication and authorization</span></span>

<span data-ttu-id="2e584-140">Route-to-code 支持身份验证和授权。</span><span class="sxs-lookup"><span data-stu-id="2e584-140">Route-to-code supports authentication and authorization.</span></span> <span data-ttu-id="2e584-141">特性（如 `[Authorize]` 和 `[AllowAnonymous]` ）不能放置在映射到请求委托的终结点上。</span><span class="sxs-lookup"><span data-stu-id="2e584-141">Attributes, such as `[Authorize]` and `[AllowAnonymous]`, can't be placed on endpoints that map to a request delegate.</span></span> <span data-ttu-id="2e584-142">相反，授权元数据是使用 `RequireAuthorization` 和 `AllowAnonymous` 扩展方法添加的。</span><span class="sxs-lookup"><span data-stu-id="2e584-142">Instead, authorization metadata is added using the `RequireAuthorization` and `AllowAnonymous` extension methods.</span></span>

[!code-csharp[](route-to-code/sample/Startup.cs?name=snippet&highlight=30)]

## <a name="dependency-injection"></a><span data-ttu-id="2e584-143">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="2e584-143">Dependency injection</span></span>

<span data-ttu-id="2e584-144">不能使用构造函数[ (DI) 的依赖关系注入](xref:fundamentals/dependency-injection) Route-to-code 。</span><span class="sxs-lookup"><span data-stu-id="2e584-144">[Dependency injection (DI)](xref:fundamentals/dependency-injection) using a constructor isn't possible with Route-to-code.</span></span> <span data-ttu-id="2e584-145">Web API 使用注入构造函数中的服务创建一个控制器。</span><span class="sxs-lookup"><span data-stu-id="2e584-145">Web API creates a controller for you with services injected into the constructor.</span></span> <span data-ttu-id="2e584-146">执行终结点时不会创建类型，因此必须手动解析服务。</span><span class="sxs-lookup"><span data-stu-id="2e584-146">A type isn't created when an endpoint is executed, so services must be resolved manually.</span></span>

<span data-ttu-id="2e584-147">基于路由的 Api 可用于 <xref:System.IServiceProvider> 解析服务：</span><span class="sxs-lookup"><span data-stu-id="2e584-147">Route-based APIs can use <xref:System.IServiceProvider> to resolve services:</span></span>

* <span data-ttu-id="2e584-148">`DbContext`必须从终结点请求委托中的[RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices)解析暂时性和作用域内的生存期服务（如）。</span><span class="sxs-lookup"><span data-stu-id="2e584-148">Transient and scoped lifetime services, such as `DbContext`, must be resolved from [HttpContext.RequestServices](xref:Microsoft.AspNetCore.Http.HttpContext.RequestServices) inside an endpoint's request delegate.</span></span>
* <span data-ttu-id="2e584-149">`ILogger`可以从[IEndpointRouteBuilder](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider)解析单独生存期服务（如）。</span><span class="sxs-lookup"><span data-stu-id="2e584-149">Singleton lifetime services, such as `ILogger`, can be resolved from [IEndpointRouteBuilder.ServiceProvider](xref:Microsoft.AspNetCore.Routing.IEndpointRouteBuilder.ServiceProvider).</span></span> <span data-ttu-id="2e584-150">可在请求委托之外解析服务，并在终结点之间共享服务。</span><span class="sxs-lookup"><span data-stu-id="2e584-150">Services can be resolved outside of request delegates and shared between endpoints.</span></span>

[!code-csharp[](route-to-code/sample/Startup4.cs?name=snippet&highlight=3,7)]

<span data-ttu-id="2e584-151">广泛使用 DI 的 Api 应考虑使用支持 DI 的 ASP.NET Core 应用类型。</span><span class="sxs-lookup"><span data-stu-id="2e584-151">APIs that use DI extensively should consider using an ASP.NET Core app type that supports DI.</span></span> <span data-ttu-id="2e584-152">例如，ASP.NET Core web API。</span><span class="sxs-lookup"><span data-stu-id="2e584-152">For example, ASP.NET Core web API.</span></span> <span data-ttu-id="2e584-153">使用控制器构造函数的服务注入比手动解析服务更容易。</span><span class="sxs-lookup"><span data-stu-id="2e584-153">Service injection using a controller's constructor is easier than manually resolving services.</span></span>

## <a name="api-project-structure"></a><span data-ttu-id="2e584-154">API 项目结构</span><span class="sxs-lookup"><span data-stu-id="2e584-154">API project structure</span></span>

<span data-ttu-id="2e584-155">基于路由的 Api 不必位于 *Startup.cs* 中。</span><span class="sxs-lookup"><span data-stu-id="2e584-155">Route-based APIs don't have to be located in *Startup.cs*.</span></span> <span data-ttu-id="2e584-156">Api 可以放在其他文件中，并在启动时映射到 `UseEndpoints` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-156">APIs can be placed in other files and mapped at startup with `UseEndpoints`.</span></span> <span data-ttu-id="2e584-157">此方法减少了启动配置文件的大小。</span><span class="sxs-lookup"><span data-stu-id="2e584-157">This approach reduces the startup configuration file size.</span></span>

<span data-ttu-id="2e584-158">请考虑以下 `UserApi` 定义方法的静态类 `Map` 。</span><span class="sxs-lookup"><span data-stu-id="2e584-158">Consider the following static `UserApi` class that defines a `Map` method.</span></span> <span data-ttu-id="2e584-159">方法映射基于路由的 Api。</span><span class="sxs-lookup"><span data-stu-id="2e584-159">The method maps route-based APIs.</span></span>

[!code-csharp[](route-to-code/sample/UserApi.cs?name=snippet)]

<span data-ttu-id="2e584-160">在 `Startup.Configure` 方法中，在 `Map` 中调用方法和其他类的静态方法 `UseEndpoints` ：</span><span class="sxs-lookup"><span data-stu-id="2e584-160">In the `Startup.Configure` method, the `Map` method and other class's static methods are called in `UseEndpoints`:</span></span>

[!code-csharp[](route-to-code/sample/Startup5.cs?name=snippet)]

## <a name="additional-resources"></a><span data-ttu-id="2e584-161">其他资源</span><span class="sxs-lookup"><span data-stu-id="2e584-161">Additional resources</span></span>

* <xref:web-api/index>
* <xref:fundamentals/routing>
* <xref:fundamentals/dependency-injection>
