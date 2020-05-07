---
title: 写入自定义 ASP.NET Core 中间件
author: rick-anderson
description: 了解如何写入自定义 ASP.NET Core 中间件。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 5/6/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: fundamentals/middleware/write
ms.openlocfilehash: 6852c1831d1f71af7f4fad3288fd6f897dfaa65f
ms.sourcegitcommit: 30fcf69556b6b6ec54a3879e280d5f61f018b48f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/07/2020
ms.locfileid: "82876187"
---
# <a name="write-custom-aspnet-core-middleware"></a><span data-ttu-id="979b1-103">写入自定义 ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="979b1-103">Write custom ASP.NET Core middleware</span></span>

<span data-ttu-id="979b1-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="979b1-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="979b1-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="979b1-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="979b1-106">ASP.NET Core 提供了一组丰富的内置中间件组件，但在某些情况下，你可能需要写入自定义中间件。</span><span class="sxs-lookup"><span data-stu-id="979b1-106">ASP.NET Core provides a rich set of built-in middleware components, but in some scenarios you might want to write a custom middleware.</span></span>

## <a name="middleware-class"></a><span data-ttu-id="979b1-107">中间件类</span><span class="sxs-lookup"><span data-stu-id="979b1-107">Middleware class</span></span>

<span data-ttu-id="979b1-108">通常，中间件封装在类中，并且通过扩展方法公开。</span><span class="sxs-lookup"><span data-stu-id="979b1-108">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="979b1-109">请考虑以下中间件，该中间件通过查询字符串设置当前请求的区域性：</span><span class="sxs-lookup"><span data-stu-id="979b1-109">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](write/snapshot/StartupCulture.cs)]

<span data-ttu-id="979b1-110">以上示例代码用于演示创建中间件组件。</span><span class="sxs-lookup"><span data-stu-id="979b1-110">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="979b1-111">有关 ASP.NET Core 的内置本地化支持，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="979b1-111">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="979b1-112">通过传入区域性测试中间件。</span><span class="sxs-lookup"><span data-stu-id="979b1-112">Test the middleware by passing in the culture.</span></span> <span data-ttu-id="979b1-113">例如，请求 `https://localhost:5001/?culture=no`。</span><span class="sxs-lookup"><span data-stu-id="979b1-113">For example, request `https://localhost:5001/?culture=no`.</span></span>

<span data-ttu-id="979b1-114">以下代码将中间件委托移动到类：</span><span class="sxs-lookup"><span data-stu-id="979b1-114">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddleware.cs)]

<span data-ttu-id="979b1-115">必须包括中间件类：</span><span class="sxs-lookup"><span data-stu-id="979b1-115">The middleware class must include:</span></span>

* <span data-ttu-id="979b1-116">具有类型为 <xref:Microsoft.AspNetCore.Http.RequestDelegate> 的参数的公共构造函数。</span><span class="sxs-lookup"><span data-stu-id="979b1-116">A public constructor with a parameter of type <xref:Microsoft.AspNetCore.Http.RequestDelegate>.</span></span>
* <span data-ttu-id="979b1-117">名为 `Invoke` 或 `InvokeAsync` 的公共方法。</span><span class="sxs-lookup"><span data-stu-id="979b1-117">A public method named `Invoke` or `InvokeAsync`.</span></span> <span data-ttu-id="979b1-118">此方法必须：</span><span class="sxs-lookup"><span data-stu-id="979b1-118">This method must:</span></span>
  * <span data-ttu-id="979b1-119">返回 `Task`。</span><span class="sxs-lookup"><span data-stu-id="979b1-119">Return a `Task`.</span></span>
  * <span data-ttu-id="979b1-120">接受类型 <xref:Microsoft.AspNetCore.Http.HttpContext> 的第一个参数。</span><span class="sxs-lookup"><span data-stu-id="979b1-120">Accept a first parameter of type <xref:Microsoft.AspNetCore.Http.HttpContext>.</span></span>
  
<span data-ttu-id="979b1-121">构造函数和 `Invoke`/`InvokeAsync` 的其他参数由[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 填充。</span><span class="sxs-lookup"><span data-stu-id="979b1-121">Additional parameters for the constructor and `Invoke`/`InvokeAsync` are populated by [dependency injection (DI)](xref:fundamentals/dependency-injection).</span></span>

## <a name="middleware-dependencies"></a><span data-ttu-id="979b1-122">中间件依赖项</span><span class="sxs-lookup"><span data-stu-id="979b1-122">Middleware dependencies</span></span>

<span data-ttu-id="979b1-123">中间件应通过在其构造函数中公开其依赖项来遵循[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="979b1-123">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="979b1-124">在每个应用程序生存期  构造一次中间件。</span><span class="sxs-lookup"><span data-stu-id="979b1-124">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="979b1-125">如果需要与请求中的中间件共享服务，请参阅[按请求中间件依赖项](#per-request-middleware-dependencies)部分。</span><span class="sxs-lookup"><span data-stu-id="979b1-125">See the [Per-request middleware dependencies](#per-request-middleware-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="979b1-126">中间件组件可通过构造函数参数从[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 解析其依赖项。</span><span class="sxs-lookup"><span data-stu-id="979b1-126">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="979b1-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) 也可直接接受其他参数。</span><span class="sxs-lookup"><span data-stu-id="979b1-127">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

## <a name="per-request-middleware-dependencies"></a><span data-ttu-id="979b1-128">按请求中间件依赖项</span><span class="sxs-lookup"><span data-stu-id="979b1-128">Per-request middleware dependencies</span></span>

<span data-ttu-id="979b1-129">由于中间件是在应用启动时构造的，而不是按请求构造的，因此在每个请求过程中，中间件构造函数使用的范围内  生存期服务不与其他依赖关系注入类型共享。</span><span class="sxs-lookup"><span data-stu-id="979b1-129">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="979b1-130">如果必须在中间件和其他类型之间共享范围内  服务，请将这些服务添加到 `Invoke` 方法的签名。</span><span class="sxs-lookup"><span data-stu-id="979b1-130">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="979b1-131">`Invoke` 方法可接受由 DI 填充的其他参数：</span><span class="sxs-lookup"><span data-stu-id="979b1-131">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="middleware-extension-method"></a><span data-ttu-id="979b1-132">中间件扩展方法</span><span class="sxs-lookup"><span data-stu-id="979b1-132">Middleware extension method</span></span>

<span data-ttu-id="979b1-133">以下扩展方法通过 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开中间件：</span><span class="sxs-lookup"><span data-stu-id="979b1-133">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](write/snapshot/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="979b1-134">以下代码通过 `Startup.Configure` 调用中间件：</span><span class="sxs-lookup"><span data-stu-id="979b1-134">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](write/snapshot/Startup.cs?highlight=5)]

## <a name="additional-resources"></a><span data-ttu-id="979b1-135">其他资源</span><span class="sxs-lookup"><span data-stu-id="979b1-135">Additional resources</span></span>

* <xref:fundamentals/middleware/index>
* <xref:test/middleware>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
