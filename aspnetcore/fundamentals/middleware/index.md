---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: 5d02e1eb37693881d5b1855e1ed163590d8a44d3
ms.sourcegitcommit: fcdf9aaa6c45c1a926bd870ed8f893bdb4935152
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165308"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="8ac5c-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="8ac5c-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="8ac5c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8ac5c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8ac5c-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="8ac5c-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-106">Each component:</span></span>

* <span data-ttu-id="8ac5c-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="8ac5c-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="8ac5c-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="8ac5c-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="8ac5c-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="8ac5c-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="8ac5c-113">这些可重用的类和并行匿名方法即为中间件  ，也叫中间件组件  。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="8ac5c-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="8ac5c-115">当中间件短路时，它被称为“终端中间件”  ，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="8ac5c-116"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="8ac5c-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="8ac5c-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="8ac5c-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="8ac5c-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="8ac5c-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="8ac5c-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="8ac5c-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="8ac5c-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="8ac5c-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="8ac5c-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="8ac5c-129">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="8ac5c-130">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="8ac5c-131">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="8ac5c-132">可通过不  调用 next  参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="8ac5c-133">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="8ac5c-134">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”  。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="8ac5c-135">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="8ac5c-136">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件  的作用。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="8ac5c-137">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="8ac5c-138">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="8ac5c-139">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="8ac5c-140">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="8ac5c-141">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="8ac5c-142">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="8ac5c-143">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-143">May cause a protocol violation.</span></span> <span data-ttu-id="8ac5c-144">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="8ac5c-145">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-145">May corrupt the body format.</span></span> <span data-ttu-id="8ac5c-146">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="8ac5c-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="8ac5c-148">顺序</span><span class="sxs-lookup"><span data-stu-id="8ac5c-148">Order</span></span>

<span data-ttu-id="8ac5c-149">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="8ac5c-150">此排序对于安全性、性能和功能至关重要。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="8ac5c-151">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-3.0"

1. <span data-ttu-id="8ac5c-152">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="8ac5c-152">Exception/error handling</span></span>
   * <span data-ttu-id="8ac5c-153">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-153">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="8ac5c-154">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-154">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="8ac5c-155">数据库错误页中间件报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-155">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="8ac5c-156">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-156">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="8ac5c-157">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-157">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="8ac5c-158">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-158">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="8ac5c-159">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-159">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="8ac5c-160">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-160">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="8ac5c-161">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-161">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="8ac5c-162">用于路由请求的路由中间件 (`UseRouting`)。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-162">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="8ac5c-163">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-163">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="8ac5c-164">用于授权用户访问安全资源的授权中间件 (`UseAuthorization`)。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-164">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="8ac5c-165">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-165">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="8ac5c-166">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-166">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="8ac5c-167">用于将 Razor Pages 终结点添加到请求管道的终结点路由中间件（带有 `MapRazorPages` 的 `UseEndpoints`）。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-167">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

<!--

FUTURE UPDATE

On the next topic overhaul/release update, add API crosslink to "Database Error Page Middleware" in Item 1 of the list ...

Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*

... when available via the API docs.

-->

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseRouting();
    app.UseAuthentication();
    app.UseAuthorization();
    app.UseSession();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

<span data-ttu-id="8ac5c-168">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-168">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="8ac5c-169"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-169"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="8ac5c-170">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-170">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="8ac5c-171">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-171">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="8ac5c-172">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-172">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="8ac5c-173">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-173">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="8ac5c-174">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-174">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="8ac5c-175">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-175">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="8ac5c-176">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-176">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="8ac5c-177">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-177">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="8ac5c-178">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="8ac5c-179">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="8ac5c-180">可以压缩 Razor Pages 响应。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-180">The Razor Pages responses can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapRazorPages();
    });
}
```

::: moniker-end

::: moniker range="< aspnetcore-3.0"

1. <span data-ttu-id="8ac5c-181">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="8ac5c-181">Exception/error handling</span></span>
   * <span data-ttu-id="8ac5c-182">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-182">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="8ac5c-183">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-183">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="8ac5c-184">数据库错误页中间件 (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) 报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-184">Database Error Page Middleware (<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) reports database runtime errors.</span></span>
   * <span data-ttu-id="8ac5c-185">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-185">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="8ac5c-186">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-186">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="8ac5c-187">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-187">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="8ac5c-188">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-188">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="8ac5c-189">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-189">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="8ac5c-190">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-190">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="8ac5c-191">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-191">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="8ac5c-192">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-192">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="8ac5c-193">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-193">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="8ac5c-194">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) 将 MVC 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-194">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    app.UseHttpsRedirection();
    app.UseStaticFiles();
    app.UseCookiePolicy();
    app.UseAuthentication();
    app.UseSession();
    app.UseMvc();
}
```

<span data-ttu-id="8ac5c-195">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-195">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="8ac5c-196"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-196"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="8ac5c-197">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-197">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="8ac5c-198">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-198">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="8ac5c-199">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-199">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="8ac5c-200">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-200">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="8ac5c-201">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-201">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="8ac5c-202">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-202">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="8ac5c-203">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-203">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="8ac5c-204">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-204">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="8ac5c-205">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-205">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="8ac5c-206">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-206">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="8ac5c-207">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-207">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

## <a name="use-run-and-map"></a><span data-ttu-id="8ac5c-208">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="8ac5c-208">Use, Run, and Map</span></span>

<span data-ttu-id="8ac5c-209">使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 和 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-209">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="8ac5c-210">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-210">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="8ac5c-211">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-211">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="8ac5c-212"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-212"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="8ac5c-213">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-213">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="8ac5c-214">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-214">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="8ac5c-215">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-215">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8ac5c-216">请求</span><span class="sxs-lookup"><span data-stu-id="8ac5c-216">Request</span></span>             | <span data-ttu-id="8ac5c-217">响应</span><span class="sxs-lookup"><span data-stu-id="8ac5c-217">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="8ac5c-218">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8ac5c-218">localhost:1234</span></span>      | <span data-ttu-id="8ac5c-219">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8ac5c-219">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8ac5c-220">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="8ac5c-220">localhost:1234/map1</span></span> | <span data-ttu-id="8ac5c-221">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="8ac5c-221">Map Test 1</span></span>                   |
| <span data-ttu-id="8ac5c-222">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="8ac5c-222">localhost:1234/map2</span></span> | <span data-ttu-id="8ac5c-223">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="8ac5c-223">Map Test 2</span></span>                   |
| <span data-ttu-id="8ac5c-224">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="8ac5c-224">localhost:1234/map3</span></span> | <span data-ttu-id="8ac5c-225">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8ac5c-225">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="8ac5c-226">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-226">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="8ac5c-227"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-227"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="8ac5c-228">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-228">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="8ac5c-229">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-229">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="8ac5c-230">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-230">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="8ac5c-231">请求</span><span class="sxs-lookup"><span data-stu-id="8ac5c-231">Request</span></span>                       | <span data-ttu-id="8ac5c-232">响应</span><span class="sxs-lookup"><span data-stu-id="8ac5c-232">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="8ac5c-233">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="8ac5c-233">localhost:1234</span></span>                | <span data-ttu-id="8ac5c-234">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="8ac5c-234">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="8ac5c-235">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="8ac5c-235">localhost:1234/?branch=master</span></span> | <span data-ttu-id="8ac5c-236">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="8ac5c-236">Branch used = master</span></span>         |

<span data-ttu-id="8ac5c-237">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-237">`Map` supports nesting, for example:</span></span>

```csharp
app.Map("/level1", level1App => {
    level1App.Map("/level2a", level2AApp => {
        // "/level1/level2a" processing
    });
    level1App.Map("/level2b", level2BApp => {
        // "/level1/level2b" processing
    });
});
```

<span data-ttu-id="8ac5c-238">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="8ac5c-238">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="8ac5c-239">内置中间件</span><span class="sxs-lookup"><span data-stu-id="8ac5c-239">Built-in middleware</span></span>

<span data-ttu-id="8ac5c-240">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-240">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="8ac5c-241">“顺序”  列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-241">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="8ac5c-242">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”  。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-242">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="8ac5c-243">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-243">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="8ac5c-244">中间件</span><span class="sxs-lookup"><span data-stu-id="8ac5c-244">Middleware</span></span> | <span data-ttu-id="8ac5c-245">描述</span><span class="sxs-lookup"><span data-stu-id="8ac5c-245">Description</span></span> | <span data-ttu-id="8ac5c-246">顺序</span><span class="sxs-lookup"><span data-stu-id="8ac5c-246">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="8ac5c-247">身份验证</span><span class="sxs-lookup"><span data-stu-id="8ac5c-247">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="8ac5c-248">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-248">Provides authentication support.</span></span> | <span data-ttu-id="8ac5c-249">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-249">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="8ac5c-250">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-250">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="8ac5c-251">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="8ac5c-251">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="8ac5c-252">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-252">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="8ac5c-253">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-253">Before middleware that issues cookies.</span></span> <span data-ttu-id="8ac5c-254">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-254">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="8ac5c-255">CORS</span><span class="sxs-lookup"><span data-stu-id="8ac5c-255">CORS</span></span>](xref:security/cors) | <span data-ttu-id="8ac5c-256">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-256">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="8ac5c-257">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-257">Before components that use CORS.</span></span> |
| [<span data-ttu-id="8ac5c-258">诊断</span><span class="sxs-lookup"><span data-stu-id="8ac5c-258">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="8ac5c-259">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-259">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="8ac5c-260">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-260">Before components that generate errors.</span></span> <span data-ttu-id="8ac5c-261">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-261">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="8ac5c-262">转接头</span><span class="sxs-lookup"><span data-stu-id="8ac5c-262">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="8ac5c-263">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-263">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="8ac5c-264">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-264">Before components that consume the updated fields.</span></span> <span data-ttu-id="8ac5c-265">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-265">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="8ac5c-266">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="8ac5c-266">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="8ac5c-267">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-267">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="8ac5c-268">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-268">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="8ac5c-269">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="8ac5c-269">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="8ac5c-270">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-270">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="8ac5c-271">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-271">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="8ac5c-272">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="8ac5c-272">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="8ac5c-273">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-273">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="8ac5c-274">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-274">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8ac5c-275">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="8ac5c-275">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="8ac5c-276">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-276">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="8ac5c-277">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-277">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="8ac5c-278">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-278">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="8ac5c-279">MVC</span><span class="sxs-lookup"><span data-stu-id="8ac5c-279">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="8ac5c-280">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-280">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="8ac5c-281">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-281">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="8ac5c-282">OWIN</span><span class="sxs-lookup"><span data-stu-id="8ac5c-282">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="8ac5c-283">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-283">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="8ac5c-284">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-284">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="8ac5c-285">响应缓存</span><span class="sxs-lookup"><span data-stu-id="8ac5c-285">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="8ac5c-286">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-286">Provides support for caching responses.</span></span> | <span data-ttu-id="8ac5c-287">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-287">Before components that require caching.</span></span> |
| [<span data-ttu-id="8ac5c-288">响应压缩</span><span class="sxs-lookup"><span data-stu-id="8ac5c-288">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="8ac5c-289">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-289">Provides support for compressing responses.</span></span> | <span data-ttu-id="8ac5c-290">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-290">Before components that require compression.</span></span> |
| [<span data-ttu-id="8ac5c-291">请求本地化</span><span class="sxs-lookup"><span data-stu-id="8ac5c-291">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="8ac5c-292">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-292">Provides localization support.</span></span> | <span data-ttu-id="8ac5c-293">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-293">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="8ac5c-294">终结点路由</span><span class="sxs-lookup"><span data-stu-id="8ac5c-294">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="8ac5c-295">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-295">Defines and constrains request routes.</span></span> | <span data-ttu-id="8ac5c-296">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-296">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="8ac5c-297">会话</span><span class="sxs-lookup"><span data-stu-id="8ac5c-297">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="8ac5c-298">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-298">Provides support for managing user sessions.</span></span> | <span data-ttu-id="8ac5c-299">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-299">Before components that require Session.</span></span> |
| [<span data-ttu-id="8ac5c-300">静态文件</span><span class="sxs-lookup"><span data-stu-id="8ac5c-300">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="8ac5c-301">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-301">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="8ac5c-302">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-302">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="8ac5c-303">URL 重写</span><span class="sxs-lookup"><span data-stu-id="8ac5c-303">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="8ac5c-304">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-304">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="8ac5c-305">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-305">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="8ac5c-306">WebSockets</span><span class="sxs-lookup"><span data-stu-id="8ac5c-306">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="8ac5c-307">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-307">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="8ac5c-308">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="8ac5c-308">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="8ac5c-309">其他资源</span><span class="sxs-lookup"><span data-stu-id="8ac5c-309">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
