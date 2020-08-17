---
title: 处理 ASP.NET Core 中的错误
author: rick-anderson
description: 了解如何处理 ASP.NET Core 应用中的错误。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 12/05/2019
no-loc:
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
ms.openlocfilehash: 2e6aabda449a24496916c6ea9fcbd38062b54c04
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88017449"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="2a7fe-103">处理 ASP.NET Core 中的错误</span><span class="sxs-lookup"><span data-stu-id="2a7fe-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="2a7fe-104">作者：[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2a7fe-104">By [Tom Dykstra](https://github.com/tdykstra/) and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2a7fe-105">本文介绍了处理 ASP.NET Core Web 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-105">This article covers common approaches to handling errors in ASP.NET Core web apps.</span></span> <span data-ttu-id="2a7fe-106">有关 Web API，请参阅 <xref:web-api/handle-errors>。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-106">See <xref:web-api/handle-errors> for web APIs.</span></span>

<span data-ttu-id="2a7fe-107">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-107">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="2a7fe-108">（[下载方法](xref:index#how-to-download-a-sample)。）本文介绍了如何在示例应用中设置预处理器指令（`#if`、`#endif`、`#define`）来启用不同方案。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-108">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="2a7fe-109">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="2a7fe-109">Developer Exception Page</span></span>

<span data-ttu-id="2a7fe-110">开发人员异常页显示请求异常的详细信息。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-110">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="2a7fe-111">此页是通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-111">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="2a7fe-112">向 `Startup.Configure` 方法添加代码，以当应用在开发[环境](xref:fundamentals/environments)中运行时启用此页：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-112">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="2a7fe-113">将 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> 调用置于要捕获其异常的任何中间件前面。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-113">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage%2A> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="2a7fe-114">仅当应用程序在开发环境中运行时才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-114">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="2a7fe-115">否则当应用程序在生产环境中运行时，详细的异常信息会向公众泄露</span><span class="sxs-lookup"><span data-stu-id="2a7fe-115">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="2a7fe-116">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-116">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="2a7fe-117">该页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-117">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="2a7fe-118">堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="2a7fe-118">Stack trace</span></span>
* <span data-ttu-id="2a7fe-119">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="2a7fe-119">Query string parameters (if any)</span></span>
* <span data-ttu-id="2a7fe-120">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="2a7fe-120">Cookies (if any)</span></span>
* <span data-ttu-id="2a7fe-121">标头</span><span class="sxs-lookup"><span data-stu-id="2a7fe-121">Headers</span></span>

<span data-ttu-id="2a7fe-122">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看开发人员异常页，请使用 `DevEnvironment` 预处理器指令，并选择主页上的“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-122">To see the Developer Exception Page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="2a7fe-123">异常处理程序页</span><span class="sxs-lookup"><span data-stu-id="2a7fe-123">Exception handler page</span></span>

<span data-ttu-id="2a7fe-124">若要为生产环境配置自定义错误处理页，请使用异常处理中间件。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-124">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="2a7fe-125">中间件：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-125">The middleware:</span></span>

* <span data-ttu-id="2a7fe-126">捕获并记录异常。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-126">Catches and logs exceptions.</span></span>
* <span data-ttu-id="2a7fe-127">在备用管道中为指定的页或控制器重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-127">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="2a7fe-128">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-128">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="2a7fe-129">在下面的示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 在非开发环境中添加异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-129">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="2a7fe-130">Razor Pages 应用模板在 Pages 文件夹中提供了一个“错误”页面 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`) 。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="2a7fe-131">对于 MVC 应用，项目模板包括 Error 操作方法和 Error 视图。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-131">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="2a7fe-132">操作方法如下：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-132">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="2a7fe-133">不要使用 HTTP 方法属性（如 `HttpGet`）标记错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-133">Don't mark the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="2a7fe-134">显式谓词可阻止某些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-134">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="2a7fe-135">允许匿名访问方法，以便未经身份验证的用户能够接收错误视图。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-135">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="2a7fe-136">访问异常</span><span class="sxs-lookup"><span data-stu-id="2a7fe-136">Access the exception</span></span>

<span data-ttu-id="2a7fe-137">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 访问错误处理程序控制器或页中的异常和原始请求路径：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-137">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="2a7fe-138">请勿向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-138">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="2a7fe-139">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-139">Serving errors is a security risk.</span></span>

<span data-ttu-id="2a7fe-140">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看异常处理页，请使用 `ProdEnvironment` 和 `ErrorHandlerPage` 预处理器指令，并选择主页上的“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-140">To see the exception handling page in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="2a7fe-141">异常处理程序 lambda</span><span class="sxs-lookup"><span data-stu-id="2a7fe-141">Exception handler lambda</span></span>

<span data-ttu-id="2a7fe-142">[自定义异常处理程序页](#exception-handler-page)的替代方法是向 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A> 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-142">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler%2A>.</span></span> <span data-ttu-id="2a7fe-143">使用 lambda，可以在返回响应前访问错误。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-143">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="2a7fe-144">下面的示例展示了如何使用 lambda 进行异常处理：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-144">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

<span data-ttu-id="2a7fe-145">在前面的代码中，添加了 `await context.Response.WriteAsync(new string(' ', 512));`，以便 Internet Explorer 浏览器显示相应的错误消息，而非显示 IE 错误消息。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-145">In the preceding code, `await context.Response.WriteAsync(new string(' ', 512));` is added so the Internet Explorer browser displays the error message rather than an IE error message.</span></span> <span data-ttu-id="2a7fe-146">有关详细信息，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore.Docs/issues/16144)。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-146">For more information, see [this GitHub issue](https://github.com/dotnet/AspNetCore.Docs/issues/16144).</span></span>

> [!WARNING]
> <span data-ttu-id="2a7fe-147">不要向客户端提供来自 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 的敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-147">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="2a7fe-148">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-148">Serving errors is a security risk.</span></span>

<span data-ttu-id="2a7fe-149">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看异常处理 lambda 的结果，请使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 预处理器指令，并选择主页上的“触发异常”。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-149">To see the result of the exception handling lambda in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="2a7fe-150">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="2a7fe-150">UseStatusCodePages</span></span>

<span data-ttu-id="2a7fe-151">默认情况下，ASP.NET Core 应用不会为 HTTP 状态代码（如“404 - 未找到”）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-151">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="2a7fe-152">应用返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-152">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="2a7fe-153">若要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-153">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="2a7fe-154">此中间件是通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-154">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="2a7fe-155">若要启用常见错误状态代码的默认纯文本处理程序，请在 `Startup.Configure` 方法中调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A>：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-155">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="2a7fe-156">在请求处理中间件（例如，静态文件中间件和 MVC 中间件）前面调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-156">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="2a7fe-157">下面的示例展示了默认处理程序显示的文本：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-157">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="2a7fe-158">若要在[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看各种状态代码页格式之一，请使用以 `StatusCodePages` 开头的预处理器指令之一，并选择主页上的“触发 404”。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-158">To see one of the various status code page formats in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="2a7fe-159">包含格式字符串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="2a7fe-159">UseStatusCodePages with format string</span></span>

<span data-ttu-id="2a7fe-160">若要自定义响应内容类型和文本，请利用需要使用内容类型和格式字符串的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-160">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="2a7fe-161">包含 lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="2a7fe-161">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="2a7fe-162">若要指定自定义错误处理和响应写入代码，请利用需要使用 lambda 表达式的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> 重载：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-162">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages%2A> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirects"></a><span data-ttu-id="2a7fe-163">UseStatusCodePagesWithRedirects</span><span class="sxs-lookup"><span data-stu-id="2a7fe-163">UseStatusCodePagesWithRedirects</span></span>

<span data-ttu-id="2a7fe-164"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-164">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects%2A> extension method:</span></span>

* <span data-ttu-id="2a7fe-165">向客户端发送“302 - 已找到”状态代码。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-165">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="2a7fe-166">将客户端重定向到 URL 模板中的位置。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-166">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="2a7fe-167">URL 模板可能会包括状态代码的 `{0}` 占位符，如上面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-167">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="2a7fe-168">如果 URL 模板以波形符 (~) 开头，波形符会替换为应用的 `PathBase`。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-168">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="2a7fe-169">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-169">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="2a7fe-170">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-170">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="2a7fe-171">使用此方法通常是当应用：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-171">This method is commonly used when the app:</span></span>

* <span data-ttu-id="2a7fe-172">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-172">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="2a7fe-173">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-173">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="2a7fe-174">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-174">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="2a7fe-175">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="2a7fe-175">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="2a7fe-176"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-176">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute%2A> extension method:</span></span>

* <span data-ttu-id="2a7fe-177">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-177">Returns the original status code to the client.</span></span>
* <span data-ttu-id="2a7fe-178">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-178">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="2a7fe-179">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-179">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="2a7fe-180">确保将 `UseStatusCodePagesWithReExecute` 放置在 `UseRouting` 之前，以便可以将请求重新路由到状态页。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-180">Ensure `UseStatusCodePagesWithReExecute` is placed before `UseRouting` so the request can be rerouted to the status page.</span></span> <span data-ttu-id="2a7fe-181">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-181">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="2a7fe-182">使用此方法通常是当应用应：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-182">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="2a7fe-183">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-183">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="2a7fe-184">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-184">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="2a7fe-185">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-185">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="2a7fe-186">URL 模板和查询字符串模板可能包括状态代码的占位符 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-186">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="2a7fe-187">URL 模板必须以斜杠 (`/`) 开头。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-187">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="2a7fe-188">若要在路径中使用占位符，请确认终结点（页或控制器）能否处理路径段。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-188">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="2a7fe-189">例如，错误的 Razor 页面应通过 `@page` 指令接受可选路径段值：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-189">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="2a7fe-190">错误处理终结点可以获取生成错误的原始 URL，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-190">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="2a7fe-191">禁用状态代码页</span><span class="sxs-lookup"><span data-stu-id="2a7fe-191">Disable status code pages</span></span>

<span data-ttu-id="2a7fe-192">若要禁用 MVC 控制器或操作方法的状态代码页，请使用 [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-192">To disable status code pages for an MVC controller or action method, use the [`[SkipStatusCodePages]`](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="2a7fe-193">若要禁用 Razor Pages 处理程序方法或 MVC 控制器中的特定请求的状态代码页，请使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-193">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="2a7fe-194">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="2a7fe-194">Exception-handling code</span></span>

<span data-ttu-id="2a7fe-195">异常处理页中的代码可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-195">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="2a7fe-196">建议在生产错误页面中包含纯静态内容。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-196">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="2a7fe-197">响应头</span><span class="sxs-lookup"><span data-stu-id="2a7fe-197">Response headers</span></span>

<span data-ttu-id="2a7fe-198">在响应头发送后：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-198">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="2a7fe-199">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-199">The app can't change the response's status code.</span></span>
* <span data-ttu-id="2a7fe-200">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-200">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="2a7fe-201">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-201">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="2a7fe-202">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="2a7fe-202">Server exception handling</span></span>

<span data-ttu-id="2a7fe-203">除了应用中的异常处理逻辑外，[HTTP 服务器实现](xref:fundamentals/servers/index)还能处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-203">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="2a7fe-204">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的“500 - 内部服务器错误”响应。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-204">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="2a7fe-205">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-205">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="2a7fe-206">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-206">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="2a7fe-207">当服务器处理请求时，发生的任何异常都将由服务器的异常处理进行处理。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-207">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="2a7fe-208">应用的自定义错误页面、异常处理中间件和筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-208">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="2a7fe-209">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="2a7fe-209">Startup exception handling</span></span>

<span data-ttu-id="2a7fe-210">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-210">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="2a7fe-211">可以将主机配置为，[捕获启动错误](xref:fundamentals/host/web-host#capture-startup-errors)和[捕获详细错误](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-211">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="2a7fe-212">仅当错误在主机地址/端口绑定后出现时，托管层才能显示捕获的启动错误的错误页。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-212">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="2a7fe-213">如果绑定失败：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-213">If binding fails:</span></span>

* <span data-ttu-id="2a7fe-214">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-214">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="2a7fe-215">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-215">The dotnet process crashes.</span></span>
* <span data-ttu-id="2a7fe-216">不会在 HTTP 服务器为 [Kestrel](xref:fundamentals/servers/kestrel) 时显示任何错误页。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-216">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="2a7fe-217">在 [IIS](/iis)（或 Azure 应用服务）或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-217">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="2a7fe-218">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-218">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="2a7fe-219">数据库错误页</span><span class="sxs-lookup"><span data-stu-id="2a7fe-219">Database error page</span></span>

<span data-ttu-id="2a7fe-220">数据库错误页中间件捕获与数据库相关的异常，可使用实体框架迁移来解析这些异常。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-220">Database Error Page Middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="2a7fe-221">当这些异常出现时，便会生成 HTML 响应，其中包含用于解决问题的可能操作的详细信息。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-221">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="2a7fe-222">应仅在开发环境中启用此页。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-222">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="2a7fe-223">通过向 `Startup.Configure` 添加代码来启用此页：</span><span class="sxs-lookup"><span data-stu-id="2a7fe-223">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

<span data-ttu-id="2a7fe-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> 需要 [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet 包。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-224"><xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage%2A> requires the [Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics.EntityFrameworkCore/) NuGet package.</span></span>

<!-- FUTURE UPDATE: On the next topic overhaul/release update, add API crosslink to this section for xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage* when available via the API docs. -->

## <a name="exception-filters"></a><span data-ttu-id="2a7fe-225">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="2a7fe-225">Exception filters</span></span>

<span data-ttu-id="2a7fe-226">在 MVC 应用中，可以全局配置异常筛选器，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-226">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="2a7fe-227">在 Razor Pages 应用中，可以全局配置异常筛选器，也可以为每个页面模型单独配置。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-227">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="2a7fe-228">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-228">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="2a7fe-229">有关详细信息，请参阅 <xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-229">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="2a7fe-230">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如异常处理中间件灵活。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-230">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="2a7fe-231">建议使用中间件。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-231">We recommend using the middleware.</span></span> <span data-ttu-id="2a7fe-232">仅在需要根据选定 MVC 操作以不同方式执行错误处理时，才使用筛选器。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-232">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="2a7fe-233">模型状态错误</span><span class="sxs-lookup"><span data-stu-id="2a7fe-233">Model state errors</span></span>

<span data-ttu-id="2a7fe-234">若要了解如何处理模型状态错误，请参阅[模型绑定](xref:mvc/models/model-binding)和[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="2a7fe-234">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2a7fe-235">其他资源</span><span class="sxs-lookup"><span data-stu-id="2a7fe-235">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
