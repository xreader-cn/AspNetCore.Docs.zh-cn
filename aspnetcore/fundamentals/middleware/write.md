---
title: 写入自定义 ASP.NET Core 中间件
author: rick-anderson
description: 了解如何写入自定义 ASP.NET Core 中间件。
ms.author: riande
ms.custom: mvc
ms.date: 02/14/2019
uid: fundamentals/middleware/write
ms.openlocfilehash: 2c5577394a10370d92c8a83f9d806b63f3245c8b
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64889162"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="bade4-103">写入自定义 ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="bade4-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="bade4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bade4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bade4-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="bade4-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="bade4-106">ASP.NET Core 提供了一组丰富的内置中间件组件，但在某些情况下，你可能需要写入自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="bade4-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="bade4-107">中间件类</span><span class="sxs-lookup"><span data-stu-id="bade4-107">Middleware class</span></span>

<span data-ttu-id="bade4-108">通常，中间件封装在类中，并且通过扩展方法公开。</span><span class="sxs-lookup"><span data-stu-id="bade4-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="bade4-109">请考虑以下中间件，该中间件通过查询字符串设置当前请求的区域性：</span><span class="sxs-lookup"><span data-stu-id="bade4-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="bade4-110">以上示例代码用于演示创建中间件组件。</span><span class="sxs-lookup"><span data-stu-id="bade4-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="bade4-111">有关 ASP.NET Core 的内置本地化支持，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="bade4-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="bade4-112">可通过传入区域性测试中间件。</span><span class="sxs-lookup"><span data-stu-id="bade4-112">You can test the middleware by passing in the culture.</span></span> <span data-ttu-id="bade4-113">例如 `http://localhost:7997/?culture=no`。</span><span class="sxs-lookup"><span data-stu-id="bade4-113">For example, `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="bade4-114">以下代码将中间件委托移动到类：</span><span class="sxs-lookup"><span data-stu-id="bade4-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="bade4-115">中间件 `Task` 方法的名称必须是 `Invoke`。</span><span class="sxs-lookup"><span data-stu-id="bade4-115">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="bade4-116">在 ASP.NET Core 2.0 或更高版本中，该名称可以为 `Invoke` 或 `InvokeAsync`。</span><span class="sxs-lookup"><span data-stu-id="bade4-116">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

## <a name="middleware-extension-method"></a><span data-ttu-id="bade4-117">中间件扩展方法</span><span class="sxs-lookup"><span data-stu-id="bade4-117">Middleware extension method</span></span>

<span data-ttu-id="bade4-118">以下扩展方法通过 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开中间件：</span><span class="sxs-lookup"><span data-stu-id="bade4-118">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="bade4-119">以下代码通过 `Startup.Configure` 调用中间件：</span><span class="sxs-lookup"><span data-stu-id="bade4-119">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="bade4-120">中间件应通过在其构造函数中公开其依赖项来遵循[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="bade4-120">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="bade4-121">在每个应用程序生存期构造一次中间件。</span><span class="sxs-lookup"><span data-stu-id="bade4-121">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="bade4-122">如果需要与请求中的中间件共享服务，请参阅[按请求依赖项](#per-request-dependencies)部分。</span><span class="sxs-lookup"><span data-stu-id="bade4-122">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="bade4-123">中间件组件可通过构造函数参数从[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 解析其依赖项。</span><span class="sxs-lookup"><span data-stu-id="bade4-123">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="bade4-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) 也可直接接受其他参数。</span><span class="sxs-lookup"><span data-stu-id="bade4-124">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-dependencies"></a><span data-ttu-id="bade4-125">按请求依赖项</span><span class="sxs-lookup"><span data-stu-id="bade4-125">Per-request dependencies</span></span>

<span data-ttu-id="bade4-126">由于中间件是在应用启动时构造的，而不是按请求构造的，因此在每个请求过程中，中间件构造函数使用的范围内生存期服务不与其他依赖关系注入类型共享。</span><span class="sxs-lookup"><span data-stu-id="bade4-126">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="bade4-127">如果必须在中间件和其他类型之间共享范围内服务，请将这些服务添加到 `Invoke` 方法的签名。</span><span class="sxs-lookup"><span data-stu-id="bade4-127">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="bade4-128">`Invoke` 方法可接受由 DI 填充的其他参数：</span><span class="sxs-lookup"><span data-stu-id="bade4-128">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

```csharp
public class CustomMiddleware
{
    private readonly RequestDelegate _next;

    public CustomMiddleware(RequestDelegate next)
    {
        _next = next;
    }

    // IMyScopedService is injected into Invoke
    public async Task Invoke(HttpContext httpContext, IMyScopedService svc)
    {
        svc.MyProperty = 1000;
        await _next(httpContext);
    }
}
```

## <a name="additional-resources"></a><span data-ttu-id="bade4-129">其他资源</span><span class="sxs-lookup"><span data-stu-id="bade4-129">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
