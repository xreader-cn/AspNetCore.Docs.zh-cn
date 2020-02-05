---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 5c8e9e58ab222e482ef029f5099d0a8acd07d8a6
ms.sourcegitcommit: 990a4c2e623c202a27f60bdf3902f250359c13be
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/03/2020
ms.locfileid: "76972023"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="7c37b-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="7c37b-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="7c37b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7c37b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7c37b-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="7c37b-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="7c37b-106">Each component:</span></span>

* <span data-ttu-id="7c37b-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="7c37b-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="7c37b-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="7c37b-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="7c37b-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="7c37b-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="7c37b-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="7c37b-113">这些可重用的类和并行匿名方法即为中间件  ，也叫中间件组件  。</span><span class="sxs-lookup"><span data-stu-id="7c37b-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="7c37b-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="7c37b-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="7c37b-115">当中间件短路时，它被称为“终端中间件”  ，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="7c37b-116"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="7c37b-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="7c37b-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="7c37b-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="7c37b-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="7c37b-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="7c37b-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="7c37b-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="7c37b-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="7c37b-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="7c37b-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="7c37b-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="7c37b-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="7c37b-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="7c37b-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="7c37b-129">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="7c37b-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="7c37b-130">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="7c37b-131">可通过不  调用 next  参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="7c37b-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="7c37b-132">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="7c37b-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="7c37b-133">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”  。</span><span class="sxs-lookup"><span data-stu-id="7c37b-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="7c37b-134">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="7c37b-135">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件  的作用。</span><span class="sxs-lookup"><span data-stu-id="7c37b-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="7c37b-136">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="7c37b-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="7c37b-137">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="7c37b-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="7c37b-138">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="7c37b-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="7c37b-139">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="7c37b-140">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="7c37b-141">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="7c37b-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="7c37b-142">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="7c37b-142">May cause a protocol violation.</span></span> <span data-ttu-id="7c37b-143">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="7c37b-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="7c37b-144">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="7c37b-144">May corrupt the body format.</span></span> <span data-ttu-id="7c37b-145">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="7c37b-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="7c37b-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="7c37b-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="7c37b-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托不会收到 `next` 参数。</span><span class="sxs-lookup"><span data-stu-id="7c37b-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="7c37b-148">第一个 `Run` 委托始终为终端，用于终止管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="7c37b-149">`Run` 是一种约定。</span><span class="sxs-lookup"><span data-stu-id="7c37b-149">`Run` is a convention.</span></span> <span data-ttu-id="7c37b-150">某些中间件组件可能会公开在管道末尾运行的 `Run[Middleware]` 方法：</span><span class="sxs-lookup"><span data-stu-id="7c37b-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

<span data-ttu-id="7c37b-151">在前面的示例中，`Run` 委托将 `"Hello from 2nd delegate."` 写入响应，然后终止管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="7c37b-152">如果在 `Run` 委托之后添加了另一个 `Use` 或 `Run` 委托，则不会调用该委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="7c37b-153">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="7c37b-153">Middleware order</span></span>

<span data-ttu-id="7c37b-154">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="7c37b-154">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="7c37b-155">此顺序对于安全性、性能和功能至关重要。 </span><span class="sxs-lookup"><span data-stu-id="7c37b-155">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="7c37b-156">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="7c37b-156">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="7c37b-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="7c37b-157">In the preceding code:</span></span>

* <span data-ttu-id="7c37b-158">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="7c37b-158">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="7c37b-159">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="7c37b-159">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="7c37b-160">例如，`UseCors`、`UseAuthentication` 和 `UseAuthorization` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="7c37b-160">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="7c37b-161">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="7c37b-161">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="7c37b-162">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="7c37b-162">Exception/error handling</span></span>
   * <span data-ttu-id="7c37b-163">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="7c37b-163">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="7c37b-164">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="7c37b-164">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="7c37b-165">数据库错误页中间件报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="7c37b-165">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="7c37b-166">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="7c37b-166">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="7c37b-167">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-167">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="7c37b-168">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="7c37b-168">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="7c37b-169">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7c37b-169">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="7c37b-170">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="7c37b-170">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="7c37b-171">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="7c37b-171">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="7c37b-172">用于路由请求的路由中间件 (`UseRouting`)。</span><span class="sxs-lookup"><span data-stu-id="7c37b-172">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="7c37b-173">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="7c37b-173">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="7c37b-174">用于授权用户访问安全资源的授权中间件 (`UseAuthorization`)。</span><span class="sxs-lookup"><span data-stu-id="7c37b-174">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="7c37b-175">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="7c37b-175">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="7c37b-176">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-176">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="7c37b-177">用于将 Razor Pages 终结点添加到请求管道的终结点路由中间件（带有 `MapRazorPages` 的 `UseEndpoints`）。</span><span class="sxs-lookup"><span data-stu-id="7c37b-177">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="7c37b-178">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="7c37b-178">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7c37b-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="7c37b-180">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-180">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="7c37b-181">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-181">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="7c37b-182">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="7c37b-182">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="7c37b-183">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-183">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="7c37b-184">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="7c37b-184">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="7c37b-185">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="7c37b-185">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="7c37b-186">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="7c37b-186">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="7c37b-187">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="7c37b-187">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="7c37b-188">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="7c37b-188">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="7c37b-189">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-189">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="7c37b-190">可以压缩 Razor Pages 响应。</span><span class="sxs-lookup"><span data-stu-id="7c37b-190">The Razor Pages responses can be compressed.</span></span>

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

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="7c37b-191">对中间件管道进行分支</span><span class="sxs-lookup"><span data-stu-id="7c37b-191">Branch the middleware pipeline</span></span>

<span data-ttu-id="7c37b-192"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-192"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="7c37b-193">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-193">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="7c37b-194">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-194">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="7c37b-195">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="7c37b-195">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="7c37b-196">请求</span><span class="sxs-lookup"><span data-stu-id="7c37b-196">Request</span></span>             | <span data-ttu-id="7c37b-197">响应</span><span class="sxs-lookup"><span data-stu-id="7c37b-197">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="7c37b-198">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="7c37b-198">localhost:1234</span></span>      | <span data-ttu-id="7c37b-199">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="7c37b-199">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="7c37b-200">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="7c37b-200">localhost:1234/map1</span></span> | <span data-ttu-id="7c37b-201">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="7c37b-201">Map Test 1</span></span>                   |
| <span data-ttu-id="7c37b-202">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="7c37b-202">localhost:1234/map2</span></span> | <span data-ttu-id="7c37b-203">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="7c37b-203">Map Test 2</span></span>                   |
| <span data-ttu-id="7c37b-204">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="7c37b-204">localhost:1234/map3</span></span> | <span data-ttu-id="7c37b-205">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="7c37b-205">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="7c37b-206">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="7c37b-206">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="7c37b-207">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="7c37b-207">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="7c37b-208">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="7c37b-208">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="7c37b-209"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-209"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="7c37b-210">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-210">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="7c37b-211">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="7c37b-211">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="7c37b-212">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应：</span><span class="sxs-lookup"><span data-stu-id="7c37b-212">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="7c37b-213">请求</span><span class="sxs-lookup"><span data-stu-id="7c37b-213">Request</span></span>                       | <span data-ttu-id="7c37b-214">响应</span><span class="sxs-lookup"><span data-stu-id="7c37b-214">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="7c37b-215">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="7c37b-215">localhost:1234</span></span>                | <span data-ttu-id="7c37b-216">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="7c37b-216">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="7c37b-217">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="7c37b-217">localhost:1234/?branch=master</span></span> | <span data-ttu-id="7c37b-218">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="7c37b-218">Branch used = master</span></span>         |

<span data-ttu-id="7c37b-219"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> 也基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-219"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="7c37b-220">与 `MapWhen` 不同的是，如果这个分支发生短路或包含终端中间件，则会重新加入主管道：</span><span class="sxs-lookup"><span data-stu-id="7c37b-220">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it does short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

<span data-ttu-id="7c37b-221">在前面的示例中，响应 "Hello from main pipeline."</span><span class="sxs-lookup"><span data-stu-id="7c37b-221">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="7c37b-222">是为所有请求编写的。</span><span class="sxs-lookup"><span data-stu-id="7c37b-222">is written for all requests.</span></span> <span data-ttu-id="7c37b-223">如果请求中包含查询字符串变量 `branch`，则在重新加入主管道之前会记录其值。</span><span class="sxs-lookup"><span data-stu-id="7c37b-223">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="7c37b-224">内置中间件</span><span class="sxs-lookup"><span data-stu-id="7c37b-224">Built-in middleware</span></span>

<span data-ttu-id="7c37b-225">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-225">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="7c37b-226">“顺序”  列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-226">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="7c37b-227">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”  。</span><span class="sxs-lookup"><span data-stu-id="7c37b-227">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="7c37b-228">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="7c37b-228">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="7c37b-229">中间件</span><span class="sxs-lookup"><span data-stu-id="7c37b-229">Middleware</span></span> | <span data-ttu-id="7c37b-230">描述</span><span class="sxs-lookup"><span data-stu-id="7c37b-230">Description</span></span> | <span data-ttu-id="7c37b-231">顺序</span><span class="sxs-lookup"><span data-stu-id="7c37b-231">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="7c37b-232">身份验证</span><span class="sxs-lookup"><span data-stu-id="7c37b-232">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="7c37b-233">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-233">Provides authentication support.</span></span> | <span data-ttu-id="7c37b-234">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-234">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="7c37b-235">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-235">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="7c37b-236">授权</span><span class="sxs-lookup"><span data-stu-id="7c37b-236">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="7c37b-237">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-237">Provides authorization support.</span></span> | <span data-ttu-id="7c37b-238">紧接在身份验证中间件之后。</span><span class="sxs-lookup"><span data-stu-id="7c37b-238">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="7c37b-239">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="7c37b-239">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="7c37b-240">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="7c37b-240">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="7c37b-241">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-241">Before middleware that issues cookies.</span></span> <span data-ttu-id="7c37b-242">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="7c37b-242">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="7c37b-243">CORS</span><span class="sxs-lookup"><span data-stu-id="7c37b-243">CORS</span></span>](xref:security/cors) | <span data-ttu-id="7c37b-244">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="7c37b-244">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="7c37b-245">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-245">Before components that use CORS.</span></span> |
| [<span data-ttu-id="7c37b-246">诊断</span><span class="sxs-lookup"><span data-stu-id="7c37b-246">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="7c37b-247">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-247">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="7c37b-248">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-248">Before components that generate errors.</span></span> <span data-ttu-id="7c37b-249">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-249">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="7c37b-250">转接头</span><span class="sxs-lookup"><span data-stu-id="7c37b-250">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="7c37b-251">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-251">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="7c37b-252">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-252">Before components that consume the updated fields.</span></span> <span data-ttu-id="7c37b-253">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="7c37b-253">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="7c37b-254">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="7c37b-254">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="7c37b-255">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="7c37b-255">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="7c37b-256">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-256">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="7c37b-257">标头传播</span><span class="sxs-lookup"><span data-stu-id="7c37b-257">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="7c37b-258">将 HTTP 标头从传入的请求传播到传出的 HTTP 客户端请求中。</span><span class="sxs-lookup"><span data-stu-id="7c37b-258">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="7c37b-259">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="7c37b-259">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="7c37b-260">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="7c37b-260">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="7c37b-261">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-261">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="7c37b-262">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="7c37b-262">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="7c37b-263">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7c37b-263">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="7c37b-264">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-264">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="7c37b-265">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="7c37b-265">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="7c37b-266">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-266">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="7c37b-267">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="7c37b-267">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="7c37b-268">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="7c37b-268">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="7c37b-269">MVC</span><span class="sxs-lookup"><span data-stu-id="7c37b-269">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="7c37b-270">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-270">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="7c37b-271">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-271">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="7c37b-272">OWIN</span><span class="sxs-lookup"><span data-stu-id="7c37b-272">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="7c37b-273">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-273">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="7c37b-274">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-274">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="7c37b-275">响应缓存</span><span class="sxs-lookup"><span data-stu-id="7c37b-275">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="7c37b-276">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-276">Provides support for caching responses.</span></span> | <span data-ttu-id="7c37b-277">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-277">Before components that require caching.</span></span> |
| [<span data-ttu-id="7c37b-278">响应压缩</span><span class="sxs-lookup"><span data-stu-id="7c37b-278">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="7c37b-279">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-279">Provides support for compressing responses.</span></span> | <span data-ttu-id="7c37b-280">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-280">Before components that require compression.</span></span> |
| [<span data-ttu-id="7c37b-281">请求本地化</span><span class="sxs-lookup"><span data-stu-id="7c37b-281">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="7c37b-282">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-282">Provides localization support.</span></span> | <span data-ttu-id="7c37b-283">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-283">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="7c37b-284">终结点路由</span><span class="sxs-lookup"><span data-stu-id="7c37b-284">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="7c37b-285">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="7c37b-285">Defines and constrains request routes.</span></span> | <span data-ttu-id="7c37b-286">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-286">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="7c37b-287">会话</span><span class="sxs-lookup"><span data-stu-id="7c37b-287">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="7c37b-288">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-288">Provides support for managing user sessions.</span></span> | <span data-ttu-id="7c37b-289">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-289">Before components that require Session.</span></span> |
| [<span data-ttu-id="7c37b-290">静态文件</span><span class="sxs-lookup"><span data-stu-id="7c37b-290">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="7c37b-291">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-291">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="7c37b-292">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-292">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="7c37b-293">URL 重写</span><span class="sxs-lookup"><span data-stu-id="7c37b-293">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="7c37b-294">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-294">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="7c37b-295">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-295">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="7c37b-296">WebSockets</span><span class="sxs-lookup"><span data-stu-id="7c37b-296">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="7c37b-297">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="7c37b-297">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="7c37b-298">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-298">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="7c37b-299">其他资源</span><span class="sxs-lookup"><span data-stu-id="7c37b-299">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7c37b-300">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="7c37b-300">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="7c37b-301">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-301">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="7c37b-302">每个组件：</span><span class="sxs-lookup"><span data-stu-id="7c37b-302">Each component:</span></span>

* <span data-ttu-id="7c37b-303">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-303">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="7c37b-304">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-304">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="7c37b-305">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-305">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="7c37b-306">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-306">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="7c37b-307">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-307">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="7c37b-308">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="7c37b-308">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="7c37b-309">这些可重用的类和并行匿名方法即为中间件  ，也叫中间件组件  。</span><span class="sxs-lookup"><span data-stu-id="7c37b-309">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="7c37b-310">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="7c37b-310">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="7c37b-311">当中间件短路时，它被称为“终端中间件”  ，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-311">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="7c37b-312"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="7c37b-312"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="7c37b-313">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="7c37b-313">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="7c37b-314">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="7c37b-314">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="7c37b-315">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="7c37b-315">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="7c37b-316">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="7c37b-316">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="7c37b-320">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-320">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="7c37b-321">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-321">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="7c37b-322">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-322">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="7c37b-323">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-323">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="7c37b-324">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-324">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="7c37b-325">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-325">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="7c37b-326">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="7c37b-326">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="7c37b-327">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="7c37b-327">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="7c37b-328">可通过不  调用 next  参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="7c37b-328">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="7c37b-329">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="7c37b-329">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="7c37b-330">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”  。</span><span class="sxs-lookup"><span data-stu-id="7c37b-330">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="7c37b-331">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-331">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="7c37b-332">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件  的作用。</span><span class="sxs-lookup"><span data-stu-id="7c37b-332">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="7c37b-333">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="7c37b-333">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="7c37b-334">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="7c37b-334">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="7c37b-335">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="7c37b-335">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="7c37b-336">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-336">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="7c37b-337">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-337">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="7c37b-338">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="7c37b-338">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="7c37b-339">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="7c37b-339">May cause a protocol violation.</span></span> <span data-ttu-id="7c37b-340">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="7c37b-340">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="7c37b-341">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="7c37b-341">May corrupt the body format.</span></span> <span data-ttu-id="7c37b-342">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="7c37b-342">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="7c37b-343"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="7c37b-343"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="7c37b-344">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="7c37b-344">Middleware order</span></span>

<span data-ttu-id="7c37b-345">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="7c37b-345">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="7c37b-346">此顺序对于安全性、性能和功能至关重要。 </span><span class="sxs-lookup"><span data-stu-id="7c37b-346">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="7c37b-347">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="7c37b-347">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="7c37b-348">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="7c37b-348">In the preceding code:</span></span>

* <span data-ttu-id="7c37b-349">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="7c37b-349">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="7c37b-350">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="7c37b-350">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="7c37b-351">例如，`UseCors` 和 `UseAuthentication` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="7c37b-351">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="7c37b-352">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="7c37b-352">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="7c37b-353">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="7c37b-353">Exception/error handling</span></span>
   * <span data-ttu-id="7c37b-354">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="7c37b-354">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="7c37b-355">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="7c37b-355">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="7c37b-356">数据库错误页中间件 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="7c37b-356">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="7c37b-357">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="7c37b-357">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="7c37b-358">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-358">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="7c37b-359">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="7c37b-359">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="7c37b-360">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7c37b-360">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="7c37b-361">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="7c37b-361">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="7c37b-362">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="7c37b-362">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="7c37b-363">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="7c37b-363">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="7c37b-364">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="7c37b-364">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="7c37b-365">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-365">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="7c37b-366">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) 将 MVC 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-366">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="7c37b-367">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="7c37b-367">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="7c37b-368"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-368"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="7c37b-369">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="7c37b-369">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="7c37b-370">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-370">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="7c37b-371">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="7c37b-371">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="7c37b-372">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-372">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="7c37b-373">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="7c37b-373">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="7c37b-374">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="7c37b-374">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="7c37b-375">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="7c37b-375">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="7c37b-376">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="7c37b-376">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="7c37b-377">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="7c37b-377">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="7c37b-378">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-378">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="7c37b-379">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="7c37b-379">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="7c37b-380">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="7c37b-380">Use, Run, and Map</span></span>

<span data-ttu-id="7c37b-381">使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 和 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="7c37b-381">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="7c37b-382">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="7c37b-382">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="7c37b-383">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="7c37b-383">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="7c37b-384"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-384"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="7c37b-385">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-385">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="7c37b-386">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-386">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="7c37b-387">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="7c37b-387">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="7c37b-388">请求</span><span class="sxs-lookup"><span data-stu-id="7c37b-388">Request</span></span>             | <span data-ttu-id="7c37b-389">响应</span><span class="sxs-lookup"><span data-stu-id="7c37b-389">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="7c37b-390">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="7c37b-390">localhost:1234</span></span>      | <span data-ttu-id="7c37b-391">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="7c37b-391">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="7c37b-392">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="7c37b-392">localhost:1234/map1</span></span> | <span data-ttu-id="7c37b-393">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="7c37b-393">Map Test 1</span></span>                   |
| <span data-ttu-id="7c37b-394">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="7c37b-394">localhost:1234/map2</span></span> | <span data-ttu-id="7c37b-395">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="7c37b-395">Map Test 2</span></span>                   |
| <span data-ttu-id="7c37b-396">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="7c37b-396">localhost:1234/map3</span></span> | <span data-ttu-id="7c37b-397">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="7c37b-397">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="7c37b-398">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="7c37b-398">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="7c37b-399"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-399"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="7c37b-400">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="7c37b-400">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="7c37b-401">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="7c37b-401">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="7c37b-402">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="7c37b-402">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="7c37b-403">请求</span><span class="sxs-lookup"><span data-stu-id="7c37b-403">Request</span></span>                       | <span data-ttu-id="7c37b-404">响应</span><span class="sxs-lookup"><span data-stu-id="7c37b-404">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="7c37b-405">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="7c37b-405">localhost:1234</span></span>                | <span data-ttu-id="7c37b-406">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="7c37b-406">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="7c37b-407">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="7c37b-407">localhost:1234/?branch=master</span></span> | <span data-ttu-id="7c37b-408">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="7c37b-408">Branch used = master</span></span>         |

<span data-ttu-id="7c37b-409">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="7c37b-409">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="7c37b-410">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="7c37b-410">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="7c37b-411">内置中间件</span><span class="sxs-lookup"><span data-stu-id="7c37b-411">Built-in middleware</span></span>

<span data-ttu-id="7c37b-412">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-412">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="7c37b-413">“顺序”  列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-413">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="7c37b-414">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”  。</span><span class="sxs-lookup"><span data-stu-id="7c37b-414">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="7c37b-415">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="7c37b-415">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="7c37b-416">中间件</span><span class="sxs-lookup"><span data-stu-id="7c37b-416">Middleware</span></span> | <span data-ttu-id="7c37b-417">描述</span><span class="sxs-lookup"><span data-stu-id="7c37b-417">Description</span></span> | <span data-ttu-id="7c37b-418">顺序</span><span class="sxs-lookup"><span data-stu-id="7c37b-418">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="7c37b-419">身份验证</span><span class="sxs-lookup"><span data-stu-id="7c37b-419">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="7c37b-420">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-420">Provides authentication support.</span></span> | <span data-ttu-id="7c37b-421">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-421">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="7c37b-422">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-422">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="7c37b-423">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="7c37b-423">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="7c37b-424">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="7c37b-424">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="7c37b-425">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-425">Before middleware that issues cookies.</span></span> <span data-ttu-id="7c37b-426">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="7c37b-426">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="7c37b-427">CORS</span><span class="sxs-lookup"><span data-stu-id="7c37b-427">CORS</span></span>](xref:security/cors) | <span data-ttu-id="7c37b-428">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="7c37b-428">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="7c37b-429">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-429">Before components that use CORS.</span></span> |
| [<span data-ttu-id="7c37b-430">诊断</span><span class="sxs-lookup"><span data-stu-id="7c37b-430">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="7c37b-431">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-431">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="7c37b-432">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-432">Before components that generate errors.</span></span> <span data-ttu-id="7c37b-433">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-433">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="7c37b-434">转接头</span><span class="sxs-lookup"><span data-stu-id="7c37b-434">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="7c37b-435">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-435">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="7c37b-436">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-436">Before components that consume the updated fields.</span></span> <span data-ttu-id="7c37b-437">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="7c37b-437">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="7c37b-438">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="7c37b-438">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="7c37b-439">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="7c37b-439">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="7c37b-440">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-440">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="7c37b-441">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="7c37b-441">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="7c37b-442">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="7c37b-442">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="7c37b-443">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-443">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="7c37b-444">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="7c37b-444">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="7c37b-445">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="7c37b-445">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="7c37b-446">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-446">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="7c37b-447">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="7c37b-447">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="7c37b-448">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="7c37b-448">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="7c37b-449">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="7c37b-449">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="7c37b-450">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="7c37b-450">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="7c37b-451">MVC</span><span class="sxs-lookup"><span data-stu-id="7c37b-451">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="7c37b-452">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="7c37b-452">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="7c37b-453">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-453">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="7c37b-454">OWIN</span><span class="sxs-lookup"><span data-stu-id="7c37b-454">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="7c37b-455">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="7c37b-455">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="7c37b-456">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-456">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="7c37b-457">响应缓存</span><span class="sxs-lookup"><span data-stu-id="7c37b-457">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="7c37b-458">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-458">Provides support for caching responses.</span></span> | <span data-ttu-id="7c37b-459">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-459">Before components that require caching.</span></span> |
| [<span data-ttu-id="7c37b-460">响应压缩</span><span class="sxs-lookup"><span data-stu-id="7c37b-460">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="7c37b-461">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-461">Provides support for compressing responses.</span></span> | <span data-ttu-id="7c37b-462">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-462">Before components that require compression.</span></span> |
| [<span data-ttu-id="7c37b-463">请求本地化</span><span class="sxs-lookup"><span data-stu-id="7c37b-463">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="7c37b-464">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-464">Provides localization support.</span></span> | <span data-ttu-id="7c37b-465">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-465">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="7c37b-466">终结点路由</span><span class="sxs-lookup"><span data-stu-id="7c37b-466">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="7c37b-467">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="7c37b-467">Defines and constrains request routes.</span></span> | <span data-ttu-id="7c37b-468">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-468">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="7c37b-469">会话</span><span class="sxs-lookup"><span data-stu-id="7c37b-469">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="7c37b-470">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-470">Provides support for managing user sessions.</span></span> | <span data-ttu-id="7c37b-471">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-471">Before components that require Session.</span></span> |
| [<span data-ttu-id="7c37b-472">静态文件</span><span class="sxs-lookup"><span data-stu-id="7c37b-472">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="7c37b-473">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-473">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="7c37b-474">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="7c37b-474">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="7c37b-475">URL 重写</span><span class="sxs-lookup"><span data-stu-id="7c37b-475">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="7c37b-476">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="7c37b-476">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="7c37b-477">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-477">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="7c37b-478">WebSockets</span><span class="sxs-lookup"><span data-stu-id="7c37b-478">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="7c37b-479">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="7c37b-479">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="7c37b-480">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="7c37b-480">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="7c37b-481">其他资源</span><span class="sxs-lookup"><span data-stu-id="7c37b-481">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
