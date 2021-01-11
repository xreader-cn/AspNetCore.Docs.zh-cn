---
title: 处理 ASP.NET Core 中的错误
author: rick-anderson
description: 了解如何处理 ASP.NET Core 应用中的错误。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
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
uid: fundamentals/error-handling
ms.openlocfilehash: ad9920ccd830b93d083f3c5ede03702164842b6e
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97753109"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="bcfad-103">处理 ASP.NET Core 中的错误</span><span class="sxs-lookup"><span data-stu-id="bcfad-103">Handle errors in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-5.0"

<span data-ttu-id="bcfad-104">作者：[Kirk Larkin](https://twitter.com/serpent5)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bcfad-104">By [Kirk Larkin](https://twitter.com/serpent5), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bcfad-105">本文介绍了处理 ASP.NET Core Web 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="bcfad-106">有关 Web API，请参阅 <xref:web-api/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="bcfad-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="bcfad-108">（[下载方法](xref:index#how-to-download-a-sample)。）在测试示例应用时，F12 浏览器开发人员工具上的网络选项卡非常有用。</span><span class="sxs-lookup"><span data-stu-id="bcfad-108">([How to download](xref:index#how-to-download-a-sample).) The network tab on the F12 browser developer tools is useful when testing the sample app.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="bcfad-109">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="bcfad-109">Developer Exception Page</span></span>

<span data-ttu-id="bcfad-110">开发人员异常页显示请求异常的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="bcfad-111">ASP.NET Core 模板会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="bcfad-111">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=3-6)]

<span data-ttu-id="bcfad-112">当应用在[开发环境](xref:fundamentals/environments)中运行时，前面突出显示的代码启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-112">The preceding highlighted code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bcfad-113">模板在中间件管道的前面部分放置 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A>，以便它可以捕获在后面的中间件中引发的异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-113">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> early in the middleware pipeline so that it can catch exceptions thrown in middleware that follows.</span></span>

<span data-ttu-id="bcfad-114">仅\*_当应用在开发环境中运行时，前面的代码才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-114">The preceding code enables the Developer Exception Page \***only** _ when the app runs in the Development environment.</span></span> <span data-ttu-id="bcfad-115">当应用在生产环境中运行时，不应公开显示详细的异常信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-115">Detailed exception information should not be displayed publicly when the app runs in the Production environment.</span></span> <span data-ttu-id="bcfad-116">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bcfad-117">开发人员异常页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="bcfad-117">The Developer Exception Page includes the following information about the exception and the request:</span></span>

<span data-ttu-id="bcfad-118">_ 堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="bcfad-118">_ Stack trace</span></span>
* <span data-ttu-id="bcfad-119">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="bcfad-119">Query string parameters if any</span></span>
* <span data-ttu-id="bcfad-120">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="bcfad-120">Cookies if any</span></span>
* <span data-ttu-id="bcfad-121">标头</span><span class="sxs-lookup"><span data-stu-id="bcfad-121">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="bcfad-122">异常处理程序页</span><span class="sxs-lookup"><span data-stu-id="bcfad-122">Exception handler page</span></span>

<span data-ttu-id="bcfad-123">若要为[生产环境](xref:fundamentals/environments)配置自定义错误处理页，请调用 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-123">To configure a custom error handling page for the [Production environment](xref:fundamentals/environments), call <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bcfad-124">此异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="bcfad-124">This exception handling middleware:</span></span>

* <span data-ttu-id="bcfad-125">捕获并记录异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="bcfad-126">使用指示的路径在备用管道中重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="bcfad-126">Re-executes the request in an alternate pipeline using the path indicated.</span></span> <span data-ttu-id="bcfad-127">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="bcfad-127">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="bcfad-128">模板生成的代码使用 `/Error` 路径重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="bcfad-128">The template generated code re-executes the request using the `/Error` path.</span></span>

<span data-ttu-id="bcfad-129">在下面的示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非开发环境中添加异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="bcfad-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the exception handling middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="bcfad-130">Razor Pages 应用模板在 Pages 文件夹中提供了一个“错误”页面 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`) 。</span><span class="sxs-lookup"><span data-stu-id="bcfad-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="bcfad-131">对于 MVC 应用，项目模板包括 `Error` 操作方法和主控制器的错误视图。</span><span class="sxs-lookup"><span data-stu-id="bcfad-131">For an MVC app, the project template includes an `Error` action method and an Error view for the Home controller.</span></span>

<span data-ttu-id="bcfad-132">不要使用 HTTP 方法属性（如 `HttpGet`）标记错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-132">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="bcfad-133">显式谓词可阻止某些请求访问操作方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-133">Explicit verbs prevent some requests from reaching the action method.</span></span> <span data-ttu-id="bcfad-134">如果未经身份验证的用户应看到错误视图，则允许匿名访问该方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-134">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="bcfad-135">访问异常</span><span class="sxs-lookup"><span data-stu-id="bcfad-135">Access the exception</span></span>

<span data-ttu-id="bcfad-136">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 访问错误处理程序中的异常和原始请求路径。</span><span class="sxs-lookup"><span data-stu-id="bcfad-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler.</span></span> <span data-ttu-id="bcfad-137">以下代码将 `ExceptionMessage` 添加到由 ASP.NET Core 模板生成的默认 Pages/Error.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="bcfad-137">The following code adds `ExceptionMessage` to the default *Pages/Error.cshtml.cs* generated by the ASP.NET Core templates:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet)]

> [!WARNING]
> <span data-ttu-id="bcfad-138">请勿向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="bcfad-139">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="bcfad-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="bcfad-140">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试异常，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="bcfad-140">To test the exception in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bcfad-141">将环境设置为生产环境。</span><span class="sxs-lookup"><span data-stu-id="bcfad-141">Set the environment to production.</span></span>
* <span data-ttu-id="bcfad-142">从 Program.cs 中的 `webBuilder.UseStartup<Startup>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-142">Remove the comments from `webBuilder.UseStartup<Startup>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bcfad-143">在主页上选择“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="bcfad-143">Select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="bcfad-144">异常处理程序 lambda</span><span class="sxs-lookup"><span data-stu-id="bcfad-144">Exception handler lambda</span></span>

<span data-ttu-id="bcfad-145">[自定义异常处理程序页](#exception-handler-page)的替代方法是向 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="bcfad-145">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bcfad-146">使用 lambda，可以在返回响应前访问错误。</span><span class="sxs-lookup"><span data-stu-id="bcfad-146">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="bcfad-147">以下代码使用 lambda 处理异常：</span><span class="sxs-lookup"><span data-stu-id="bcfad-147">The following code uses a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupLambda.cs?name=snippet)]

<!-- 
In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message. For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).
-->

> [!WARNING]
> <span data-ttu-id="bcfad-148">不要向客户端提供来自 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 的敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-148">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="bcfad-149">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="bcfad-149">Serving errors is a security risk.</span></span>

<span data-ttu-id="bcfad-150">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试异常处理 lambda：</span><span class="sxs-lookup"><span data-stu-id="bcfad-150">To test the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bcfad-151">将环境设置为生产环境。</span><span class="sxs-lookup"><span data-stu-id="bcfad-151">Set the environment to production.</span></span>
* <span data-ttu-id="bcfad-152">从 Program.cs 中的 `webBuilder.UseStartup<StartupLambda>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-152">Remove the comments from `webBuilder.UseStartup<StartupLambda>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bcfad-153">在主页上选择“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="bcfad-153">Select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="bcfad-154">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bcfad-154">UseStatusCodePages</span></span>

<span data-ttu-id="bcfad-155">默认情况下，ASP.NET Core 应用不会为 HTTP 错误状态代码（如“404 - 未找到”）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-155">By default, an ASP.NET Core app doesn't provide a status code page for HTTP error status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="bcfad-156">当应用遇到没有正文的 HTTP 400-599 错误状态代码时，它将返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="bcfad-156">When the app encounters an HTTP 400-599 error status code that doesn't have a body, it returns the status code and an empty response body.</span></span> <span data-ttu-id="bcfad-157">若要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="bcfad-157">To provide status code pages, use the status code pages middleware.</span></span> <span data-ttu-id="bcfad-158">若要启用常见错误状态代码的默认纯文本处理程序，请在 `Startup.Configure` 方法中调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="bcfad-158">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupUseStatusCodePages.cs?name=snippet&highlight=13)]

<span data-ttu-id="bcfad-159">在请求处理中间件之前调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-159">Call `UseStatusCodePages` before request handling middleware.</span></span> <span data-ttu-id="bcfad-160">例如，在静态文件中间件和端点中间件之前调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-160">For example, call `UseStatusCodePages` before the Static File Middleware and the Endpoints Middleware.</span></span>

<span data-ttu-id="bcfad-161">未使用 `UseStatusCodePages` 时，导航到没有终结点的 URL 会返回一条与浏览器相关的错误消息，指示找不到终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-161">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="bcfad-162">例如，导航到 `Home/Privacy2`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-162">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="bcfad-163">调用 `UseStatusCodePages` 时，浏览器返回：</span><span class="sxs-lookup"><span data-stu-id="bcfad-163">When `UseStatusCodePages` is called, the browser returns:</span></span>

```html
Status Code: 404; Not Found
```

<span data-ttu-id="bcfad-164">`UseStatusCodePages` 通常不在生产中使用，因为它返回对用户没有用的消息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-164">`UseStatusCodePages` isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bcfad-165">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`：</span><span class="sxs-lookup"><span data-stu-id="bcfad-165">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x):</span></span>

* <span data-ttu-id="bcfad-166">将环境设置为生产环境。</span><span class="sxs-lookup"><span data-stu-id="bcfad-166">Set the environment to production.</span></span>
* <span data-ttu-id="bcfad-167">从 Program.cs 中的 `webBuilder.UseStartup<StartupUseStatusCodePages>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-167">Remove the comments from `webBuilder.UseStartup<StartupUseStatusCodePages>();` in *Program.cs*.</span></span>
* <span data-ttu-id="bcfad-168">选择主页上的链接。</span><span class="sxs-lookup"><span data-stu-id="bcfad-168">Select the links on the home page on the home page.</span></span>

> [!NOTE]
> <span data-ttu-id="bcfad-169">状态代码页中间件不捕获异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-169">The status code pages middleware does **not** catch exceptions.</span></span> <span data-ttu-id="bcfad-170">若要提供自定义错误处理页，请使用[异常处理程序页](#exception-handler-page)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-170">To provide a custom error handling page, use the [exception handler page](#exception-handler-page).</span></span>

### <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="bcfad-171">包含格式字符串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bcfad-171">UseStatusCodePages with format string</span></span>

<span data-ttu-id="bcfad-172">若要自定义响应内容类型和文本，请利用需要使用内容类型和格式字符串的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="bcfad-172">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupFormat.cs?name=snippet&highlight=13-14)]

<span data-ttu-id="bcfad-173">在前面的代码中，`{0}` 是错误代码的占位符。</span><span class="sxs-lookup"><span data-stu-id="bcfad-173">In the preceding code, `{0}` is a placeholder for the error code.</span></span>

<span data-ttu-id="bcfad-174">具有格式字符串的 `UseStatusCodePages` 通常不在生产环境中使用，因为它返回对用户没有用的消息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-174">`UseStatusCodePages` with a format string isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bcfad-175">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupFormat>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-175">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupFormat>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="bcfad-176">包含 lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bcfad-176">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="bcfad-177">若要指定自定义错误处理和响应写入代码，请利用需要使用 lambda 表达式的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="bcfad-177">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupStatusLambda.cs?name=snippet&highlight=13-20)]

<span data-ttu-id="bcfad-178">使用 lambda 的 `UseStatusCodePages` 通常不在生产中使用，因为它返回对用户没有用的消息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-178">`UseStatusCodePages` with a lambda isn't typically used in production because it returns a message that isn't useful to users.</span></span>

<span data-ttu-id="bcfad-179">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupStatusLambda>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-179">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupStatusLambda>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="bcfad-180">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="bcfad-180">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="bcfad-181"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="bcfad-181">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="bcfad-182">向客户端发送“302 - 已找到”状态代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-182">Sends a [302 - Found](https://developer.mozilla.org/docs/Web/HTTP/Status/302) status code to the client.</span></span>
* <span data-ttu-id="bcfad-183">将客户端重定向到 URL 模板中提供的错误处理终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-183">Redirects the client to the error handling endpoint provided in the URL template.</span></span> <span data-ttu-id="bcfad-184">错误处理终结点通常会显示错误信息并返回 HTTP 200。</span><span class="sxs-lookup"><span data-stu-id="bcfad-184">The error handling endpoint typically displays error information and returns HTTP 200.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCredirect.cs?name=snippet&highlight=13)]

<span data-ttu-id="bcfad-185">URL 模板可能会包括状态代码的 `{0}` 占位符，如前面的代码中所示。</span><span class="sxs-lookup"><span data-stu-id="bcfad-185">The URL template can include a `{0}` placeholder for the status code, as shown in the preceding code.</span></span> <span data-ttu-id="bcfad-186">如果 URL 模板以波形符 `~`（代字号）开头，则 `~` 会替换为应用的 `PathBase`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-186">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="bcfad-187">在应用中指定终结点时，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bcfad-187">When specifying an endpoint in the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bcfad-188">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的 [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-188">For a Razor Pages example, see [Pages/MyStatusCode.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bcfad-189">使用此方法通常是当应用：</span><span class="sxs-lookup"><span data-stu-id="bcfad-189">This method is commonly used when the app:</span></span>

* <span data-ttu-id="bcfad-190">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="bcfad-190">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="bcfad-191">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-191">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="bcfad-192">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-192">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="bcfad-193">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupSCredirect>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-193">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCredirect>();` in *Program.cs*.</span></span>

### <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="bcfad-194">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="bcfad-194">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="bcfad-195"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="bcfad-195">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="bcfad-196">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-196">Returns the original status code to the client.</span></span>
* <span data-ttu-id="bcfad-197">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="bcfad-197">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/StartupSCreX.cs?name=snippet&highlight=13)]

<span data-ttu-id="bcfad-198">如果在应用中指定终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bcfad-198">If an endpoint within the app is specified, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bcfad-199">确保将 `UseStatusCodePagesWithReExecute` 放置在 `UseRouting` 之前，以便可以将请求重新路由到状态页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-199">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="bcfad-200">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-200">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bcfad-201">使用此方法通常是当应用应：</span><span class="sxs-lookup"><span data-stu-id="bcfad-201">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="bcfad-202">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-202">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="bcfad-203">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-203">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="bcfad-204">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-204">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="bcfad-205">URL 模板和查询字符串模板可能包括状态代码的占位符 `{0}`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-205">The URL and query string templates may include a placeholder `{0}` for the status code.</span></span> <span data-ttu-id="bcfad-206">URL 模板必须以 `/` 开头。</span><span class="sxs-lookup"><span data-stu-id="bcfad-206">The URL template must start with `/`.</span></span>

<!-- Review: removing this. The sample code doesn't use @page "{code?}"
If you want that, it should be @page "{code:int?}"
but that's not required. Original text follows:

When using a placeholder in the path, confirm that the endpoint can process the path segment. For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:

```cshtml
@page "{code?}"
```
-->

<span data-ttu-id="bcfad-207">错误处理终结点可以获取生成错误的原始 URL，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="bcfad-207">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/MyStatusCode2.cshtml.cs?name=snippet)]

<span data-ttu-id="bcfad-208">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中的 [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-208">For a Razor Pages example, see [Pages/MyStatusCode2.cshtml](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x/ErrorHandlingSample/Pages) in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x).</span></span>

<span data-ttu-id="bcfad-209">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x)中测试 `UseStatusCodePages`，请从 Program.cs 中的 `webBuilder.UseStartup<StartupSCreX>();` 删除注释。</span><span class="sxs-lookup"><span data-stu-id="bcfad-209">To test `UseStatusCodePages` in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/5.x), remove the comments from `webBuilder.UseStartup<StartupSCreX>();` in *Program.cs*.</span></span>

## <a name="disable-status-code-pages"></a><span data-ttu-id="bcfad-210">禁用状态代码页</span><span class="sxs-lookup"><span data-stu-id="bcfad-210">Disable status code pages</span></span>

<span data-ttu-id="bcfad-211">若要禁用 MVC 控制器或操作方法的状态代码页，请使用 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="bcfad-211">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="bcfad-212">若要禁用 Razor Pages 处理程序方法或 MVC 控制器中的特定请求的状态代码页，请使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>：</span><span class="sxs-lookup"><span data-stu-id="bcfad-212">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Pages/Privacy.cshtml.cs?name=snippet)]

## <a name="exception-handling-code"></a><span data-ttu-id="bcfad-213">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="bcfad-213">Exception-handling code</span></span>

<span data-ttu-id="bcfad-214">异常处理页中的代码可能也会引发异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-214">Code in exception handling pages can also throw exceptions.</span></span> <span data-ttu-id="bcfad-215">应彻底测试生产错误页面，并格外小心，避免引发其自己的异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-215">Production error pages should be tested thoroughly and take extra care to avoid throwing exceptions of their own.</span></span>
<!-- Review: original, which is not realistic 
 > It's often a good idea for production error pages to consist of purely static content.

 comments: - after you catch the exception, you need code to log the details and perhaps dynamically create a string with an error message. 
-->

### <a name="response-headers"></a><span data-ttu-id="bcfad-216">响应头</span><span class="sxs-lookup"><span data-stu-id="bcfad-216">Response headers</span></span>

<span data-ttu-id="bcfad-217">在响应头发送后：</span><span class="sxs-lookup"><span data-stu-id="bcfad-217">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="bcfad-218">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-218">The app can't change the response's status code.</span></span>
* <span data-ttu-id="bcfad-219">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="bcfad-219">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="bcfad-220">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="bcfad-220">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="bcfad-221">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="bcfad-221">Server exception handling</span></span>

<span data-ttu-id="bcfad-222">除了应用中的异常处理逻辑外，[HTTP 服务器实现](xref:fundamentals/servers/index)还能处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-222">In addition to the exception handling logic in an app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="bcfad-223">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的 `500 - Internal Server Error` 响应。</span><span class="sxs-lookup"><span data-stu-id="bcfad-223">If the server catches an exception before response headers are sent, the server sends a `500 - Internal Server Error` response without a response body.</span></span> <span data-ttu-id="bcfad-224">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="bcfad-224">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="bcfad-225">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-225">Requests that aren't handled by the app are handled by the server.</span></span> <span data-ttu-id="bcfad-226">当服务器处理请求时，发生的任何异常都将由服务器的异常处理进行处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-226">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="bcfad-227">应用的自定义错误页面、异常处理中间件和筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="bcfad-227">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="bcfad-228">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="bcfad-228">Startup exception handling</span></span>

<span data-ttu-id="bcfad-229">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-229">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="bcfad-230">可以将主机配置为，[捕获启动错误](xref:fundamentals/host/web-host#capture-startup-errors)和[捕获详细错误](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-230">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="bcfad-231">仅当错误在主机地址/端口绑定后出现时，托管层才能显示捕获的启动错误的错误页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-231">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="bcfad-232">如果绑定失败：</span><span class="sxs-lookup"><span data-stu-id="bcfad-232">If binding fails:</span></span>

* <span data-ttu-id="bcfad-233">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-233">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="bcfad-234">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="bcfad-234">The dotnet process crashes.</span></span>
* <span data-ttu-id="bcfad-235">不会在 HTTP 服务器为 [Kestrel](xref:fundamentals/servers/kestrel) 时显示任何错误页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-235">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="bcfad-236">在 [IIS](/iis)（或 Azure 应用服务）或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。</span><span class="sxs-lookup"><span data-stu-id="bcfad-236">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="bcfad-237">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-237">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="bcfad-238">数据库错误页</span><span class="sxs-lookup"><span data-stu-id="bcfad-238">Database error page</span></span>

<span data-ttu-id="bcfad-239">数据库开发人员页面异常筛选器 `AddDatabaseDeveloperPageExceptionFilter` 捕获可以使用 Entity Framework Core 迁移解决的与数据库相关的异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-239">The Database developer page exception filter `AddDatabaseDeveloperPageExceptionFilter` captures database-related exceptions that can be resolved by using Entity Framework Core migrations.</span></span> <span data-ttu-id="bcfad-240">当这些异常出现时，便会生成 HTML 响应，其中包含用于解决问题的可能操作的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-240">When these exceptions occur, an HTML response is generated with details of possible actions to resolve the issue.</span></span> <span data-ttu-id="bcfad-241">仅在开发环境中启用此页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-241">This page is enabled only in the Development environment.</span></span> <span data-ttu-id="bcfad-242">在指定个人用户帐户时，ASP.NET Core Razor Pages 模板生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="bcfad-242">The following code was generated by the ASP.NET Core Razor Pages templates when individual user accounts were specified:</span></span>

[!code-csharp[](error-handling/samples/5.x/StartupDBexFilter.cs?name=snippet&highlight=6)]

## <a name="exception-filters"></a><span data-ttu-id="bcfad-243">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="bcfad-243">Exception filters</span></span>

<span data-ttu-id="bcfad-244">在 MVC 应用中，可以全局配置异常筛选器，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="bcfad-244">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="bcfad-245">在 Razor Pages 应用中，可以全局配置异常筛选器，也可以为每个页面模型单独配置。</span><span class="sxs-lookup"><span data-stu-id="bcfad-245">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="bcfad-246">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-246">These filters handle any unhandled exceptions that occur during the execution of a controller action or another filter.</span></span> <span data-ttu-id="bcfad-247">有关详细信息，请参阅 <xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-247">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

<span data-ttu-id="bcfad-248">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如内置[异常处理中间件](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs) `UseExceptionHandler` 灵活。</span><span class="sxs-lookup"><span data-stu-id="bcfad-248">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the built-in [exception handling middleware](https://github.com/dotnet/aspnetcore/blob/master/src/Middleware/Diagnostics/src/ExceptionHandler/ExceptionHandlerMiddleware.cs), `UseExceptionHandler`.</span></span> <span data-ttu-id="bcfad-249">我们建议使用 `UseExceptionHandler`，除非你需要根据选择的 MVC 操作以不同的方式执行错误处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-249">We recommend using `UseExceptionHandler`, unless you need to perform error handling differently based on which MVC action is chosen.</span></span>

[!code-csharp[](error-handling/samples/5.x/ErrorHandlingSample/Startup.cs?name=snippet&highlight=9)]

## <a name="model-state-errors"></a><span data-ttu-id="bcfad-250">模型状态错误</span><span class="sxs-lookup"><span data-stu-id="bcfad-250">Model state errors</span></span>

<span data-ttu-id="bcfad-251">若要了解如何处理模型状态错误，请参阅[模型绑定](xref:mvc/models/model-binding)和[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-251">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bcfad-252">其他资源</span><span class="sxs-lookup"><span data-stu-id="bcfad-252">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

<span data-ttu-id="bcfad-253">作者：[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bcfad-253">By  [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bcfad-254">本文介绍了处理 ASP.NET Core Web 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-254">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="bcfad-255">有关 Web API，请参阅 <xref:web-api/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-255">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="bcfad-256">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-256">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="bcfad-257">（[下载方法](xref:index#how-to-download-a-sample)。）</span><span class="sxs-lookup"><span data-stu-id="bcfad-257">([How to download](xref:index#how-to-download-a-sample).)</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="bcfad-258">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="bcfad-258">Developer Exception Page</span></span>

<span data-ttu-id="bcfad-259">开发人员异常页显示请求异常的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-259">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="bcfad-260">ASP.NET Core 模板会生成以下代码：</span><span class="sxs-lookup"><span data-stu-id="bcfad-260">The ASP.NET Core templates generate the following code:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="bcfad-261">当应用在[开发环境](xref:fundamentals/environments)中运行时，前面的代码启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-261">The preceding code enables the developer exception page when the app is running in the [Development environment](xref:fundamentals/environments).</span></span>

<span data-ttu-id="bcfad-262">模板将 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 放在任何中间件之前，以便捕获后面的中间件中的异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-262">The templates place <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware so exceptions are caught in the middleware that follows.</span></span>

<span data-ttu-id="bcfad-263">仅当应用程序在开发环境中运行时，前面的代码才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-263">The preceding code enables the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="bcfad-264">当应用在生产环境中运行时，不应公开显示详细的异常信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-264">Detailed exception information should not be displayed publicly when the app runs in production.</span></span> <span data-ttu-id="bcfad-265">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-265">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="bcfad-266">开发人员异常页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="bcfad-266">The Developer Exception Page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="bcfad-267">堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="bcfad-267">Stack trace</span></span>
* <span data-ttu-id="bcfad-268">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="bcfad-268">Query string parameters if any</span></span>
* <span data-ttu-id="bcfad-269">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="bcfad-269">Cookies if any</span></span>
* <span data-ttu-id="bcfad-270">标头</span><span class="sxs-lookup"><span data-stu-id="bcfad-270">Headers</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="bcfad-271">异常处理程序页</span><span class="sxs-lookup"><span data-stu-id="bcfad-271">Exception handler page</span></span>

<span data-ttu-id="bcfad-272">若要为生产环境配置自定义错误处理页，请使用异常处理中间件。</span><span class="sxs-lookup"><span data-stu-id="bcfad-272">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="bcfad-273">中间件：</span><span class="sxs-lookup"><span data-stu-id="bcfad-273">The middleware:</span></span>

* <span data-ttu-id="bcfad-274">捕获并记录异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-274">Catches and logs exceptions.</span></span>
* <span data-ttu-id="bcfad-275">在备用管道中为指定的页或控制器重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="bcfad-275">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="bcfad-276">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="bcfad-276">The request isn't re-executed if the response has started.</span></span> <span data-ttu-id="bcfad-277">模板生成的代码将请求重新执行到 `/Error`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-277">The template generated code re-executes the request to `/Error`.</span></span>

<span data-ttu-id="bcfad-278">在下面的示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非开发环境中添加异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="bcfad-278">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="bcfad-279">Razor Pages 应用模板在 Pages 文件夹中提供了一个“错误”页面 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`) 。</span><span class="sxs-lookup"><span data-stu-id="bcfad-279">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="bcfad-280">对于 MVC 应用，项目模板包括错误操作方法和主控制器的错误视图。</span><span class="sxs-lookup"><span data-stu-id="bcfad-280">For an MVC app, the project template includes an Error action method and an Error view in the Home controller.</span></span>

<span data-ttu-id="bcfad-281">不要使用 HTTP 方法属性（如 `HttpGet`）标记错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-281">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="bcfad-282">显式谓词可阻止某些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-282">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="bcfad-283">如果未经身份验证的用户应看到错误视图，则允许匿名访问该方法。</span><span class="sxs-lookup"><span data-stu-id="bcfad-283">Allow anonymous access to the method if unauthenticated users should see the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="bcfad-284">访问异常</span><span class="sxs-lookup"><span data-stu-id="bcfad-284">Access the exception</span></span>

<span data-ttu-id="bcfad-285">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 访问错误处理程序控制器或页中的异常和原始请求路径：</span><span class="sxs-lookup"><span data-stu-id="bcfad-285">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/MyFolder/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="bcfad-286">请勿向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-286">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="bcfad-287">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="bcfad-287">Serving errors is a security risk.</span></span>

<span data-ttu-id="bcfad-288">若要触发前面的异常处理页，请将环境设置为生产环境并强制引发异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-288">To trigger the preceding exception handling page, set the environment to productions and force an exception.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="bcfad-289">异常处理程序 lambda</span><span class="sxs-lookup"><span data-stu-id="bcfad-289">Exception handler lambda</span></span>

<span data-ttu-id="bcfad-290">[自定义异常处理程序页](#exception-handler-page)的替代方法是向 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="bcfad-290">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="bcfad-291">使用 lambda，可以在返回响应前访问错误。</span><span class="sxs-lookup"><span data-stu-id="bcfad-291">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="bcfad-292">下面的示例展示了如何使用 lambda 进行异常处理：</span><span class="sxs-lookup"><span data-stu-id="bcfad-292">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="bcfad-293">在前面的代码中，添加了 `await context.Response.WriteAsync(new string(' ', 512));`，以便 Internet Explorer 浏览器显示相应的错误消息，而非显示 IE 错误消息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-293">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="bcfad-294">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16144)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-294">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="bcfad-295">不要向客户端提供来自 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 的敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-295">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="bcfad-296">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="bcfad-296">Serving errors is a security risk.</span></span>

<span data-ttu-id="bcfad-297">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看异常处理 lambda 的结果，请使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 预处理器指令，并选择主页上的“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="bcfad-297">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="bcfad-298">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bcfad-298">UseStatusCodePages</span></span>

<span data-ttu-id="bcfad-299">默认情况下，ASP.NET Core 应用不会为 HTTP 状态代码（如“404 - 未找到”）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-299">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="bcfad-300">应用返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="bcfad-300">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="bcfad-301">若要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="bcfad-301">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="bcfad-302">此中间件是通过 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="bcfad-302">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package.</span></span>

<span data-ttu-id="bcfad-303">若要启用常见错误状态代码的默认纯文本处理程序，请在 `Startup.Configure` 方法中调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="bcfad-303">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="bcfad-304">在请求处理中间件（例如，静态文件中间件和 MVC 中间件）前面调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-304">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="bcfad-305">未使用 `UseStatusCodePages` 时，导航到没有终结点的 URL 会返回一条与浏览器相关的错误消息，指示找不到终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-305">When `UseStatusCodePages` isn't used, navigating to a URL without an endpoint returns a browser dependent error message indicating the endpoint can't be found.</span></span> <span data-ttu-id="bcfad-306">例如，导航到 `Home/Privacy2`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-306">For example, navigating to `Home/Privacy2`.</span></span> <span data-ttu-id="bcfad-307">调用 `UseStatusCodePages` 时，浏览器返回：</span><span class="sxs-lookup"><span data-stu-id="bcfad-307">When `UseStatusCodePages` is called, the browser returns:</span></span>

```
Status Code: 404; Not Found
```

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="bcfad-308">包含格式字符串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bcfad-308">UseStatusCodePages with format string</span></span>

<span data-ttu-id="bcfad-309">若要自定义响应内容类型和文本，请利用需要使用内容类型和格式字符串的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="bcfad-309">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="bcfad-310">包含 lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="bcfad-310">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="bcfad-311">若要指定自定义错误处理和响应写入代码，请利用需要使用 lambda 表达式的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="bcfad-311">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="bcfad-312">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="bcfad-312">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="bcfad-313"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="bcfad-313">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="bcfad-314">向客户端发送“302 - 已找到”状态代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-314">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="bcfad-315">将客户端重定向到 URL 模板中的位置。</span><span class="sxs-lookup"><span data-stu-id="bcfad-315">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="bcfad-316">URL 模板可能会包括状态代码的 `{0}` 占位符，如上面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="bcfad-316">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="bcfad-317">如果 URL 模板以波形符 `~`（代字号）开头，则 `~` 会替换为应用的 `PathBase`。</span><span class="sxs-lookup"><span data-stu-id="bcfad-317">If the URL template starts with `~` (tilde), the `~` is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="bcfad-318">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bcfad-318">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bcfad-319">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml。</span><span class="sxs-lookup"><span data-stu-id="bcfad-319">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="bcfad-320">使用此方法通常是当应用：</span><span class="sxs-lookup"><span data-stu-id="bcfad-320">This method is commonly used when the app:</span></span>

* <span data-ttu-id="bcfad-321">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="bcfad-321">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="bcfad-322">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-322">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="bcfad-323">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-323">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="bcfad-324">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="bcfad-324">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="bcfad-325"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="bcfad-325">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="bcfad-326">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-326">Returns the original status code to the client.</span></span>
* <span data-ttu-id="bcfad-327">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="bcfad-327">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="bcfad-328">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="bcfad-328">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="bcfad-329">确保将 `UseStatusCodePagesWithReExecute` 放置在 `UseRouting` 之前，以便可以将请求重新路由到状态页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-329">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="bcfad-330">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml。</span><span class="sxs-lookup"><span data-stu-id="bcfad-330">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="bcfad-331">使用此方法通常是当应用应：</span><span class="sxs-lookup"><span data-stu-id="bcfad-331">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="bcfad-332">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-332">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="bcfad-333">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="bcfad-333">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="bcfad-334">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-334">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="bcfad-335">URL 模板和查询字符串模板可能包括状态代码的占位符 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-335">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="bcfad-336">URL 模板必须以斜杠 (`/`) 开头。</span><span class="sxs-lookup"><span data-stu-id="bcfad-336">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="bcfad-337">若要在路径中使用占位符，请确认终结点（页或控制器）能否处理路径段。</span><span class="sxs-lookup"><span data-stu-id="bcfad-337">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="bcfad-338">例如，错误的 Razor 页面应通过 `@page` 指令接受可选路径段值：</span><span class="sxs-lookup"><span data-stu-id="bcfad-338">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="bcfad-339">错误处理终结点可以获取生成错误的原始 URL，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="bcfad-339">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="bcfad-340">禁用状态代码页</span><span class="sxs-lookup"><span data-stu-id="bcfad-340">Disable status code pages</span></span>

<span data-ttu-id="bcfad-341">若要禁用 MVC 控制器或操作方法的状态代码页，请使用 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="bcfad-341">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="bcfad-342">若要禁用 Razor Pages 处理程序方法或 MVC 控制器中的特定请求的状态代码页，请使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>：</span><span class="sxs-lookup"><span data-stu-id="bcfad-342">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="bcfad-343">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="bcfad-343">Exception-handling code</span></span>

<span data-ttu-id="bcfad-344">异常处理页中的代码可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-344">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="bcfad-345">建议在生产错误页面中包含纯静态内容。</span><span class="sxs-lookup"><span data-stu-id="bcfad-345">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="bcfad-346">响应头</span><span class="sxs-lookup"><span data-stu-id="bcfad-346">Response headers</span></span>

<span data-ttu-id="bcfad-347">在响应头发送后：</span><span class="sxs-lookup"><span data-stu-id="bcfad-347">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="bcfad-348">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="bcfad-348">The app can't change the response's status code.</span></span>
* <span data-ttu-id="bcfad-349">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="bcfad-349">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="bcfad-350">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="bcfad-350">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="bcfad-351">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="bcfad-351">Server exception handling</span></span>

<span data-ttu-id="bcfad-352">除了应用中的异常处理逻辑外，[HTTP 服务器实现](xref:fundamentals/servers/index)还能处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-352">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="bcfad-353">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的“500 - 内部服务器错误”响应。</span><span class="sxs-lookup"><span data-stu-id="bcfad-353">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="bcfad-354">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="bcfad-354">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="bcfad-355">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-355">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="bcfad-356">当服务器处理请求时，发生的任何异常都将由服务器的异常处理进行处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-356">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="bcfad-357">应用的自定义错误页面、异常处理中间件和筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="bcfad-357">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="bcfad-358">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="bcfad-358">Startup exception handling</span></span>

<span data-ttu-id="bcfad-359">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="bcfad-359">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="bcfad-360">可以将主机配置为，[捕获启动错误](xref:fundamentals/host/web-host#capture-startup-errors)和[捕获详细错误](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-360">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="bcfad-361">仅当错误在主机地址/端口绑定后出现时，托管层才能显示捕获的启动错误的错误页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-361">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="bcfad-362">如果绑定失败：</span><span class="sxs-lookup"><span data-stu-id="bcfad-362">If binding fails:</span></span>

* <span data-ttu-id="bcfad-363">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-363">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="bcfad-364">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="bcfad-364">The dotnet process crashes.</span></span>
* <span data-ttu-id="bcfad-365">不会在 HTTP 服务器为 [Kestrel](xref:fundamentals/servers/kestrel) 时显示任何错误页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-365">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="bcfad-366">在 [IIS](/iis)（或 Azure 应用服务）或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。</span><span class="sxs-lookup"><span data-stu-id="bcfad-366">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="bcfad-367">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-367">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="bcfad-368">数据库错误页</span><span class="sxs-lookup"><span data-stu-id="bcfad-368">Database error page</span></span>

<span data-ttu-id="bcfad-369">数据库错误页中间件捕获与数据库相关的异常，可使用实体框架迁移来解析这些异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-369">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="bcfad-370">当这些异常出现时，便会生成 HTML 响应，其中包含用于解决问题的可能操作的详细信息。</span><span class="sxs-lookup"><span data-stu-id="bcfad-370">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="bcfad-371">应仅在开发环境中启用此页。</span><span class="sxs-lookup"><span data-stu-id="bcfad-371">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="bcfad-372">通过向 `Startup.Configure` 添加代码来启用此页：</span><span class="sxs-lookup"><span data-stu-id="bcfad-372">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="bcfad-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> 需要 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="bcfad-373"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="bcfad-374">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="bcfad-374">Exception filters</span></span>

<span data-ttu-id="bcfad-375">在 MVC 应用中，可以全局配置异常筛选器，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="bcfad-375">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="bcfad-376">在 Razor Pages 应用中，可以全局配置异常筛选器，也可以为每个页面模型单独配置。</span><span class="sxs-lookup"><span data-stu-id="bcfad-376">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="bcfad-377">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="bcfad-377">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="bcfad-378">有关详细信息，请参阅 <xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="bcfad-378">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="bcfad-379">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如异常处理中间件灵活。</span><span class="sxs-lookup"><span data-stu-id="bcfad-379">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="bcfad-380">建议使用中间件。</span><span class="sxs-lookup"><span data-stu-id="bcfad-380">We recommend using the middleware.</span></span> <span data-ttu-id="bcfad-381">仅在需要根据选定 MVC 操作以不同方式执行错误处理时，才使用筛选器。</span><span class="sxs-lookup"><span data-stu-id="bcfad-381">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="bcfad-382">模型状态错误</span><span class="sxs-lookup"><span data-stu-id="bcfad-382">Model state errors</span></span>

<span data-ttu-id="bcfad-383">若要了解如何处理模型状态错误，请参阅[模型绑定](xref:mvc/models/model-binding)和[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="bcfad-383">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="bcfad-384">其他资源</span><span class="sxs-lookup"><span data-stu-id="bcfad-384">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>

::: moniker-end
