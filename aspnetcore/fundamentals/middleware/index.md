---
title: ASP.NET Core 中间件
author: rick-anderson
description: 了解 ASP.NET Core 中间件和请求管道。
ms.author: riande
ms.custom: mvc
ms.date: 02/17/2019
uid: fundamentals/middleware/index
---
# <a name="aspnet-core-middleware"></a><span data-ttu-id="61df2-103">ASP.NET Core 中间件</span><span class="sxs-lookup"><span data-stu-id="61df2-103">ASP.NET Core Middleware</span></span>

<span data-ttu-id="61df2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="61df2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="61df2-105">中间件是一种装配到应用管道以处理请求和响应的软件。</span><span class="sxs-lookup"><span data-stu-id="61df2-105">Middleware is software that's assembled into an app pipeline to handle requests and responses.</span></span> <span data-ttu-id="61df2-106">每个组件：</span><span class="sxs-lookup"><span data-stu-id="61df2-106">Each component:</span></span>

* <span data-ttu-id="61df2-107">选择是否将请求传递到管道中的下一个组件。</span><span class="sxs-lookup"><span data-stu-id="61df2-107">Chooses whether to pass the request to the next component in the pipeline.</span></span>
* <span data-ttu-id="61df2-108">可在管道中的下一个组件前后执行工作。</span><span class="sxs-lookup"><span data-stu-id="61df2-108">Can perform work before and after the next component in the pipeline.</span></span>

<span data-ttu-id="61df2-109">请求委托用于生成请求管道。</span><span class="sxs-lookup"><span data-stu-id="61df2-109">Request delegates are used to build the request pipeline.</span></span> <span data-ttu-id="61df2-110">请求委托处理每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="61df2-110">The request delegates handle each HTTP request.</span></span>

<span data-ttu-id="61df2-111">使用 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 和 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 扩展方法来配置请求委托。</span><span class="sxs-lookup"><span data-stu-id="61df2-111">Request delegates are configured using <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*>, <xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*>, and <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> extension methods.</span></span> <span data-ttu-id="61df2-112">可将一个单独的请求委托并行指定为匿名方法（称为并行中间件），或在可重用的类中对其进行定义。</span><span class="sxs-lookup"><span data-stu-id="61df2-112">An individual request delegate can be specified in-line as an anonymous method (called in-line middleware), or it can be defined in a reusable class.</span></span> <span data-ttu-id="61df2-113">这些可重用的类和并行匿名方法即为中间件，也叫中间件组件。</span><span class="sxs-lookup"><span data-stu-id="61df2-113">These reusable classes and in-line anonymous methods are *middleware*, also called *middleware components*.</span></span> <span data-ttu-id="61df2-114">请求管道中的每个中间件组件负责调用管道中的下一个组件，或使管道短路。</span><span class="sxs-lookup"><span data-stu-id="61df2-114">Each middleware component in the request pipeline is responsible for invoking the next component in the pipeline or short-circuiting the pipeline.</span></span> <span data-ttu-id="61df2-115">当中间件短路时，它被称为“终端中间件”，因为它阻止中间件进一步处理请求。</span><span class="sxs-lookup"><span data-stu-id="61df2-115">When a middleware short-circuits, it's called a *terminal middleware* because it prevents further middleware from processing the request.</span></span>

<span data-ttu-id="61df2-116"><xref:migration/http-modules> 介绍了 ASP.NET Core 和 ASP.NET 4.x 中请求管道之间的差异，并提供了更多的中间件示例。</span><span class="sxs-lookup"><span data-stu-id="61df2-116"><xref:migration/http-modules> explains the difference between request pipelines in ASP.NET Core and ASP.NET 4.x and provides more middleware samples.</span></span>

## <a name="create-a-middleware-pipeline-with-iapplicationbuilder"></a><span data-ttu-id="61df2-117">使用 IApplicationBuilder 创建中间件管道</span><span class="sxs-lookup"><span data-stu-id="61df2-117">Create a middleware pipeline with IApplicationBuilder</span></span>

<span data-ttu-id="61df2-118">ASP.NET Core 请求管道包含一系列请求委托，依次调用。</span><span class="sxs-lookup"><span data-stu-id="61df2-118">The ASP.NET Core request pipeline consists of a sequence of request delegates, called one after the other.</span></span> <span data-ttu-id="61df2-119">下图演示了这一概念。</span><span class="sxs-lookup"><span data-stu-id="61df2-119">The following diagram demonstrates the concept.</span></span> <span data-ttu-id="61df2-120">沿黑色箭头执行。</span><span class="sxs-lookup"><span data-stu-id="61df2-120">The thread of execution follows the black arrows.</span></span>

![请求处理模式显示请求到达、通过三个中间件进行处理以及响应离开应用。](index/_static/request-delegate-pipeline.png)

<span data-ttu-id="61df2-124">每个委托均可在下一个委托前后执行操作。</span><span class="sxs-lookup"><span data-stu-id="61df2-124">Each delegate can perform operations before and after the next delegate.</span></span> <span data-ttu-id="61df2-125">应尽早在管道中调用异常处理委托，这样它们就能捕获在管道的后期阶段发生的异常。</span><span class="sxs-lookup"><span data-stu-id="61df2-125">Exception-handling delegates should be called early in the pipeline, so they can catch exceptions that occur in later stages of the pipeline.</span></span>

<span data-ttu-id="61df2-126">尽可能简单的 ASP.NET Core 应用设置了处理所有请求的单个请求委托。</span><span class="sxs-lookup"><span data-stu-id="61df2-126">The simplest possible ASP.NET Core app sets up a single request delegate that handles all requests.</span></span> <span data-ttu-id="61df2-127">这种情况不包括实际请求管道。</span><span class="sxs-lookup"><span data-stu-id="61df2-127">This case doesn't include an actual request pipeline.</span></span> <span data-ttu-id="61df2-128">调用单个匿名函数以响应每个 HTTP 请求。</span><span class="sxs-lookup"><span data-stu-id="61df2-128">Instead, a single anonymous function is called in response to every HTTP request.</span></span>

[!code-csharp[](index/snapshot/Middleware/Startup.cs?name=snippet1)]

<span data-ttu-id="61df2-129">第一个 <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> 委托终止了管道。</span><span class="sxs-lookup"><span data-stu-id="61df2-129">The first <xref:Microsoft.AspNetCore.Builder.RunExtensions.Run*> delegate terminates the pipeline.</span></span>

<span data-ttu-id="61df2-130">用 <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*> 将多个请求委托链接在一起。</span><span class="sxs-lookup"><span data-stu-id="61df2-130">Chain multiple request delegates together with <xref:Microsoft.AspNetCore.Builder.UseExtensions.Use*>.</span></span> <span data-ttu-id="61df2-131">`next` 参数表示管道中的下一个委托。</span><span class="sxs-lookup"><span data-stu-id="61df2-131">The `next` parameter represents the next delegate in the pipeline.</span></span> <span data-ttu-id="61df2-132">可通过不调用 next 参数使管道短路。</span><span class="sxs-lookup"><span data-stu-id="61df2-132">You can short-circuit the pipeline by *not* calling the *next* parameter.</span></span> <span data-ttu-id="61df2-133">通常可在下一个委托前后执行操作，如以下示例所示：</span><span class="sxs-lookup"><span data-stu-id="61df2-133">You can typically perform actions both before and after the next delegate, as the following example demonstrates:</span></span>

[!code-csharp[](index/snapshot/Chain/Startup.cs?name=snippet1)]

<span data-ttu-id="61df2-134">当委托不将请求传递给下一个委托时，它被称为“让请求管道短路”。</span><span class="sxs-lookup"><span data-stu-id="61df2-134">When a delegate doesn't pass a request to the next delegate, it's called *short-circuiting the request pipeline*.</span></span> <span data-ttu-id="61df2-135">通常需要短路，因为这样可以避免不必要的工作。</span><span class="sxs-lookup"><span data-stu-id="61df2-135">Short-circuiting is often desirable because it avoids unnecessary work.</span></span> <span data-ttu-id="61df2-136">例如，[静态文件中间件](xref:fundamentals/static-files)可以处理对静态文件的请求，并让管道的其余部分短路，从而起到终端中间件的作用。</span><span class="sxs-lookup"><span data-stu-id="61df2-136">For example, [Static File Middleware](xref:fundamentals/static-files) can act as a *terminal middleware* by processing a request for a static file and short-circuiting the rest of the pipeline.</span></span> <span data-ttu-id="61df2-137">如果中间件添加到管道中，且位于终止进一步处理的中间件前，它们仍处理 `next.Invoke` 语句后面的代码。</span><span class="sxs-lookup"><span data-stu-id="61df2-137">Middleware added to the pipeline before the middleware that terminates further processing still processes code after their `next.Invoke` statements.</span></span> <span data-ttu-id="61df2-138">不过，请参阅下面有关尝试对已发送的响应执行写入操作的警告。</span><span class="sxs-lookup"><span data-stu-id="61df2-138">However, see the following warning about attempting to write to a response that has already been sent.</span></span>

> [!WARNING]
> <span data-ttu-id="61df2-139">在向客户端发送响应后，请勿调用 `next.Invoke`。</span><span class="sxs-lookup"><span data-stu-id="61df2-139">Don't call `next.Invoke` after the response has been sent to the client.</span></span> <span data-ttu-id="61df2-140">响应启动后，针对 <xref:Microsoft.AspNetCore.Http.HttpResponse> 的更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="61df2-140">Changes to <xref:Microsoft.AspNetCore.Http.HttpResponse> after the response has started throw an exception.</span></span> <span data-ttu-id="61df2-141">例如，设置标头和状态代码更改将引发异常。</span><span class="sxs-lookup"><span data-stu-id="61df2-141">For example, changes such as setting headers and a status code throw an exception.</span></span> <span data-ttu-id="61df2-142">调用 `next` 后写入响应正文：</span><span class="sxs-lookup"><span data-stu-id="61df2-142">Writing to the response body after calling `next`:</span></span>
>
> * <span data-ttu-id="61df2-143">可能导致违反协议。</span><span class="sxs-lookup"><span data-stu-id="61df2-143">May cause a protocol violation.</span></span> <span data-ttu-id="61df2-144">例如，写入的长度超过规定的 `Content-Length`。</span><span class="sxs-lookup"><span data-stu-id="61df2-144">For example, writing more than the stated `Content-Length`.</span></span>
> * <span data-ttu-id="61df2-145">可能损坏正文格式。</span><span class="sxs-lookup"><span data-stu-id="61df2-145">May corrupt the body format.</span></span> <span data-ttu-id="61df2-146">例如，向 CSS 文件中写入 HTML 页脚。</span><span class="sxs-lookup"><span data-stu-id="61df2-146">For example, writing an HTML footer to a CSS file.</span></span>
>
> <span data-ttu-id="61df2-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> 是一个有用的提示，指示是否已发送标头或已写入正文。</span><span class="sxs-lookup"><span data-stu-id="61df2-147"><xref:Microsoft.AspNetCore.Http.HttpResponse.HasStarted*> is a useful hint to indicate if headers have been sent or the body has been written to.</span></span>

## <a name="order"></a><span data-ttu-id="61df2-148">顺序</span><span class="sxs-lookup"><span data-stu-id="61df2-148">Order</span></span>

<span data-ttu-id="61df2-149">向 `Startup.Configure` 方法添加中间件组件的顺序定义了针对请求调用这些组件的顺序，以及响应的相反顺序。</span><span class="sxs-lookup"><span data-stu-id="61df2-149">The order that middleware components are added in the `Startup.Configure` method defines the order in which the middleware components are invoked on requests and the reverse order for the response.</span></span> <span data-ttu-id="61df2-150">此排序对于安全性、性能和功能至关重要。</span><span class="sxs-lookup"><span data-stu-id="61df2-150">The order is critical for security, performance, and functionality.</span></span>

<span data-ttu-id="61df2-151">以下 `Startup.Configure` 方法将为常见应用方案添加中间件组件：</span><span class="sxs-lookup"><span data-stu-id="61df2-151">The following `Startup.Configure` method adds middleware components for common app scenarios:</span></span>

::: moniker range=">= aspnetcore-2.0"

1. <span data-ttu-id="61df2-152">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="61df2-152">Exception/error handling</span></span>
1. <span data-ttu-id="61df2-153">HTTP 严格传输安全协议</span><span class="sxs-lookup"><span data-stu-id="61df2-153">HTTP Strict Transport Security Protocol</span></span>
1. <span data-ttu-id="61df2-154">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="61df2-154">HTTPS redirection</span></span>
1. <span data-ttu-id="61df2-155">静态文件服务器</span><span class="sxs-lookup"><span data-stu-id="61df2-155">Static file server</span></span>
1. <span data-ttu-id="61df2-156">Cookie 策略实施</span><span class="sxs-lookup"><span data-stu-id="61df2-156">Cookie policy enforcement</span></span>
1. <span data-ttu-id="61df2-157">身份验证</span><span class="sxs-lookup"><span data-stu-id="61df2-157">Authentication</span></span>
1. <span data-ttu-id="61df2-158">会话</span><span class="sxs-lookup"><span data-stu-id="61df2-158">Session</span></span>
1. <span data-ttu-id="61df2-159">MVC</span><span class="sxs-lookup"><span data-stu-id="61df2-159">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    if (env.IsDevelopment())
    {
        // When the app runs in the Development environment:
        //   Use the Developer Exception Page to report app runtime errors.
        //   Use the Database Error Page to report database runtime errors.
        app.UseDeveloperExceptionPage();
        app.UseDatabaseErrorPage();
    }
    else
    {
        // When the app doesn't run in the Development environment:
        //   Enable the Exception Handler Middleware to catch exceptions
        //     thrown in the following middlewares.
        //   Use the HTTP Strict Transport Security Protocol (HSTS)
        //     Middleware.
        app.UseExceptionHandler("/Error");
        app.UseHsts();
    }

    // Use HTTPS Redirection Middleware to redirect HTTP requests to HTTPS.
    app.UseHttpsRedirection();

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Use Cookie Policy Middleware to conform to EU General Data 
    // Protection Regulation (GDPR) regulations.
    app.UseCookiePolicy();

    // Authenticate before the user accesses secure resources.
    app.UseAuthentication();

    // If the app uses session state, call Session Middleware after Cookie 
    // Policy Middleware and before MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvc();
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.0"

1. <span data-ttu-id="61df2-160">异常/错误处理</span><span class="sxs-lookup"><span data-stu-id="61df2-160">Exception/error handling</span></span>
1. <span data-ttu-id="61df2-161">静态文件</span><span class="sxs-lookup"><span data-stu-id="61df2-161">Static files</span></span>
1. <span data-ttu-id="61df2-162">身份验证</span><span class="sxs-lookup"><span data-stu-id="61df2-162">Authentication</span></span>
1. <span data-ttu-id="61df2-163">会话</span><span class="sxs-lookup"><span data-stu-id="61df2-163">Session</span></span>
1. <span data-ttu-id="61df2-164">MVC</span><span class="sxs-lookup"><span data-stu-id="61df2-164">MVC</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Enable the Exception Handler Middleware to catch exceptions
    //   thrown in the following middlewares.
    app.UseExceptionHandler("/Home/Error");

    // Return static files and end the pipeline.
    app.UseStaticFiles();

    // Authenticate before you access secure resources.
    app.UseIdentity();

    // If the app uses session state, call UseSession before 
    // MVC Middleware.
    app.UseSession();

    // Add MVC to the request pipeline.
    app.UseMvcWithDefaultRoute();
}
```

::: moniker-end

<span data-ttu-id="61df2-165">在前面的示例代码中，每个中间件扩展方法都通过 <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> 命名空间在 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 上公开。</span><span class="sxs-lookup"><span data-stu-id="61df2-165">In the preceding example code, each middleware extension method is exposed on <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> through the <xref:Microsoft.AspNetCore.Builder?displayProperty=fullName> namespace.</span></span>

<span data-ttu-id="61df2-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 是添加到管道的第一个中间件组件。</span><span class="sxs-lookup"><span data-stu-id="61df2-166"><xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> is the first middleware component added to the pipeline.</span></span> <span data-ttu-id="61df2-167">因此，异常处理程序中间件可捕获稍后调用中发生的任何异常。</span><span class="sxs-lookup"><span data-stu-id="61df2-167">Therefore, the Exception Handler Middleware catches any exceptions that occur in later calls.</span></span>

<span data-ttu-id="61df2-168">尽早在管道中调用静态文件中间件，以便它可以处理请求并使其短路，而无需通过剩余组件。</span><span class="sxs-lookup"><span data-stu-id="61df2-168">Static File Middleware is called early in the pipeline so that it can handle requests and short-circuit without going through the remaining components.</span></span> <span data-ttu-id="61df2-169">静态文件中间件不提供授权检查。</span><span class="sxs-lookup"><span data-stu-id="61df2-169">The Static File Middleware provides **no** authorization checks.</span></span> <span data-ttu-id="61df2-170">可公开访问由静态文件中间件服务的任何文件，包括 wwwroot 下的文件。</span><span class="sxs-lookup"><span data-stu-id="61df2-170">Any files served by it, including those under *wwwroot*, are publicly available.</span></span> <span data-ttu-id="61df2-171">若要了解如何保护静态文件，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="61df2-171">For an approach to secure static files, see <xref:fundamentals/static-files>.</span></span>

::: moniker range=">= aspnetcore-2.0"

<span data-ttu-id="61df2-172">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的身份验证中间件 (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>)。</span><span class="sxs-lookup"><span data-stu-id="61df2-172">If the request isn't handled by the Static File Middleware, it's passed on to the Authentication Middleware (<xref:Microsoft.AspNetCore.Builder.AuthAppBuilderExtensions.UseAuthentication*>), which performs authentication.</span></span> <span data-ttu-id="61df2-173">身份验证不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="61df2-173">Authentication doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="61df2-174">虽然身份验证中间件对请求进行身份验证，但仅在 MVC 选择特定 Razor 页或 MVC 控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="61df2-174">Although Authentication Middleware authenticates requests, authorization (and rejection) occurs only after MVC selects a specific Razor Page or MVC controller and action.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="61df2-175">如果静态文件中间件未处理请求，则请求将被传递给执行身份验证的标识中间件 (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>)。</span><span class="sxs-lookup"><span data-stu-id="61df2-175">If the request isn't handled by Static File Middleware, it's passed on to the Identity Middleware (<xref:Microsoft.AspNetCore.Builder.BuilderExtensions.UseIdentity*>), which performs authentication.</span></span> <span data-ttu-id="61df2-176">标识不使未经身份验证的请求短路。</span><span class="sxs-lookup"><span data-stu-id="61df2-176">Identity doesn't short-circuit unauthenticated requests.</span></span> <span data-ttu-id="61df2-177">虽然标识对请求进行身份验证，但仅在 MVC 选择特定控制器和操作后，才发生授权（和拒绝）。</span><span class="sxs-lookup"><span data-stu-id="61df2-177">Although Identity authenticates requests, authorization (and rejection) occurs only after MVC selects a specific controller and action.</span></span>

::: moniker-end

<span data-ttu-id="61df2-178">以下示例演示中间件排序，其中静态文件的请求在响应压缩中间件前由静态文件中间件进行处理。</span><span class="sxs-lookup"><span data-stu-id="61df2-178">The following example demonstrates a middleware order where requests for static files are handled by Static File Middleware before Response Compression Middleware.</span></span> <span data-ttu-id="61df2-179">使用此中间件顺序不压缩静态文件。</span><span class="sxs-lookup"><span data-stu-id="61df2-179">Static files aren't compressed with this middleware order.</span></span> <span data-ttu-id="61df2-180">可以压缩来自 <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> 的 MVC 响应。</span><span class="sxs-lookup"><span data-stu-id="61df2-180">The MVC responses from <xref:Microsoft.AspNetCore.Builder.MvcApplicationBuilderExtensions.UseMvcWithDefaultRoute*> can be compressed.</span></span>

```csharp
public void Configure(IApplicationBuilder app)
{
    // Static files not compressed by Static File Middleware.
    app.UseStaticFiles();
    app.UseResponseCompression();
    app.UseMvcWithDefaultRoute();
}
```

### <a name="use-run-and-map"></a><span data-ttu-id="61df2-181">Use、Run 和 Map</span><span class="sxs-lookup"><span data-stu-id="61df2-181">Use, Run, and Map</span></span>

<span data-ttu-id="61df2-182">使用 `Use`、`Run` 和 `Map` 配置 HTTP 管道。</span><span class="sxs-lookup"><span data-stu-id="61df2-182">Configure the HTTP pipeline using `Use`, `Run`, and `Map`.</span></span> <span data-ttu-id="61df2-183">`Use` 方法可使管道短路（即不调用 `next` 请求委托）。</span><span class="sxs-lookup"><span data-stu-id="61df2-183">The `Use` method can short-circuit the pipeline (that is, if it doesn't call a `next` request delegate).</span></span> <span data-ttu-id="61df2-184">`Run` 是一种约定，并且某些中间件组件可公开在管道末尾运行的 `Run[Middleware]` 方法。</span><span class="sxs-lookup"><span data-stu-id="61df2-184">`Run` is a convention, and some middleware components may expose `Run[Middleware]` methods that run at the end of the pipeline.</span></span>

<span data-ttu-id="61df2-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> 扩展用作约定来创建管道分支。</span><span class="sxs-lookup"><span data-stu-id="61df2-185"><xref:Microsoft.AspNetCore.Builder.MapExtensions.Map*> extensions are used as a convention for branching the pipeline.</span></span> <span data-ttu-id="61df2-186">`Map*` 基于给定请求路径的匹配项来创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="61df2-186">`Map*` branches the request pipeline based on matches of the given request path.</span></span> <span data-ttu-id="61df2-187">如果请求路径以给定路径开头，则执行分支。</span><span class="sxs-lookup"><span data-stu-id="61df2-187">If the request path starts with the given path, the branch is executed.</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMap.cs?name=snippet1)]

<span data-ttu-id="61df2-188">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="61df2-188">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="61df2-189">请求</span><span class="sxs-lookup"><span data-stu-id="61df2-189">Request</span></span>             | <span data-ttu-id="61df2-190">响应</span><span class="sxs-lookup"><span data-stu-id="61df2-190">Response</span></span>                     |
| ------------------- | ---------------------------- |
| <span data-ttu-id="61df2-191">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="61df2-191">localhost:1234</span></span>      | <span data-ttu-id="61df2-192">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="61df2-192">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="61df2-193">localhost:1234/map1</span><span class="sxs-lookup"><span data-stu-id="61df2-193">localhost:1234/map1</span></span> | <span data-ttu-id="61df2-194">Map Test 1</span><span class="sxs-lookup"><span data-stu-id="61df2-194">Map Test 1</span></span>                   |
| <span data-ttu-id="61df2-195">localhost:1234/map2</span><span class="sxs-lookup"><span data-stu-id="61df2-195">localhost:1234/map2</span></span> | <span data-ttu-id="61df2-196">Map Test 2</span><span class="sxs-lookup"><span data-stu-id="61df2-196">Map Test 2</span></span>                   |
| <span data-ttu-id="61df2-197">localhost:1234/map3</span><span class="sxs-lookup"><span data-stu-id="61df2-197">localhost:1234/map3</span></span> | <span data-ttu-id="61df2-198">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="61df2-198">Hello from non-Map delegate.</span></span> |

<span data-ttu-id="61df2-199">使用 `Map` 时，将从 `HttpRequest.Path` 中删除匹配的线段，并针对每个请求将该线段追加到 `HttpRequest.PathBase`。</span><span class="sxs-lookup"><span data-stu-id="61df2-199">When `Map` is used, the matched path segment(s) are removed from `HttpRequest.Path` and appended to `HttpRequest.PathBase` for each request.</span></span>

<span data-ttu-id="61df2-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) 基于给定谓词的结果创建请求管道分支。</span><span class="sxs-lookup"><span data-stu-id="61df2-200">[MapWhen](/dotnet/api/microsoft.aspnetcore.builder.mapwhenextensions) branches the request pipeline based on the result of the given predicate.</span></span> <span data-ttu-id="61df2-201">`Func<HttpContext, bool>` 类型的任何谓词均可用于将请求映射到管道的新分支。</span><span class="sxs-lookup"><span data-stu-id="61df2-201">Any predicate of type `Func<HttpContext, bool>` can be used to map requests to a new branch of the pipeline.</span></span> <span data-ttu-id="61df2-202">在以下示例中，谓词用于检测查询字符串变量 `branch` 是否存在：</span><span class="sxs-lookup"><span data-stu-id="61df2-202">In the following example, a predicate is used to detect the presence of a query string variable `branch`:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMapWhen.cs?name=snippet1)]

<span data-ttu-id="61df2-203">下表使用前面的代码显示来自 `http://localhost:1234` 的请求和响应。</span><span class="sxs-lookup"><span data-stu-id="61df2-203">The following table shows the requests and responses from `http://localhost:1234` using the previous code.</span></span>

| <span data-ttu-id="61df2-204">请求</span><span class="sxs-lookup"><span data-stu-id="61df2-204">Request</span></span>                       | <span data-ttu-id="61df2-205">响应</span><span class="sxs-lookup"><span data-stu-id="61df2-205">Response</span></span>                     |
| ----------------------------- | ---------------------------- |
| <span data-ttu-id="61df2-206">localhost:1234</span><span class="sxs-lookup"><span data-stu-id="61df2-206">localhost:1234</span></span>                | <span data-ttu-id="61df2-207">Hello from non-Map delegate.</span><span class="sxs-lookup"><span data-stu-id="61df2-207">Hello from non-Map delegate.</span></span> |
| <span data-ttu-id="61df2-208">localhost:1234/?branch=master</span><span class="sxs-lookup"><span data-stu-id="61df2-208">localhost:1234/?branch=master</span></span> | <span data-ttu-id="61df2-209">Branch used = master</span><span class="sxs-lookup"><span data-stu-id="61df2-209">Branch used = master</span></span>         |

<span data-ttu-id="61df2-210">`Map` 支持嵌套，例如：</span><span class="sxs-lookup"><span data-stu-id="61df2-210">`Map` supports nesting, for example:</span></span>

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

<span data-ttu-id="61df2-211">此外，`Map` 还可同时匹配多个段：</span><span class="sxs-lookup"><span data-stu-id="61df2-211">`Map` can also match multiple segments at once:</span></span>

[!code-csharp[](index/snapshot/Chain/StartupMultiSeg.cs?name=snippet1&highlight=13)]

## <a name="built-in-middleware"></a><span data-ttu-id="61df2-212">内置中间件</span><span class="sxs-lookup"><span data-stu-id="61df2-212">Built-in middleware</span></span>

<span data-ttu-id="61df2-213">ASP.NET Core 附带以下中间件组件。</span><span class="sxs-lookup"><span data-stu-id="61df2-213">ASP.NET Core ships with the following middleware components.</span></span> <span data-ttu-id="61df2-214">“顺序”列提供备注，以说明中间件在请求处理管道中的放置，以及中间件可能会终止请求处理的条件。</span><span class="sxs-lookup"><span data-stu-id="61df2-214">The *Order* column provides notes on middleware placement in the request processing pipeline and under what conditions the middleware may terminate request processing.</span></span> <span data-ttu-id="61df2-215">如果中间件让请求处理管道短路，并阻止下游中间件进一步处理请求，它被称为“终端中间件”。</span><span class="sxs-lookup"><span data-stu-id="61df2-215">When a middleware short-circuits the request processing pipeline and prevents further downstream middleware from processing a request, it's called a *terminal middleware*.</span></span> <span data-ttu-id="61df2-216">若要详细了解短路，请参阅[使用 IApplicationBuilder 创建中间件管道](#create-a-middleware-pipeline-with-iapplicationbuilder)部分。</span><span class="sxs-lookup"><span data-stu-id="61df2-216">For more information on short-circuiting, see the [Create a middleware pipeline with IApplicationBuilder](#create-a-middleware-pipeline-with-iapplicationbuilder) section.</span></span>

| <span data-ttu-id="61df2-217">中间件</span><span class="sxs-lookup"><span data-stu-id="61df2-217">Middleware</span></span> | <span data-ttu-id="61df2-218">描述</span><span class="sxs-lookup"><span data-stu-id="61df2-218">Description</span></span> | <span data-ttu-id="61df2-219">顺序</span><span class="sxs-lookup"><span data-stu-id="61df2-219">Order</span></span> |
| ---------- | ----------- | ----- |
| [<span data-ttu-id="61df2-220">身份验证</span><span class="sxs-lookup"><span data-stu-id="61df2-220">Authentication</span></span>](xref:security/authentication/identity) | <span data-ttu-id="61df2-221">提供身份验证支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-221">Provides authentication support.</span></span> | <span data-ttu-id="61df2-222">在需要 `HttpContext.User` 之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-222">Before `HttpContext.User` is needed.</span></span> <span data-ttu-id="61df2-223">OAuth 回叫的终端。</span><span class="sxs-lookup"><span data-stu-id="61df2-223">Terminal for OAuth callbacks.</span></span> |
| [<span data-ttu-id="61df2-224">Cookie 策略</span><span class="sxs-lookup"><span data-stu-id="61df2-224">Cookie Policy</span></span>](xref:security/gdpr) | <span data-ttu-id="61df2-225">跟踪用户是否同意存储个人信息，并强制实施 cookie 字段（如 `secure` 和 `SameSite`）的最低标准。</span><span class="sxs-lookup"><span data-stu-id="61df2-225">Tracks consent from users for storing personal information and enforces minimum standards for cookie fields, such as `secure` and `SameSite`.</span></span> | <span data-ttu-id="61df2-226">在发出 cookie 的中间件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-226">Before middleware that issues cookies.</span></span> <span data-ttu-id="61df2-227">示例：身份验证、会话、MVC (TempData)。</span><span class="sxs-lookup"><span data-stu-id="61df2-227">Examples: Authentication, Session, MVC (TempData).</span></span> |
| [<span data-ttu-id="61df2-228">CORS</span><span class="sxs-lookup"><span data-stu-id="61df2-228">CORS</span></span>](xref:security/cors) | <span data-ttu-id="61df2-229">配置跨域资源共享。</span><span class="sxs-lookup"><span data-stu-id="61df2-229">Configures Cross-Origin Resource Sharing.</span></span> | <span data-ttu-id="61df2-230">在使用 CORS 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-230">Before components that use CORS.</span></span> |
| [<span data-ttu-id="61df2-231">诊断</span><span class="sxs-lookup"><span data-stu-id="61df2-231">Diagnostics</span></span>](xref:fundamentals/error-handling) | <span data-ttu-id="61df2-232">配置诊断。</span><span class="sxs-lookup"><span data-stu-id="61df2-232">Configures diagnostics.</span></span> | <span data-ttu-id="61df2-233">在生成错误的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-233">Before components that generate errors.</span></span> |
| [<span data-ttu-id="61df2-234">转接头</span><span class="sxs-lookup"><span data-stu-id="61df2-234">Forwarded Headers</span></span>](/dotnet/api/microsoft.aspnetcore.builder.forwardedheadersextensions) | <span data-ttu-id="61df2-235">将代理标头转发到当前请求。</span><span class="sxs-lookup"><span data-stu-id="61df2-235">Forwards proxied headers onto the current request.</span></span> | <span data-ttu-id="61df2-236">在使用已更新字段的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-236">Before components that consume the updated fields.</span></span> <span data-ttu-id="61df2-237">示例：方案、主机、客户端 IP、方法。</span><span class="sxs-lookup"><span data-stu-id="61df2-237">Examples: scheme, host, client IP, method.</span></span> |
| [<span data-ttu-id="61df2-238">运行状况检查</span><span class="sxs-lookup"><span data-stu-id="61df2-238">Health Check</span></span>](xref:host-and-deploy/health-checks) | <span data-ttu-id="61df2-239">检查 ASP.NET Core 应用及其依赖项的运行状况，如检查数据库可用性。</span><span class="sxs-lookup"><span data-stu-id="61df2-239">Checks the health of an ASP.NET Core app and its dependencies, such as checking database availability.</span></span> | <span data-ttu-id="61df2-240">如果请求与运行状况检查终结点匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="61df2-240">Terminal if a request matches a health check endpoint.</span></span> |
| [<span data-ttu-id="61df2-241">HTTP 方法重写</span><span class="sxs-lookup"><span data-stu-id="61df2-241">HTTP Method Override</span></span>](/dotnet/api/microsoft.aspnetcore.builder.httpmethodoverrideextensions) | <span data-ttu-id="61df2-242">允许传入 POST 请求重写方法。</span><span class="sxs-lookup"><span data-stu-id="61df2-242">Allows an incoming POST request to override the method.</span></span> | <span data-ttu-id="61df2-243">在使用已更新方法的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-243">Before components that consume the updated method.</span></span> |
| [<span data-ttu-id="61df2-244">HTTPS 重定向</span><span class="sxs-lookup"><span data-stu-id="61df2-244">HTTPS Redirection</span></span>](xref:security/enforcing-ssl#require-https) | <span data-ttu-id="61df2-245">将所有 HTTP 请求重定向到 HTTPS（ASP.NET Core 2.1 或更高版本）。</span><span class="sxs-lookup"><span data-stu-id="61df2-245">Redirect all HTTP requests to HTTPS (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="61df2-246">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-246">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="61df2-247">HTTP 严格传输安全性 (HSTS)</span><span class="sxs-lookup"><span data-stu-id="61df2-247">HTTP Strict Transport Security (HSTS)</span></span>](xref:security/enforcing-ssl#http-strict-transport-security-protocol-hsts) | <span data-ttu-id="61df2-248">添加特殊响应标头的安全增强中间件（ASP.NET Core 2.1 或更高版本）。</span><span class="sxs-lookup"><span data-stu-id="61df2-248">Security enhancement middleware that adds a special response header (ASP.NET Core 2.1 or later).</span></span> | <span data-ttu-id="61df2-249">在发送响应之前，修改请求的组件之后。</span><span class="sxs-lookup"><span data-stu-id="61df2-249">Before responses are sent and after components that modify requests.</span></span> <span data-ttu-id="61df2-250">示例：转接头、URL 重写。</span><span class="sxs-lookup"><span data-stu-id="61df2-250">Examples: Forwarded Headers, URL Rewriting.</span></span> |
| [<span data-ttu-id="61df2-251">MVC</span><span class="sxs-lookup"><span data-stu-id="61df2-251">MVC</span></span>](xref:mvc/overview) | <span data-ttu-id="61df2-252">用 MVC/Razor Pages 处理请求（ASP.NET Core 2.0 或更高版本）。</span><span class="sxs-lookup"><span data-stu-id="61df2-252">Processes requests with MVC/Razor Pages (ASP.NET Core 2.0 or later).</span></span> | <span data-ttu-id="61df2-253">如果请求与路由匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="61df2-253">Terminal if a request matches a route.</span></span> |
| [<span data-ttu-id="61df2-254">OWIN</span><span class="sxs-lookup"><span data-stu-id="61df2-254">OWIN</span></span>](xref:fundamentals/owin) | <span data-ttu-id="61df2-255">与基于 OWIN 的应用、服务器和中间件进行互操作。</span><span class="sxs-lookup"><span data-stu-id="61df2-255">Interop with OWIN-based apps, servers, and middleware.</span></span> | <span data-ttu-id="61df2-256">如果 OWIN 中间件处理完请求，则为终端。</span><span class="sxs-lookup"><span data-stu-id="61df2-256">Terminal if the OWIN Middleware fully processes the request.</span></span> |
| [<span data-ttu-id="61df2-257">响应缓存</span><span class="sxs-lookup"><span data-stu-id="61df2-257">Response Caching</span></span>](xref:performance/caching/middleware) | <span data-ttu-id="61df2-258">提供对缓存响应的支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-258">Provides support for caching responses.</span></span> | <span data-ttu-id="61df2-259">在需要缓存的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-259">Before components that require caching.</span></span> |
| [<span data-ttu-id="61df2-260">响应压缩</span><span class="sxs-lookup"><span data-stu-id="61df2-260">Response Compression</span></span>](xref:performance/response-compression) | <span data-ttu-id="61df2-261">提供对压缩响应的支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-261">Provides support for compressing responses.</span></span> | <span data-ttu-id="61df2-262">在需要压缩的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-262">Before components that require compression.</span></span> |
| [<span data-ttu-id="61df2-263">请求本地化</span><span class="sxs-lookup"><span data-stu-id="61df2-263">Request Localization</span></span>](xref:fundamentals/localization) | <span data-ttu-id="61df2-264">提供本地化支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-264">Provides localization support.</span></span> | <span data-ttu-id="61df2-265">在对本地化敏感的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-265">Before localization sensitive components.</span></span> |
| [<span data-ttu-id="61df2-266">路由</span><span class="sxs-lookup"><span data-stu-id="61df2-266">Routing</span></span>](xref:fundamentals/routing) | <span data-ttu-id="61df2-267">定义和约束请求路由。</span><span class="sxs-lookup"><span data-stu-id="61df2-267">Defines and constrains request routes.</span></span> | <span data-ttu-id="61df2-268">用于匹配路由的终端。</span><span class="sxs-lookup"><span data-stu-id="61df2-268">Terminal for matching routes.</span></span> |
| [<span data-ttu-id="61df2-269">会话</span><span class="sxs-lookup"><span data-stu-id="61df2-269">Session</span></span>](xref:fundamentals/app-state) | <span data-ttu-id="61df2-270">提供对管理用户会话的支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-270">Provides support for managing user sessions.</span></span> | <span data-ttu-id="61df2-271">在需要会话的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-271">Before components that require Session.</span></span> |
| [<span data-ttu-id="61df2-272">静态文件</span><span class="sxs-lookup"><span data-stu-id="61df2-272">Static Files</span></span>](xref:fundamentals/static-files) | <span data-ttu-id="61df2-273">为提供静态文件和目录浏览提供支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-273">Provides support for serving static files and directory browsing.</span></span> | <span data-ttu-id="61df2-274">如果请求与文件匹配，则为终端。</span><span class="sxs-lookup"><span data-stu-id="61df2-274">Terminal if a request matches a file.</span></span> |
| [<span data-ttu-id="61df2-275">URL 重写</span><span class="sxs-lookup"><span data-stu-id="61df2-275">URL Rewriting</span></span>](xref:fundamentals/url-rewriting) | <span data-ttu-id="61df2-276">提供对重写 URL 和重定向请求的支持。</span><span class="sxs-lookup"><span data-stu-id="61df2-276">Provides support for rewriting URLs and redirecting requests.</span></span> | <span data-ttu-id="61df2-277">在使用 URL 的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-277">Before components that consume the URL.</span></span> |
| [<span data-ttu-id="61df2-278">WebSockets</span><span class="sxs-lookup"><span data-stu-id="61df2-278">WebSockets</span></span>](xref:fundamentals/websockets) | <span data-ttu-id="61df2-279">启用 WebSockets 协议。</span><span class="sxs-lookup"><span data-stu-id="61df2-279">Enables the WebSockets protocol.</span></span> | <span data-ttu-id="61df2-280">在接受 WebSocket 请求所需的组件之前。</span><span class="sxs-lookup"><span data-stu-id="61df2-280">Before components that are required to accept WebSocket requests.</span></span> |

## <a name="write-middleware"></a><span data-ttu-id="61df2-281">编写中间件</span><span class="sxs-lookup"><span data-stu-id="61df2-281">Write middleware</span></span>

<span data-ttu-id="61df2-282">通常，中间件封装在类中，并且通过扩展方法公开。</span><span class="sxs-lookup"><span data-stu-id="61df2-282">Middleware is generally encapsulated in a class and exposed with an extension method.</span></span> <span data-ttu-id="61df2-283">请考虑以下中间件，该中间件通过查询字符串设置当前请求的区域性：</span><span class="sxs-lookup"><span data-stu-id="61df2-283">Consider the following middleware, which sets the culture for the current request from a query string:</span></span>

[!code-csharp[](index/snapshot/Culture/StartupCulture.cs?name=snippet1)]

<span data-ttu-id="61df2-284">以上示例代码用于演示创建中间件组件。</span><span class="sxs-lookup"><span data-stu-id="61df2-284">The preceding sample code is used to demonstrate creating a middleware component.</span></span> <span data-ttu-id="61df2-285">有关 ASP.NET Core 的内置本地化支持，请参阅 <xref:fundamentals/localization>。</span><span class="sxs-lookup"><span data-stu-id="61df2-285">For ASP.NET Core's built-in localization support, see <xref:fundamentals/localization>.</span></span>

<span data-ttu-id="61df2-286">可通过传入区域性（如 `http://localhost:7997/?culture=no`）测试中间件。</span><span class="sxs-lookup"><span data-stu-id="61df2-286">You can test the middleware by passing in the culture, for example `http://localhost:7997/?culture=no`.</span></span>

<span data-ttu-id="61df2-287">以下代码将中间件委托移动到类：</span><span class="sxs-lookup"><span data-stu-id="61df2-287">The following code moves the middleware delegate to a class:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddleware.cs)]

::: moniker range="< aspnetcore-2.0"

<span data-ttu-id="61df2-288">中间件 `Task` 方法的名称必须是 `Invoke`。</span><span class="sxs-lookup"><span data-stu-id="61df2-288">The middleware `Task` method's name must be `Invoke`.</span></span> <span data-ttu-id="61df2-289">在 ASP.NET Core 2.0 或更高版本中，该名称可以为 `Invoke` 或 `InvokeAsync`。</span><span class="sxs-lookup"><span data-stu-id="61df2-289">In ASP.NET Core 2.0 or later, the name can be either `Invoke` or `InvokeAsync`.</span></span>

::: moniker-end

<span data-ttu-id="61df2-290">以下扩展方法通过 <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder> 公开中间件：</span><span class="sxs-lookup"><span data-stu-id="61df2-290">The following extension method exposes the middleware through <xref:Microsoft.AspNetCore.Builder.IApplicationBuilder>:</span></span>

[!code-csharp[](index/snapshot/Culture/RequestCultureMiddlewareExtensions.cs)]

<span data-ttu-id="61df2-291">以下代码通过 `Startup.Configure` 调用中间件：</span><span class="sxs-lookup"><span data-stu-id="61df2-291">The following code calls the middleware from `Startup.Configure`:</span></span>

[!code-csharp[](index/snapshot/Culture/Startup.cs?name=snippet1&highlight=5)]

<span data-ttu-id="61df2-292">中间件应通过在其构造函数中公开其依赖项来遵循[显式依赖项原则](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)。</span><span class="sxs-lookup"><span data-stu-id="61df2-292">Middleware should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies) by exposing its dependencies in its constructor.</span></span> <span data-ttu-id="61df2-293">在每个应用程序生存期构造一次中间件。</span><span class="sxs-lookup"><span data-stu-id="61df2-293">Middleware is constructed once per *application lifetime*.</span></span> <span data-ttu-id="61df2-294">如果需要与请求中的中间件共享服务，请参阅[按请求依赖项](#per-request-dependencies)部分。</span><span class="sxs-lookup"><span data-stu-id="61df2-294">See the [Per-request dependencies](#per-request-dependencies) section if you need to share services with middleware within a request.</span></span>

<span data-ttu-id="61df2-295">中间件组件可通过构造函数参数从[依赖关系注入 (DI)](xref:fundamentals/dependency-injection) 解析其依赖项。</span><span class="sxs-lookup"><span data-stu-id="61df2-295">Middleware components can resolve their dependencies from [dependency injection (DI)](xref:fundamentals/dependency-injection) through constructor parameters.</span></span> <span data-ttu-id="61df2-296">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) 也可直接接受其他参数。</span><span class="sxs-lookup"><span data-stu-id="61df2-296">[UseMiddleware&lt;T&gt;](/dotnet/api/microsoft.aspnetcore.builder.usemiddlewareextensions.usemiddleware#Microsoft_AspNetCore_Builder_UseMiddlewareExtensions_UseMiddleware_Microsoft_AspNetCore_Builder_IApplicationBuilder_System_Type_System_Object___) can also accept additional parameters directly.</span></span>

### <a name="per-request-dependencies"></a><span data-ttu-id="61df2-297">按请求依赖项</span><span class="sxs-lookup"><span data-stu-id="61df2-297">Per-request dependencies</span></span>

<span data-ttu-id="61df2-298">由于中间件是在应用启动时构造的，而不是按请求构造的，因此在每个请求过程中，中间件构造函数使用的范围内生存期服务不与其他依赖关系注入类型共享。</span><span class="sxs-lookup"><span data-stu-id="61df2-298">Because middleware is constructed at app startup, not per-request, *scoped* lifetime services used by middleware constructors aren't shared with other dependency-injected types during each request.</span></span> <span data-ttu-id="61df2-299">如果必须在中间件和其他类型之间共享范围内服务，请将这些服务添加到 `Invoke` 方法的签名。</span><span class="sxs-lookup"><span data-stu-id="61df2-299">If you must share a *scoped* service between your middleware and other types, add these services to the `Invoke` method's signature.</span></span> <span data-ttu-id="61df2-300">`Invoke` 方法可接受由 DI 填充的其他参数：</span><span class="sxs-lookup"><span data-stu-id="61df2-300">The `Invoke` method can accept additional parameters that are populated by DI:</span></span>

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

## <a name="additional-resources"></a><span data-ttu-id="61df2-301">其他资源</span><span class="sxs-lookup"><span data-stu-id="61df2-301">Additional resources</span></span>

* <xref:migration/http-modules>
* <xref:fundamentals/startup>
* <xref:fundamentals/request-features>
* <xref:fundamentals/middleware/extensibility>
* <xref:fundamentals/middleware/extensibility-third-party-container>
