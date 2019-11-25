---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/08/2019
uid: fundamentals/middleware/index
ms.openlocfilehash: d678f3d1f6ca10e486543a2965506236e4e61b82
ms.sourcegitcommit: 8157e5a351f49aeef3769f7d38b787b4386aad5f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/20/2019
ms.locfileid: "74239839"
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="a480e-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="a480e-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="a480e-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a480e-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a480e-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="a480e-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="a480e-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="a480e-106">Each component:</span></span>

* <span data-ttu-id="a480e-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="a480e-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="a480e-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="a480e-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="a480e-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="a480e-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="a480e-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a480e-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="a480e-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="a480e-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="a480e-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="a480e-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="a480e-113">这些可重用的类和并行匿名方法即为中间件  ，也叫中间件组件  。</span><span class="sxs-lookup"><span data-stu-id="a480e-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="a480e-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="a480e-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="a480e-115">当中间件短路时，它被称为“终端中间件”  ，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="a480e-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="a480e-116"><xref:migration/http-modules>介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="a480e-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides additional middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="a480e-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="a480e-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="a480e-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="a480e-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="a480e-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="a480e-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="a480e-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="a480e-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="a480e-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="a480e-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="a480e-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="a480e-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="a480e-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="a480e-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="a480e-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="a480e-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="a480e-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs)]

<span data-ttu-id="a480e-129">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="a480e-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="a480e-130">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="a480e-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="a480e-131">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="a480e-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="a480e-132">可通过不  调用 next  参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="a480e-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="a480e-133">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="a480e-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs)]

<span data-ttu-id="a480e-134">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”  。</span><span class="sxs-lookup"><span data-stu-id="a480e-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="a480e-135">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="a480e-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="a480e-136">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件  的作用。</span><span class="sxs-lookup"><span data-stu-id="a480e-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="a480e-137">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="a480e-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="a480e-138">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="a480e-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="a480e-139">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="a480e-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="a480e-140">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="a480e-141">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="a480e-142">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="a480e-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="a480e-143">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="a480e-143">May cause a protocol violation.</span></span> <span data-ttu-id="a480e-144">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="a480e-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="a480e-145">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="a480e-145">May corrupt the body format.</span></span> <span data-ttu-id="a480e-146">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="a480e-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="a480e-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="a480e-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

<a name="order"></a>

## <a name="middleware-order"></a><span data-ttu-id="a480e-148">中间件顺序</span><span class="sxs-lookup"><span data-stu-id="a480e-148">Middleware order</span></span>

<span data-ttu-id="a480e-149">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="a480e-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="a480e-150">此顺序对于安全性、性能和功能至关重要。 </span><span class="sxs-lookup"><span data-stu-id="a480e-150">The order is **critical** for security, performance, and functionality.</span></span>

<span data-ttu-id="a480e-151">下面的 `Startup.Configure` 方法按照建议的顺序增加与安全相关的中间件组件：</span><span class="sxs-lookup"><span data-stu-id="a480e-151">The following `Startup.Configure` method adds security related middleware components in the recommended order:</span></span>

::: moniker range=">= aspnetcore-3.0"

[!code-csharp[](index/snapshot/StartupAll3.cs?name=snippet)]

<span data-ttu-id="a480e-152">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="a480e-152">In the preceding code:</span></span>

* <span data-ttu-id="a480e-153">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="a480e-153">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="a480e-154">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="a480e-154">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="a480e-155">例如，`UseCors`、`UseAuthentication` 和 `UseAuthorization` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="a480e-155">For example, `UseCors`, `UseAuthentication`, and `UseAuthorization` must go in the order shown.</span></span>

<span data-ttu-id="a480e-156">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="a480e-156">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="a480e-157">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="a480e-157">Exception/error handling</span></span>
   * <span data-ttu-id="a480e-158">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="a480e-158">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="a480e-159">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="a480e-159">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="a480e-160">数据库错误页中间件报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="a480e-160">Database Error Page Middleware reports database runtime errors.</span></span>
   * <span data-ttu-id="a480e-161">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="a480e-161">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="a480e-162">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-162">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="a480e-163">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="a480e-163">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="a480e-164">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a480e-164">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="a480e-165">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="a480e-165">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="a480e-166">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="a480e-166">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="a480e-167">用于路由请求的路由中间件 (`UseRouting`)。</span><span class="sxs-lookup"><span data-stu-id="a480e-167">Routing Middleware (`UseRouting`) to route requests.</span></span>
1. <span data-ttu-id="a480e-168">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="a480e-168">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="a480e-169">用于授权用户访问安全资源的授权中间件 (`UseAuthorization`)。</span><span class="sxs-lookup"><span data-stu-id="a480e-169">Authorization Middleware (`UseAuthorization`) authorizes a user to access secure resources.</span></span>
1. <span data-ttu-id="a480e-170">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="a480e-170">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="a480e-171">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="a480e-171">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="a480e-172">用于将 Razor Pages 终结点添加到请求管道的终结点路由中间件（带有 `MapRazorPages` 的 `UseEndpoints`）。</span><span class="sxs-lookup"><span data-stu-id="a480e-172">Endpoint Routing Middleware (`UseEndpoints` with `MapRazorPages`) to add Razor Pages endpoints to the request pipeline.</span></span>

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

<span data-ttu-id="a480e-173">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="a480e-173">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="a480e-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="a480e-174"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="a480e-175">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-175">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="a480e-176">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="a480e-176">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="a480e-177">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="a480e-177">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="a480e-178">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="a480e-178">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="a480e-179">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="a480e-179">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="a480e-180">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="a480e-180">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="a480e-181">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="a480e-181">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="a480e-182">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="a480e-182">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="a480e-183">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="a480e-183">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="a480e-184">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="a480e-184">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="a480e-185">可以压缩 Razor Pages 响应。</span><span class="sxs-lookup"><span data-stu-id="a480e-185">The Razor Pages responses can be compressed.</span></span>

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

[!code-csharp[](index/snapshot/Startup22.cs?name=snippet)]

<span data-ttu-id="a480e-186">在上述代码中：</span><span class="sxs-lookup"><span data-stu-id="a480e-186">In the preceding code:</span></span>

* <span data-ttu-id="a480e-187">在使用[单个用户帐户](xref:security/authentication/identity)创建新的 Web 应用时未添加的中间件已被注释掉。</span><span class="sxs-lookup"><span data-stu-id="a480e-187">Middleware that is not added when creating a new web app with [individual users accounts](xref:security/authentication/identity) is commented out.</span></span>
* <span data-ttu-id="a480e-188">并非所有中间件都需要准确按照此顺序运行，但许多中间件必须遵循这个顺序。</span><span class="sxs-lookup"><span data-stu-id="a480e-188">Not every middleware needs to go in this exact order, but many do.</span></span> <span data-ttu-id="a480e-189">例如，`UseCors` 和 `UseAuthentication` 必须按照上述顺序运行。</span><span class="sxs-lookup"><span data-stu-id="a480e-189">For example, `UseCors` and `UseAuthentication` must go in the order shown.</span></span>

<span data-ttu-id="a480e-190">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="a480e-190">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

1. <span data-ttu-id="a480e-191">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="a480e-191">Exception/error handling</span></span>
   * <span data-ttu-id="a480e-192">当应用在开发环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="a480e-192">When the app runs in the Development environment:</span></span>
     * <span data-ttu-id="a480e-193">开发人员异常页中间件 (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) 报告应用运行时错误。</span><span class="sxs-lookup"><span data-stu-id="a480e-193">Developer Exception Page Middleware (<xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*>) reports app runtime errors.</span></span>
     * <span data-ttu-id="a480e-194">数据库错误页中间件 (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) 报告数据库运行时错误。</span><span class="sxs-lookup"><span data-stu-id="a480e-194">Database Error Page Middleware (`Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage`) reports database runtime errors.</span></span>
   * <span data-ttu-id="a480e-195">当应用在生产环境中运行时：</span><span class="sxs-lookup"><span data-stu-id="a480e-195">When the app runs in the Production environment:</span></span>
     * <span data-ttu-id="a480e-196">异常处理程序中间件 (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) 捕获以下中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-196">Exception Handler Middleware (<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>) catches exceptions thrown in the following middlewares.</span></span>
     * <span data-ttu-id="a480e-197">HTTP 严格传输安全协议 (HSTS) 中间件 (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) 添加 `Strict-Transport-Security` 标头。</span><span class="sxs-lookup"><span data-stu-id="a480e-197">HTTP Strict Transport Security Protocol (HSTS) Middleware (<xref:Microsoft.AspNetCore.Builder.HstsBuilderExtensions.UseHsts*>) adds the `Strict-Transport-Security` header.</span></span>
1. <span data-ttu-id="a480e-198">HTTPS 重定向中间件 (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) 将 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a480e-198">HTTPS Redirection Middleware (<xref:Microsoft.AspNetCore.Builder.HttpsPolicyBuilderExtensions.UseHttpsRedirection*>) redirects HTTP requests to HTTPS.</span></span>
1. <span data-ttu-id="a480e-199">静态文件中间件 (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) 返回静态文件，并简化进一步请求处理。</span><span class="sxs-lookup"><span data-stu-id="a480e-199">Static File Middleware (<xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>) returns static files and short-circuits further request processing.</span></span>
1. <span data-ttu-id="a480e-200">Cookie 策略中间件 (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) 使应用符合欧盟一般数据保护条例 (GDPR) 规定。</span><span class="sxs-lookup"><span data-stu-id="a480e-200">Cookie Policy Middleware (<xref:Microsoft.AspNetCore.Builder.CookiePolicyAppBuilderExtensions.UseCookiePolicy*>) conforms the app to the EU General Data Protection Regulation (GDPR) regulations.</span></span>
1. <span data-ttu-id="a480e-201">身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) 尝试对用户进行身份验证，然后才会允许用户访问安全资源。</span><span class="sxs-lookup"><span data-stu-id="a480e-201">Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>) attempts to authenticate the user before they're allowed access to secure resources.</span></span>
1. <span data-ttu-id="a480e-202">会话中间件 (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) 建立和维护会话状态。</span><span class="sxs-lookup"><span data-stu-id="a480e-202">Session Middleware (<xref:Microsoft.AspNetCore.Builder.SessionMiddlewareExtensions.UseSession*>) establishes and maintains session state.</span></span> <span data-ttu-id="a480e-203">如果应用使用会话状态，请在 Cookie 策略中间件之后和 MVC 中间件之前调用会话中间件。</span><span class="sxs-lookup"><span data-stu-id="a480e-203">If the app uses session state, call Session Middleware after Cookie Policy Middleware and before MVC Middleware.</span></span>
1. <span data-ttu-id="a480e-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) 将 MVC 添加到请求管道。</span><span class="sxs-lookup"><span data-stu-id="a480e-204">MVC (<xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvc*>) to add MVC to the request pipeline.</span></span>

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

<span data-ttu-id="a480e-205">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="a480e-205">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="a480e-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="a480e-206"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="a480e-207">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="a480e-207">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="a480e-208">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="a480e-208">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="a480e-209">静态文件中间件不  提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="a480e-209">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="a480e-210">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot  下的文件。</span><span class="sxs-lookup"><span data-stu-id="a480e-210">Any files served by Static File Middleware, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="a480e-211">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="a480e-211">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

<span data-ttu-id="a480e-212">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="a480e-212">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="a480e-213">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="a480e-213">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="a480e-214">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="a480e-214">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

<span data-ttu-id="a480e-215">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="a480e-215">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="a480e-216">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="a480e-216">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="a480e-217">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="a480e-217">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

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

## <a name="use-run-and-map"></a><span data-ttu-id="a480e-218">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="a480e-218">Use, Run, and Map</span></span>

<span data-ttu-id="a480e-219">使用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>、<xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 和 <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="a480e-219">Configure the HTTP pipeline using <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>, <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, and <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>.</span></span> <span data-ttu-id="a480e-220">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="a480e-220">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="a480e-221">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="a480e-221">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="a480e-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="a480e-222"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="a480e-223">`Map` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="a480e-223">`Map` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="a480e-224">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="a480e-224">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs)]

<span data-ttu-id="a480e-225">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="a480e-225">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="a480e-226">请求</span><span class="sxs-lookup"><span data-stu-id="a480e-226">Request</span></span>             | <span data-ttu-id="a480e-227">响应</span><span class="sxs-lookup"><span data-stu-id="a480e-227">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="a480e-228">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="a480e-228">localhost:1234</span></span>      | <span data-ttu-id="a480e-229">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="a480e-229">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="a480e-230">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="a480e-230">localhost:1234/map1</span></span> | <span data-ttu-id="a480e-231">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="a480e-231">Map Test 1</span></span>                   |
| <span data-ttu-id="a480e-232">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="a480e-232">localhost:1234/map2</span></span> | <span data-ttu-id="a480e-233">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="a480e-233">Map Test 2</span></span>                   |
| <span data-ttu-id="a480e-234">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="a480e-234">localhost:1234/map3</span></span> | <span data-ttu-id="a480e-235">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="a480e-235">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="a480e-236">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的路径段，并针对每个请求将该路径段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="a480e-236">When `Map` is used, the matched path segments are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="a480e-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="a480e-237"><xref:Microsoft.AspNetCore.Builder.MapWhenExtensions.MapWhen*> branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="a480e-238">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="a480e-238">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="a480e-239">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="a480e-239">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs)]

<span data-ttu-id="a480e-240">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="a480e-240">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="a480e-241">请求</span><span class="sxs-lookup"><span data-stu-id="a480e-241">Request</span></span>                       | <span data-ttu-id="a480e-242">响应</span><span class="sxs-lookup"><span data-stu-id="a480e-242">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="a480e-243">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="a480e-243">localhost:1234</span></span>                | <span data-ttu-id="a480e-244">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="a480e-244">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="a480e-245">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="a480e-245">localhost:1234/?branch=master</span></span> | <span data-ttu-id="a480e-246">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="a480e-246">Branch used = master</span></span>         |

<span data-ttu-id="a480e-247">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="a480e-247">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="a480e-248">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="a480e-248">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="a480e-249">内置中间件</span><span class="sxs-lookup"><span data-stu-id="a480e-249">Built-in middleware</span></span>

<span data-ttu-id="a480e-250">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="a480e-250">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="a480e-251">“顺序”  列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="a480e-251">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="a480e-252">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”  。</span><span class="sxs-lookup"><span data-stu-id="a480e-252">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="a480e-253">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="a480e-253">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="a480e-254">中间件</span><span class="sxs-lookup"><span data-stu-id="a480e-254">Middleware</span></span> | <span data-ttu-id="a480e-255">描述</span><span class="sxs-lookup"><span data-stu-id="a480e-255">Description</span></span> | <span data-ttu-id="a480e-256">顺序</span><span class="sxs-lookup"><span data-stu-id="a480e-256">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="a480e-257">身份验证</span><span class="sxs-lookup"><span data-stu-id="a480e-257">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="a480e-258">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-258">Provides authentication support.</span></span> | <span data-ttu-id="a480e-259">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-259">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="a480e-260">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-260">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="a480e-261">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="a480e-261">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="a480e-262">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="a480e-262">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="a480e-263">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-263">Before middleware that issues cookies.</span></span> <span data-ttu-id="a480e-264">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="a480e-264">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="a480e-265">CORS</span><span class="sxs-lookup"><span data-stu-id="a480e-265">CORS</span></span>](xref:security/cors) | <span data-ttu-id="a480e-266">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="a480e-266">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="a480e-267">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-267">Before components that use CORS.</span></span> |
| [<span data-ttu-id="a480e-268">诊断</span><span class="sxs-lookup"><span data-stu-id="a480e-268">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="a480e-269">提供新应用的开发人员异常页、异常处理、状态代码页和默认网页的几个单独的中间件。</span><span class="sxs-lookup"><span data-stu-id="a480e-269">Several separate middlewares that provide a developer exception page, exception handling, status code pages, and the default web page for new apps.</span></span> | <span data-ttu-id="a480e-270">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-270">Before components that generate errors.</span></span> <span data-ttu-id="a480e-271">异常终端或为新应用提供默认网页的终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-271">Terminal for exceptions or serving the default web page for new apps.</span></span> |
| [<span data-ttu-id="a480e-272">转接头</span><span class="sxs-lookup"><span data-stu-id="a480e-272">Forwarded Headers</span></span>](xref:host-and-deploy/proxy-load-balancer) | <span data-ttu-id="a480e-273">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="a480e-273">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="a480e-274">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-274">Before components that consume the updated fields.</span></span> <span data-ttu-id="a480e-275">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="a480e-275">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="a480e-276">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="a480e-276">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="a480e-277">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="a480e-277">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="a480e-278">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-278">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="a480e-279">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="a480e-279">HTTP Method Override</span></span>](xref:Microsoft.AspNetCore.Builder.HttpMethodOverrideExtensions) | <span data-ttu-id="a480e-280">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="a480e-280">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="a480e-281">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-281">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="a480e-282">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="a480e-282">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="a480e-283">将所有 HTTP 请求重定向到 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a480e-283">Redirect all HTTP requests to HTTPS.</span></span> | <span data-ttu-id="a480e-284">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-284">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="a480e-285">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="a480e-285">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="a480e-286">添加特殊响应标头的安全增强中间件。</span><span class="sxs-lookup"><span data-stu-id="a480e-286">Security enhancement middleware that adds a special response header.</span></span> | <span data-ttu-id="a480e-287">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="a480e-287">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="a480e-288">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="a480e-288">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="a480e-289">MVC</span><span class="sxs-lookup"><span data-stu-id="a480e-289">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="a480e-290">用 MVC/Razor Pages 处理请求。</span><span class="sxs-lookup"><span data-stu-id="a480e-290">Processes requests with MVC/Razor Pages.</span></span> | <span data-ttu-id="a480e-291">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-291">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="a480e-292">OWIN</span><span class="sxs-lookup"><span data-stu-id="a480e-292">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="a480e-293">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="a480e-293">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="a480e-294">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-294">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="a480e-295">响应缓存</span><span class="sxs-lookup"><span data-stu-id="a480e-295">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="a480e-296">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-296">Provides support for caching responses.</span></span> | <span data-ttu-id="a480e-297">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-297">Before components that require caching.</span></span> |
| [<span data-ttu-id="a480e-298">响应压缩</span><span class="sxs-lookup"><span data-stu-id="a480e-298">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="a480e-299">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-299">Provides support for compressing responses.</span></span> | <span data-ttu-id="a480e-300">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-300">Before components that require compression.</span></span> |
| [<span data-ttu-id="a480e-301">请求本地化</span><span class="sxs-lookup"><span data-stu-id="a480e-301">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="a480e-302">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-302">Provides localization support.</span></span> | <span data-ttu-id="a480e-303">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-303">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="a480e-304">终结点路由</span><span class="sxs-lookup"><span data-stu-id="a480e-304">Endpoint Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="a480e-305">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="a480e-305">Defines and constrains request routes.</span></span> | <span data-ttu-id="a480e-306">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-306">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="a480e-307">会话</span><span class="sxs-lookup"><span data-stu-id="a480e-307">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="a480e-308">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-308">Provides support for managing user sessions.</span></span> | <span data-ttu-id="a480e-309">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-309">Before components that require Session.</span></span> |
| [<span data-ttu-id="a480e-310">静态文件</span><span class="sxs-lookup"><span data-stu-id="a480e-310">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="a480e-311">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-311">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="a480e-312">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="a480e-312">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="a480e-313">URL 重写</span><span class="sxs-lookup"><span data-stu-id="a480e-313">URL Rewrite</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="a480e-314">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="a480e-314">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="a480e-315">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-315">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="a480e-316">WebSockets</span><span class="sxs-lookup"><span data-stu-id="a480e-316">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="a480e-317">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="a480e-317">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="a480e-318">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="a480e-318">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="additional-resources"></a><span data-ttu-id="a480e-319">其他资源</span><span class="sxs-lookup"><span data-stu-id="a480e-319">Additional resources</span></span>

* <xref:fundamentals/middleware/write>
* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
