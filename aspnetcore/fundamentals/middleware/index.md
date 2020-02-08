---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 02/02/2020
uid: fundamentals/middleware/index
ms.openlocfilehash: 6698e269e0a6480cd5a03c59f9a19da31e23bf69
ms.sourcegitcommit: 235623b6e5a5d1841139c82a11ac2b4b3f31a7a9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/08/2020
ms.locfileid: "77089144"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="df714-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="df714-103">ASP.NET Core Middleware</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="df714-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="df714-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="df714-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="df714-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="df714-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="df714-106">Each component:</span></span>

* <span data-ttu-id="df714-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="df714-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="df714-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="df714-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="df714-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="df714-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="df714-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="df714-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="df714-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="df714-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="df714-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="df714-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="df714-113">这些可重用的类和并行匿名方法即为中间件  ，也叫中间件组件  。</span><span class="sxs-lookup"><span data-stu-id="df714-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="df714-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="df714-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="df714-115">当中间件短路时，它被称为“终端中间件”  ，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="df714-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="df714-116"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="df714-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="df714-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="df714-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="df714-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="df714-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="df714-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="df714-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="df714-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="df714-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="df714-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="df714-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="df714-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="df714-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="df714-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="df714-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="df714-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="df714-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="df714-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="df714-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="df714-129">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="df714-129">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="df714-130">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="df714-130">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="df714-131">可通过不  调用 next  参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="df714-131">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="df714-132">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="df714-132">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=5-10)]

<span data-ttu-id="df714-133">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”  。</span><span class="sxs-lookup"><span data-stu-id="df714-133">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="df714-134">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="df714-134">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="df714-135">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件  的作用。</span><span class="sxs-lookup"><span data-stu-id="df714-135">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="df714-136">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="df714-136">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="df714-137">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="df714-137">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="df714-138">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="df714-138">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="df714-139">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="df714-139">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="df714-140">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="df714-140">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="df714-141">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="df714-141">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="df714-142">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="df714-142">May cause a protocol violation.</span></span> <span data-ttu-id="df714-143">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="df714-143">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="df714-144">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="df714-144">May corrupt the body format.</span></span> <span data-ttu-id="df714-145">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="df714-145">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="df714-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="df714-146"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<span data-ttu-id="df714-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托不会收到 `next` 参数。</span><span class="sxs-lookup"><span data-stu-id="df714-147"><xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegates don't receive a `next` parameter.</span></span> <span data-ttu-id="df714-148">第一个 `Run` 委托始终为终端，用于终止管道。</span><span class="sxs-lookup"><span data-stu-id="df714-148">The first `Run` delegate is always terminal and terminates the pipeline.</span></span> <span data-ttu-id="df714-149">`Run` 是一种约定。</span><span class="sxs-lookup"><span data-stu-id="df714-149">`Run` is a convention.</span></span> <span data-ttu-id="df714-150">某些中间件组件可能会公开在管道末尾运行的 `Run[Middleware]` 方法：</span><span class="sxs-lookup"><span data-stu-id="df714-150">Some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?highlight=12-15)]

<span data-ttu-id="df714-151">在前面的示例中，`Run` 委托将 `"Hello from 2nd delegate."` 写入响应，然后终止管道。</span><span class="sxs-lookup"><span data-stu-id="df714-151">In the preceding example, the `Run` delegate writes `"Hello from 2nd delegate."` to the response and then terminates the pipeline.</span></span> <span data-ttu-id="df714-152">如果在 `Run` 委托之后添加了另一个 `Use` 或 `Run` 委托，则不会调用该委托。</span><span class="sxs-lookup"><span data-stu-id="df714-152">If another `Use` or `Run` delegate is added after the `Run` delegate, it's not called.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="df714-153">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="df714-153">Middleware order</span></span>

<span data-ttu-id="df714-154">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="df714-154">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="df714-155">此顺序对于安全性、性能和功能至关重要。 </span><span class="sxs-lookup"><span data-stu-id="df714-155">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="df714-156">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="df714-156">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="df714-157">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="df714-157">In the preceding code:</span></span>

* <span data-ttu-id="df714-158">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="df714-158">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="df714-159">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="df714-159">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="df714-160">例如，`UseCors`、`UseAuthentication` 和 `UseAuthorization` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="df714-160">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="df714-161">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="df714-161">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="df714-162">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="df714-162">Exception/error handling</span></span>
   * <span data-ttu-id="df714-163">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="df714-163">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="df714-164">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="df714-164">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="df714-165">数据库错误页中间件报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="df714-165">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="df714-166">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="df714-166">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="df714-167">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="df714-167">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="df714-168">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="df714-168">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="df714-169">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="df714-169">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="df714-170">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="df714-170">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="df714-171">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="df714-171">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="df714-172">用于路由请求的路由中间件 (`UseRouting`)。</span><span class="sxs-lookup"><span data-stu-id="df714-172">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="df714-173">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="df714-173">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="df714-174">用于授权用户访问安全资源的授权中间件 (`UseAuthorization`)。</span><span class="sxs-lookup"><span data-stu-id="df714-174">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="df714-175">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="df714-175">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="df714-176">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="df714-176">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="df714-177">用于将 Razor Pages 终结点添加到请求管道的终结点路由中间件（带有 `MapRazorPages` 的 `UseEndpoints`）。</span><span class="sxs-lookup"><span data-stu-id="df714-177">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="df714-178">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="df714-178">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="df714-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="df714-179"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="df714-180">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="df714-180">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="df714-181">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="df714-181">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="df714-182">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="df714-182">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="df714-183">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="df714-183">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="df714-184">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="df714-184">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="df714-185">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="df714-185">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="df714-186">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="df714-186">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="df714-187">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="df714-187">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="df714-188">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="df714-188">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="df714-189">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="df714-189">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="df714-190">可以压缩 Razor Pages 响应。</span><span class="sxs-lookup"><span data-stu-id="df714-190">The Razor Pages responses can be compressed.</span></span>

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

<span data-ttu-id="df714-191">对于单页应用程序，SPA 中间件 <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> 通常是中间件管道中的最后一个。</span><span class="sxs-lookup"><span data-stu-id="df714-191">For single page applications, the SPA middleware <xref:Microsoft.Extensions.DependencyInjection.SpaStaticFilesExtensions.UseSpaStaticFiles*> usually comes last in the middleware pipeline.</span></span> <span data-ttu-id="df714-192">SPA 中间件处于最后的作用是：</span><span class="sxs-lookup"><span data-stu-id="df714-192">The SPA middleware comes last:</span></span>

* <span data-ttu-id="df714-193">允许所有其他中间件首先响应匹配的请求。</span><span class="sxs-lookup"><span data-stu-id="df714-193">To allow all other middlewares to respond to matching requests first.</span></span>
* <span data-ttu-id="df714-194">允许具有客户端侧路由的 SPA 针对服务器应用无法识别的所有路由运行。</span><span class="sxs-lookup"><span data-stu-id="df714-194">To allow SPAs with client-side routing to run for all routes that are unrecognized by the server app.</span></span>

<span data-ttu-id="df714-195">有关单页应用程序的详细信息，请参阅 [React](xref:spa/react) 和 [Angular](xref:spa/angular) 项目模板的指南。</span><span class="sxs-lookup"><span data-stu-id="df714-195">For more details on single page applications, see the guides for the [React](xref:spa/react) and [Angular](xref:spa/angular) project templates.</span></span>

## <a name="branch-the-middleware-pipeline"></a><span data-ttu-id="df714-196">对中间件管道进行分支</span><span class="sxs-lookup"><span data-stu-id="df714-196">Branch the middleware pipeline</span></span>

<span data-ttu-id="df714-197"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-197"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="df714-198">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-198">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="df714-199">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="df714-199">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="df714-200">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="df714-200">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="df714-201">请求</span><span class="sxs-lookup"><span data-stu-id="df714-201">Request</span></span>             | <span data-ttu-id="df714-202">响应</span><span class="sxs-lookup"><span data-stu-id="df714-202">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="df714-203">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="df714-203">localhost:1234</span></span>      | <span data-ttu-id="df714-204">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="df714-204">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="df714-205">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="df714-205">localhost:1234/map1</span></span> | <span data-ttu-id="df714-206">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="df714-206">Map Test 1</span></span>                   |
| <span data-ttu-id="df714-207">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="df714-207">localhost:1234/map2</span></span> | <span data-ttu-id="df714-208">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="df714-208">Map Test 2</span></span>                   |
| <span data-ttu-id="df714-209">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="df714-209">localhost:1234/map3</span></span> | <span data-ttu-id="df714-210">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="df714-210">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="df714-211">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="df714-211">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="df714-212">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="df714-212">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="df714-213">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="df714-213">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

<span data-ttu-id="df714-214"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-214"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="df714-215">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="df714-215">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="df714-216">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="df714-216">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?highlight=14-15)]

<span data-ttu-id="df714-217">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应：</span><span class="sxs-lookup"><span data-stu-id="df714-217">The following table shows the requests and responses from `http://localhost:1234` using the previous code:</span></span>

| <span data-ttu-id="df714-218">请求</span><span class="sxs-lookup"><span data-stu-id="df714-218">Request</span></span>                       | <span data-ttu-id="df714-219">响应</span><span class="sxs-lookup"><span data-stu-id="df714-219">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="df714-220">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="df714-220">localhost:1234</span></span>                | <span data-ttu-id="df714-221">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="df714-221">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="df714-222">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="df714-222">localhost:1234/?branch=master</span></span> | <span data-ttu-id="df714-223">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="df714-223">Branch used = master</span></span>         |

<span data-ttu-id="df714-224"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> 也基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-224"><xref:Microsoft.AspNetCore.Builder.UseWhenExtensions.UseWhen*> also branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="df714-225">与 `MapWhen` 不同的是，如果这个分支发生短路或包含终端中间件，则会重新加入主管道：</span><span class="sxs-lookup"><span data-stu-id="df714-225">Unlike with `MapWhen`, this branch is rejoined to the main pipeline if it does short-circuit or contain a terminal middleware:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupUseWhen.cs?highlight=23-24)]

<span data-ttu-id="df714-226">在前面的示例中，响应 "Hello from main pipeline."</span><span class="sxs-lookup"><span data-stu-id="df714-226">In the preceding example, a response of "Hello from main pipeline."</span></span> <span data-ttu-id="df714-227">是为所有请求编写的。</span><span class="sxs-lookup"><span data-stu-id="df714-227">is written for all requests.</span></span> <span data-ttu-id="df714-228">如果请求中包含查询字符串变量 `branch`，则在重新加入主管道之前会记录其值。</span><span class="sxs-lookup"><span data-stu-id="df714-228">If the request includes a query string variable `branch`, its value is logged before the main pipeline is rejoined.</span></span>

## <a name="built-in-middleware"></a><span data-ttu-id="df714-229">内置中间件</span><span class="sxs-lookup"><span data-stu-id="df714-229">Built-in middleware</span></span>

<span data-ttu-id="df714-230">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="df714-230">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="df714-231">“顺序”  列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="df714-231">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="df714-232">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”  。</span><span class="sxs-lookup"><span data-stu-id="df714-232">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="df714-233">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="df714-233">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="df714-234">中间件</span><span class="sxs-lookup"><span data-stu-id="df714-234">Middleware</span></span> | <span data-ttu-id="df714-235">描述</span><span class="sxs-lookup"><span data-stu-id="df714-235">Description</span></span> | <span data-ttu-id="df714-236">顺序</span><span class="sxs-lookup"><span data-stu-id="df714-236">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="df714-237">身份验证</span><span class="sxs-lookup"><span data-stu-id="df714-237">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="df714-238">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="df714-238">Provides authentication support.</span></span> | <span data-ttu-id="df714-239">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="df714-239">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="df714-240">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="df714-240">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="df714-241">授权</span><span class="sxs-lookup"><span data-stu-id="df714-241">Authorization</span></span>](xref:Microsoft.AspNetCore.Builder.AuthorizationAppBuilderExtensions.UseAuthorization*) | <span data-ttu-id="df714-242">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="df714-242">Provides authorization support.</span></span> | <span data-ttu-id="df714-243">紧接在身份验证中间件之后。</span><span class="sxs-lookup"><span data-stu-id="df714-243">Immediately after the Authentication Middleware.</span></span> |
| [<span data-ttu-id="df714-244">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="df714-244">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="df714-245">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="df714-245">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="df714-246">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-246">Before middleware that issues cookies.</span></span> <span data-ttu-id="df714-247">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="df714-247">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="df714-248">CORS</span><span class="sxs-lookup"><span data-stu-id="df714-248">CORS</span></span>](xref:security/cors) | <span data-ttu-id="df714-249">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="df714-249">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="df714-250">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-250">Before components that use CORS.</span></span> |
| [<span data-ttu-id="df714-251">诊断</span><span class="sxs-lookup"><span data-stu-id="df714-251">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="df714-252">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="df714-252">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="df714-253">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-253">Before components that generate errors.</span></span> <span data-ttu-id="df714-254">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="df714-254">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="df714-255">转接头</span><span class="sxs-lookup"><span data-stu-id="df714-255">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="df714-256">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="df714-256">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="df714-257">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-257">Before components that consume the updated fields.</span></span> <span data-ttu-id="df714-258">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="df714-258">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="df714-259">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="df714-259">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="df714-260">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="df714-260">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="df714-261">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-261">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="df714-262">标头传播</span><span class="sxs-lookup"><span data-stu-id="df714-262">Header Propagation</span></span>](xref:fundamentals/http-requests#header-propagation-middleware) | <span data-ttu-id="df714-263">将 HTTP 标头从传入的请求传播到传出的 HTTP 客户端请求中。</span><span class="sxs-lookup"><span data-stu-id="df714-263">Propagates HTTP headers from the incoming request to the outgoing HTTP Client requests.</span></span> |
| [<span data-ttu-id="df714-264">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="df714-264">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="df714-265">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="df714-265">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="df714-266">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-266">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="df714-267">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="df714-267">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="df714-268">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="df714-268">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="df714-269">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-269">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="df714-270">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="df714-270">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="df714-271">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="df714-271">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="df714-272">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="df714-272">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="df714-273">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="df714-273">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="df714-274">MVC</span><span class="sxs-lookup"><span data-stu-id="df714-274">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="df714-275">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="df714-275">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="df714-276">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-276">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="df714-277">OWIN</span><span class="sxs-lookup"><span data-stu-id="df714-277">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="df714-278">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="df714-278">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="df714-279">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-279">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="df714-280">响应缓存</span><span class="sxs-lookup"><span data-stu-id="df714-280">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="df714-281">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-281">Provides support for caching responses.</span></span> | <span data-ttu-id="df714-282">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-282">Before components that require caching.</span></span> |
| [<span data-ttu-id="df714-283">响应压缩</span><span class="sxs-lookup"><span data-stu-id="df714-283">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="df714-284">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-284">Provides support for compressing responses.</span></span> | <span data-ttu-id="df714-285">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-285">Before components that require compression.</span></span> |
| [<span data-ttu-id="df714-286">请求本地化</span><span class="sxs-lookup"><span data-stu-id="df714-286">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="df714-287">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="df714-287">Provides localization support.</span></span> | <span data-ttu-id="df714-288">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-288">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="df714-289">终结点路由</span><span class="sxs-lookup"><span data-stu-id="df714-289">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="df714-290">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="df714-290">Defines and constrains request routes.</span></span> | <span data-ttu-id="df714-291">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="df714-291">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="df714-292">SPA</span><span class="sxs-lookup"><span data-stu-id="df714-292">SPA</span></span>](xref:Microsoft.AspNetCore.Builder.SpaApplicationBuilderExtensions.UseSpa*) | <span data-ttu-id="df714-293">通过返回单页应用程序 (SPA) 的默认页面，在中间件链中处理来自这个点的所有请求</span><span class="sxs-lookup"><span data-stu-id="df714-293">Handles all requests from this point in the middleware chain by returning the default page for the Single Page Application (SPA)</span></span> | <span data-ttu-id="df714-294">在链中处于靠后位置，因此其他服务于静态文件、MVC 操作等内容的中间件占据优先位置。</span><span class="sxs-lookup"><span data-stu-id="df714-294">Late in the chain, so that other middleware for serving static files, MVC actions, etc., takes precedence.</span></span>|
| [<span data-ttu-id="df714-295">会话</span><span class="sxs-lookup"><span data-stu-id="df714-295">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="df714-296">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-296">Provides support for managing user sessions.</span></span> | <span data-ttu-id="df714-297">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-297">Before components that require Session.</span></span> | 
| [<span data-ttu-id="df714-298">静态文件</span><span class="sxs-lookup"><span data-stu-id="df714-298">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="df714-299">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="df714-299">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="df714-300">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-300">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="df714-301">URL 重写</span><span class="sxs-lookup"><span data-stu-id="df714-301">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="df714-302">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-302">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="df714-303">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-303">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="df714-304">WebSockets</span><span class="sxs-lookup"><span data-stu-id="df714-304">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="df714-305">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="df714-305">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="df714-306">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-306">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="df714-307">其他资源</span><span class="sxs-lookup"><span data-stu-id="df714-307">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="df714-308">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="df714-308">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="df714-309">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="df714-309">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="df714-310">每个组件：</span><span class="sxs-lookup"><span data-stu-id="df714-310">Each component:</span></span>

* <span data-ttu-id="df714-311">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="df714-311">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="df714-312">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="df714-312">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="df714-313">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="df714-313">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="df714-314">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="df714-314">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="df714-315">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="df714-315">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="df714-316">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="df714-316">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="df714-317">这些可重用的类和并行匿名方法即为中间件  ，也叫中间件组件  。</span><span class="sxs-lookup"><span data-stu-id="df714-317">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="df714-318">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="df714-318">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="df714-319">当中间件短路时，它被称为“终端中间件”  ，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="df714-319">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="df714-320"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="df714-320"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="df714-321">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="df714-321">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="df714-322">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="df714-322">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="df714-323">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="df714-323">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="df714-324">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="df714-324">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="df714-328">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="df714-328">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="df714-329">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="df714-329">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="df714-330">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="df714-330">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="df714-331">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="df714-331">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="df714-332">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="df714-332">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="df714-333">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="df714-333">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="df714-334">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="df714-334">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="df714-335">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="df714-335">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="df714-336">可通过不  调用 next  参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="df714-336">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="df714-337">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="df714-337">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="df714-338">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”  。</span><span class="sxs-lookup"><span data-stu-id="df714-338">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="df714-339">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="df714-339">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="df714-340">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件  的作用。</span><span class="sxs-lookup"><span data-stu-id="df714-340">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="df714-341">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="df714-341">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="df714-342">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="df714-342">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="df714-343">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="df714-343">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="df714-344">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="df714-344">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="df714-345">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="df714-345">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="df714-346">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="df714-346">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="df714-347">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="df714-347">May cause a protocol violation.</span></span> <span data-ttu-id="df714-348">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="df714-348">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="df714-349">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="df714-349">May corrupt the body format.</span></span> <span data-ttu-id="df714-350">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="df714-350">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="df714-351"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="df714-351"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="df714-352">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="df714-352">Middleware order</span></span>

<span data-ttu-id="df714-353">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="df714-353">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="df714-354">此顺序对于安全性、性能和功能至关重要。 </span><span class="sxs-lookup"><span data-stu-id="df714-354">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="df714-355">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="df714-355">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="df714-356">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="df714-356">In the preceding code:</span></span>

* <span data-ttu-id="df714-357">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="df714-357">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="df714-358">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="df714-358">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="df714-359">例如，`UseCors` 和 `UseAuthentication` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="df714-359">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="df714-360">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="df714-360">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="df714-361">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="df714-361">Exception/error handling</span></span>
   * <span data-ttu-id="df714-362">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="df714-362">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="df714-363">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="df714-363">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="df714-364">数据库错误页中间件 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="df714-364">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="df714-365">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="df714-365">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="df714-366">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="df714-366">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="df714-367">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="df714-367">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="df714-368">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="df714-368">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="df714-369">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="df714-369">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="df714-370">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="df714-370">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="df714-371">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="df714-371">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="df714-372">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="df714-372">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="df714-373">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="df714-373">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="df714-374">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) 将 MVC 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="df714-374">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="df714-375">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="df714-375">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="df714-376"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="df714-376"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="df714-377">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="df714-377">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="df714-378">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="df714-378">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="df714-379">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="df714-379">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="df714-380">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="df714-380">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="df714-381">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="df714-381">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="df714-382">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="df714-382">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="df714-383">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="df714-383">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="df714-384">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="df714-384">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="df714-385">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="df714-385">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="df714-386">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="df714-386">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="df714-387">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="df714-387">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files aren't compressed by Static File Middleware.
    app.UseStaticFiles();

    app.UseResponseCompression();

    app.UseMvcWithDefaultRoute();
}
```

## <a name="use-run-and-map"></a><span data-ttu-id="df714-388">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="df714-388">Use, Run, and Map</span></span>

<span data-ttu-id="df714-389">使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 和 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="df714-389">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="df714-390">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="df714-390">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="df714-391">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="df714-391">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="df714-392"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-392"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="df714-393">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-393">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="df714-394">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="df714-394">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="df714-395">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="df714-395">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="df714-396">请求</span><span class="sxs-lookup"><span data-stu-id="df714-396">Request</span></span>             | <span data-ttu-id="df714-397">响应</span><span class="sxs-lookup"><span data-stu-id="df714-397">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="df714-398">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="df714-398">localhost:1234</span></span>      | <span data-ttu-id="df714-399">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="df714-399">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="df714-400">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="df714-400">localhost:1234/map1</span></span> | <span data-ttu-id="df714-401">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="df714-401">Map Test 1</span></span>                   |
| <span data-ttu-id="df714-402">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="df714-402">localhost:1234/map2</span></span> | <span data-ttu-id="df714-403">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="df714-403">Map Test 2</span></span>                   |
| <span data-ttu-id="df714-404">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="df714-404">localhost:1234/map3</span></span> | <span data-ttu-id="df714-405">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="df714-405">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="df714-406">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="df714-406">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="df714-407"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="df714-407"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="df714-408">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="df714-408">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="df714-409">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="df714-409">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="df714-410">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="df714-410">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="df714-411">请求</span><span class="sxs-lookup"><span data-stu-id="df714-411">Request</span></span>                       | <span data-ttu-id="df714-412">响应</span><span class="sxs-lookup"><span data-stu-id="df714-412">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="df714-413">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="df714-413">localhost:1234</span></span>                | <span data-ttu-id="df714-414">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="df714-414">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="df714-415">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="df714-415">localhost:1234/?branch=master</span></span> | <span data-ttu-id="df714-416">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="df714-416">Branch used = master</span></span>         |

<span data-ttu-id="df714-417">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="df714-417">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="df714-418">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="df714-418">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="df714-419">内置中间件</span><span class="sxs-lookup"><span data-stu-id="df714-419">Built-in middleware</span></span>

<span data-ttu-id="df714-420">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="df714-420">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="df714-421">“顺序”  列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="df714-421">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="df714-422">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”  。</span><span class="sxs-lookup"><span data-stu-id="df714-422">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="df714-423">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="df714-423">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="df714-424">中间件</span><span class="sxs-lookup"><span data-stu-id="df714-424">Middleware</span></span> | <span data-ttu-id="df714-425">描述</span><span class="sxs-lookup"><span data-stu-id="df714-425">Description</span></span> | <span data-ttu-id="df714-426">顺序</span><span class="sxs-lookup"><span data-stu-id="df714-426">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="df714-427">身份验证</span><span class="sxs-lookup"><span data-stu-id="df714-427">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="df714-428">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="df714-428">Provides authentication support.</span></span> | <span data-ttu-id="df714-429">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="df714-429">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="df714-430">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="df714-430">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="df714-431">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="df714-431">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="df714-432">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="df714-432">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="df714-433">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-433">Before middleware that issues cookies.</span></span> <span data-ttu-id="df714-434">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="df714-434">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="df714-435">CORS</span><span class="sxs-lookup"><span data-stu-id="df714-435">CORS</span></span>](xref:security/cors) | <span data-ttu-id="df714-436">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="df714-436">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="df714-437">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-437">Before components that use CORS.</span></span> |
| [<span data-ttu-id="df714-438">诊断</span><span class="sxs-lookup"><span data-stu-id="df714-438">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="df714-439">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="df714-439">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="df714-440">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-440">Before components that generate errors.</span></span> <span data-ttu-id="df714-441">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="df714-441">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="df714-442">转接头</span><span class="sxs-lookup"><span data-stu-id="df714-442">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="df714-443">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="df714-443">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="df714-444">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-444">Before components that consume the updated fields.</span></span> <span data-ttu-id="df714-445">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="df714-445">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="df714-446">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="df714-446">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="df714-447">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="df714-447">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="df714-448">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-448">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="df714-449">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="df714-449">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="df714-450">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="df714-450">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="df714-451">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-451">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="df714-452">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="df714-452">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="df714-453">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="df714-453">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="df714-454">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-454">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="df714-455">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="df714-455">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="df714-456">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="df714-456">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="df714-457">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="df714-457">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="df714-458">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="df714-458">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="df714-459">MVC</span><span class="sxs-lookup"><span data-stu-id="df714-459">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="df714-460">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="df714-460">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="df714-461">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-461">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="df714-462">OWIN</span><span class="sxs-lookup"><span data-stu-id="df714-462">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="df714-463">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="df714-463">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="df714-464">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-464">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="df714-465">响应缓存</span><span class="sxs-lookup"><span data-stu-id="df714-465">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="df714-466">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-466">Provides support for caching responses.</span></span> | <span data-ttu-id="df714-467">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-467">Before components that require caching.</span></span> |
| [<span data-ttu-id="df714-468">响应压缩</span><span class="sxs-lookup"><span data-stu-id="df714-468">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="df714-469">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-469">Provides support for compressing responses.</span></span> | <span data-ttu-id="df714-470">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-470">Before components that require compression.</span></span> |
| [<span data-ttu-id="df714-471">请求本地化</span><span class="sxs-lookup"><span data-stu-id="df714-471">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="df714-472">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="df714-472">Provides localization support.</span></span> | <span data-ttu-id="df714-473">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-473">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="df714-474">终结点路由</span><span class="sxs-lookup"><span data-stu-id="df714-474">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="df714-475">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="df714-475">Defines and constrains request routes.</span></span> | <span data-ttu-id="df714-476">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="df714-476">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="df714-477">会话</span><span class="sxs-lookup"><span data-stu-id="df714-477">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="df714-478">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-478">Provides support for managing user sessions.</span></span> | <span data-ttu-id="df714-479">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-479">Before components that require Session.</span></span> |
| [<span data-ttu-id="df714-480">静态文件</span><span class="sxs-lookup"><span data-stu-id="df714-480">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="df714-481">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="df714-481">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="df714-482">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="df714-482">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="df714-483">URL 重写</span><span class="sxs-lookup"><span data-stu-id="df714-483">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="df714-484">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="df714-484">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="df714-485">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-485">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="df714-486">WebSockets</span><span class="sxs-lookup"><span data-stu-id="df714-486">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="df714-487">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="df714-487">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="df714-488">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="df714-488">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="df714-489">其他资源</span><span class="sxs-lookup"><span data-stu-id="df714-489">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>

::: moniker-end
