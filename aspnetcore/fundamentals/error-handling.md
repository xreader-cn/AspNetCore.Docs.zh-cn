---
title: 处理 ASP.NET Core 中的错误
author: rick-anderson
description: 了解如何处理 ASP.NET Core 应用中的错误。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/10/2019
uid: fundamentals/error-handling
ms.openlocfilehash: 652a97a6b7fbe4c8cc678b86a92eea59937e809c
ms.sourcegitcommit: 8835b6777682da6fb3becf9f9121c03f89dc7614
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/22/2019
ms.locfileid: "69975585"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="806c9-103">处理 ASP.NET Core 中的错误</span><span class="sxs-lookup"><span data-stu-id="806c9-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="806c9-104">作者：[Tom Dykstra](https://github.com/tdykstra/)、[Luke Latham](https://github.com/guardrex) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="806c9-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="806c9-105">本文介绍了处理 ASP.NET Core 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="806c9-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="806c9-106">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)。</span><span class="sxs-lookup"><span data-stu-id="806c9-106">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span> <span data-ttu-id="806c9-107">（[下载方法](xref:index#how-to-download-a-sample)。）本文介绍了如何在示例应用中设置预处理器指令（`#if`、`#endif`、`#define`）来启用不同方案。</span><span class="sxs-lookup"><span data-stu-id="806c9-107">([How to download](xref:index#how-to-download-a-sample).) The article includes instructions about how to set preprocessor directives (`#if`, `#endif`, `#define`) in the sample app to enable different scenarios.</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="806c9-108">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="806c9-108">Developer Exception Page</span></span>

<span data-ttu-id="806c9-109">开发人员异常页  显示请求异常的详细信息。</span><span class="sxs-lookup"><span data-stu-id="806c9-109">The *Developer Exception Page* displays detailed information about request exceptions.</span></span> <span data-ttu-id="806c9-110">此页是通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="806c9-110">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="806c9-111">向 `Startup.Configure` 方法添加代码，以当应用在开发[环境](xref:fundamentals/environments)中运行时启用此页：</span><span class="sxs-lookup"><span data-stu-id="806c9-111">Add code to the `Startup.Configure` method to enable the page when the app is running in the Development [environment](xref:fundamentals/environments):</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=1-4)]

<span data-ttu-id="806c9-112">将 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> 调用置于要捕获其异常的任何中间件前面。</span><span class="sxs-lookup"><span data-stu-id="806c9-112">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> before any middleware that you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="806c9-113">仅当应用程序在开发环境中运行时才启用开发人员异常页  。</span><span class="sxs-lookup"><span data-stu-id="806c9-113">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="806c9-114">否则当应用程序在生产环境中运行时，详细的异常信息会向公众泄露</span><span class="sxs-lookup"><span data-stu-id="806c9-114">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="806c9-115">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="806c9-115">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="806c9-116">该页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="806c9-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="806c9-117">堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="806c9-117">Stack trace</span></span>
* <span data-ttu-id="806c9-118">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="806c9-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="806c9-119">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="806c9-119">Cookies (if any)</span></span>
* <span data-ttu-id="806c9-120">标头</span><span class="sxs-lookup"><span data-stu-id="806c9-120">Headers</span></span>

<span data-ttu-id="806c9-121">若要在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看开发人员异常页，请使用 `DevEnvironment` 预处理器指令，并选择主页上的“触发异常”  。</span><span class="sxs-lookup"><span data-stu-id="806c9-121">To see the Developer Exception Page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `DevEnvironment` preprocessor directive and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-page"></a><span data-ttu-id="806c9-122">异常处理程序页</span><span class="sxs-lookup"><span data-stu-id="806c9-122">Exception handler page</span></span>

<span data-ttu-id="806c9-123">若要为生产环境配置自定义错误处理页，请使用异常处理中间件。</span><span class="sxs-lookup"><span data-stu-id="806c9-123">To configure a custom error handling page for the Production environment, use the Exception Handling Middleware.</span></span> <span data-ttu-id="806c9-124">中间件：</span><span class="sxs-lookup"><span data-stu-id="806c9-124">The middleware:</span></span>

* <span data-ttu-id="806c9-125">捕获并记录异常。</span><span class="sxs-lookup"><span data-stu-id="806c9-125">Catches and logs exceptions.</span></span>
* <span data-ttu-id="806c9-126">在备用管道中为指定的页或控制器重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="806c9-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="806c9-127">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="806c9-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="806c9-128">在下面的示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 在非开发环境中添加异常处理中间件：</span><span class="sxs-lookup"><span data-stu-id="806c9-128">In the following example, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevPageAndHandlerPage&highlight=5-9)]

<span data-ttu-id="806c9-129">Razor Pages 应用模板提供“页面”  文件夹中的 Error 页 (.cshtml  ) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`)。</span><span class="sxs-lookup"><span data-stu-id="806c9-129">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the *Pages* folder.</span></span> <span data-ttu-id="806c9-130">对于 MVC 应用，项目模板包括 Error 操作方法和 Error 视图。</span><span class="sxs-lookup"><span data-stu-id="806c9-130">For an MVC app, the project template includes an Error action method and an Error view.</span></span> <span data-ttu-id="806c9-131">操作方法如下：</span><span class="sxs-lookup"><span data-stu-id="806c9-131">Here's the action method:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="806c9-132">不要使用 HTTP 方法属性（如 `HttpGet`）修饰错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="806c9-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="806c9-133">显式谓词可阻止某些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="806c9-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="806c9-134">允许匿名访问方法，以便未经身份验证的用户能够接收错误视图。</span><span class="sxs-lookup"><span data-stu-id="806c9-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

### <a name="access-the-exception"></a><span data-ttu-id="806c9-135">访问异常</span><span class="sxs-lookup"><span data-stu-id="806c9-135">Access the exception</span></span>

<span data-ttu-id="806c9-136">使用 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 访问错误处理程序控制器或页中的异常和原始请求路径：</span><span class="sxs-lookup"><span data-stu-id="806c9-136">Use <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to access the exception and the original request path in an error handler controller or page:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/Error.cshtml.cs?name=snippet_ExceptionHandlerPathFeature&3,7)]

> [!WARNING]
> <span data-ttu-id="806c9-137">请勿  向客户端提供敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="806c9-137">Do **not** serve sensitive error information to clients.</span></span> <span data-ttu-id="806c9-138">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="806c9-138">Serving errors is a security risk.</span></span>

<span data-ttu-id="806c9-139">若要在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看异常处理页，请使用 `ProdEnvironment` 和 `ErrorHandlerPage` 预处理器指令，并选择主页上的“触发异常”  。</span><span class="sxs-lookup"><span data-stu-id="806c9-139">To see the exception handling page in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerPage` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="exception-handler-lambda"></a><span data-ttu-id="806c9-140">异常处理程序 lambda</span><span class="sxs-lookup"><span data-stu-id="806c9-140">Exception handler lambda</span></span>

<span data-ttu-id="806c9-141">[自定义异常处理程序页](#exception-handler-page)的替代方法是向 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 提供 lambda。</span><span class="sxs-lookup"><span data-stu-id="806c9-141">An alternative to a [custom exception handler page](#exception-handler-page) is to provide a lambda to <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*>.</span></span> <span data-ttu-id="806c9-142">使用 lambda，可以在返回响应前访问错误。</span><span class="sxs-lookup"><span data-stu-id="806c9-142">Using a lambda allows access to the error before returning the response.</span></span>

<span data-ttu-id="806c9-143">下面的示例展示了如何使用 lambda 进行异常处理：</span><span class="sxs-lookup"><span data-stu-id="806c9-143">Here's an example of using a lambda for exception handling:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_HandlerPageLambda)]

> [!WARNING]
> <span data-ttu-id="806c9-144">不要  向客户端提供来自 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> 或 <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> 的敏感错误信息。</span><span class="sxs-lookup"><span data-stu-id="806c9-144">Do **not** serve sensitive error information from <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerFeature> or <xref:Microsoft.AspNetCore.Diagnostics.IExceptionHandlerPathFeature> to clients.</span></span> <span data-ttu-id="806c9-145">提供服务的错误是一种安全风险。</span><span class="sxs-lookup"><span data-stu-id="806c9-145">Serving errors is a security risk.</span></span>

<span data-ttu-id="806c9-146">若要在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看异常处理 lambda 的结果，请使用 `ProdEnvironment` 和 `ErrorHandlerLambda` 预处理器指令，并选择主页上的“触发异常”  。</span><span class="sxs-lookup"><span data-stu-id="806c9-146">To see the result of the exception handling lambda in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use the `ProdEnvironment` and `ErrorHandlerLambda` preprocessor directives, and select **Trigger an exception** on the home page.</span></span>

## <a name="usestatuscodepages"></a><span data-ttu-id="806c9-147">UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="806c9-147">UseStatusCodePages</span></span>

<span data-ttu-id="806c9-148">默认情况下，ASP.NET Core 应用不会为 HTTP 状态代码（如“404 - 未找到”  ）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="806c9-148">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="806c9-149">应用返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="806c9-149">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="806c9-150">若要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="806c9-150">To provide status code pages, use Status Code Pages middleware.</span></span>

<span data-ttu-id="806c9-151">此中间件是通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="806c9-151">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="806c9-152">若要启用常见错误状态代码的默认纯文本处理程序，请在 `Startup.Configure` 方法中调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*>：</span><span class="sxs-lookup"><span data-stu-id="806c9-152">To enable default text-only handlers for common error status codes, call <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> in the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="806c9-153">在请求处理中间件（例如，静态文件中间件和 MVC 中间件）前面调用 `UseStatusCodePages`。</span><span class="sxs-lookup"><span data-stu-id="806c9-153">Call `UseStatusCodePages` before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="806c9-154">下面的示例展示了默认处理程序显示的文本：</span><span class="sxs-lookup"><span data-stu-id="806c9-154">Here's an example of text displayed by the default handlers:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="806c9-155">若要在[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中查看各种状态代码页格式之一，请使用以 `StatusCodePages` 开头的预处理器指令之一，并选择主页上的“触发 404”  。</span><span class="sxs-lookup"><span data-stu-id="806c9-155">To see one of the various status code page formats in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples), use one of the preprocessor directives that begin with `StatusCodePages`, and select **Trigger a 404** on the home page.</span></span>

## <a name="usestatuscodepages-with-format-string"></a><span data-ttu-id="806c9-156">包含格式字符串的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="806c9-156">UseStatusCodePages with format string</span></span>

<span data-ttu-id="806c9-157">若要自定义响应内容类型和文本，请利用需要使用内容类型和格式字符串的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 重载：</span><span class="sxs-lookup"><span data-stu-id="806c9-157">To customize the response content type and text, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a content type and format string:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesFormatString)]

## <a name="usestatuscodepages-with-lambda"></a><span data-ttu-id="806c9-158">包含 lambda 的 UseStatusCodePages</span><span class="sxs-lookup"><span data-stu-id="806c9-158">UseStatusCodePages with lambda</span></span>

<span data-ttu-id="806c9-159">若要指定自定义错误处理和响应写入代码，请利用需要使用 lambda 表达式的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 重载：</span><span class="sxs-lookup"><span data-stu-id="806c9-159">To specify custom error-handling and response-writing code, use the overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> that takes a lambda expression:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesLambda)]

## <a name="usestatuscodepageswithredirect"></a><span data-ttu-id="806c9-160">UseStatusCodePagesWithRedirect</span><span class="sxs-lookup"><span data-stu-id="806c9-160">UseStatusCodePagesWithRedirect</span></span>

<span data-ttu-id="806c9-161"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="806c9-161">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> extension method:</span></span>

* <span data-ttu-id="806c9-162">向客户端发送“302 - 已找到”  状态代码。</span><span class="sxs-lookup"><span data-stu-id="806c9-162">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="806c9-163">将客户端重定向到 URL 模板中的位置。</span><span class="sxs-lookup"><span data-stu-id="806c9-163">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="806c9-164">URL 模板可能会包括状态代码的 `{0}` 占位符，如上面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="806c9-164">The URL template can include a `{0}` placeholder for the status code, as shown in the example.</span></span> <span data-ttu-id="806c9-165">如果 URL 模板以波形符 (~) 开头，波形符会替换为应用的 `PathBase`。</span><span class="sxs-lookup"><span data-stu-id="806c9-165">If the URL template starts with a tilde (~), the tilde is replaced by the app's `PathBase`.</span></span> <span data-ttu-id="806c9-166">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="806c9-166">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="806c9-167">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="806c9-167">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="806c9-168">使用此方法通常是当应用：</span><span class="sxs-lookup"><span data-stu-id="806c9-168">This method is commonly used when the app:</span></span>

* <span data-ttu-id="806c9-169">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="806c9-169">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="806c9-170">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="806c9-170">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="806c9-171">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="806c9-171">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

## <a name="usestatuscodepageswithreexecute"></a><span data-ttu-id="806c9-172">UseStatusCodePagesWithReExecute</span><span class="sxs-lookup"><span data-stu-id="806c9-172">UseStatusCodePagesWithReExecute</span></span>

<span data-ttu-id="806c9-173"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> 扩展方法：</span><span class="sxs-lookup"><span data-stu-id="806c9-173">The <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> extension method:</span></span>

* <span data-ttu-id="806c9-174">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="806c9-174">Returns the original status code to the client.</span></span>
* <span data-ttu-id="806c9-175">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="806c9-175">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithReExecute)]

<span data-ttu-id="806c9-176">如果在应用中指向终结点，请为终结点创建 MVC 视图或 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="806c9-176">If you point to an endpoint within the app, create an MVC view or Razor page for the endpoint.</span></span> <span data-ttu-id="806c9-177">有关 Razor Pages 示例，请参阅[示例应用](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples)中的 Pages/StatusCode.cshtml  。</span><span class="sxs-lookup"><span data-stu-id="806c9-177">For a Razor Pages example, see *Pages/StatusCode.cshtml* in the [sample app](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/fundamentals/error-handling/samples).</span></span>

<span data-ttu-id="806c9-178">使用此方法通常是当应用应：</span><span class="sxs-lookup"><span data-stu-id="806c9-178">This method is commonly used when the app should:</span></span>

* <span data-ttu-id="806c9-179">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="806c9-179">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="806c9-180">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="806c9-180">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="806c9-181">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="806c9-181">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="806c9-182">URL 模板和查询字符串模板可能包括状态代码的占位符 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="806c9-182">The URL and query string templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="806c9-183">URL 模板必须以斜杠 (`/`) 开头。</span><span class="sxs-lookup"><span data-stu-id="806c9-183">The URL template must start with a slash (`/`).</span></span> <span data-ttu-id="806c9-184">若要在路径中使用占位符，请确认终结点（页或控制器）能否处理路径段。</span><span class="sxs-lookup"><span data-stu-id="806c9-184">When using a placeholder in the path, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="806c9-185">例如，错误的 Razor Page 应通过 `@page` 指令接受可选路径段值：</span><span class="sxs-lookup"><span data-stu-id="806c9-185">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="806c9-186">错误处理终结点可以获取生成错误的原始 URL，如下面的示例所示：</span><span class="sxs-lookup"><span data-stu-id="806c9-186">The endpoint that processes the error can get the original URL that generated the error, as shown in the following example:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Pages/StatusCode.cshtml.cs?name=snippet_StatusCodeReExecute)]

## <a name="disable-status-code-pages"></a><span data-ttu-id="806c9-187">禁用状态代码页</span><span class="sxs-lookup"><span data-stu-id="806c9-187">Disable status code pages</span></span>

<span data-ttu-id="806c9-188">若要禁用 MVC 控制器或操作方法的状态代码页，请使用 [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) 特性。</span><span class="sxs-lookup"><span data-stu-id="806c9-188">To disable status code pages for an MVC controller or action method, use the [[SkipStatusCodePages]](xref:Microsoft.AspNetCore.Mvc.SkipStatusCodePagesAttribute) attribute.</span></span>

<span data-ttu-id="806c9-189">若要禁用 Razor Pages 处理程序方法或 MVC 控制器中的特定请求的状态代码页，请使用 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>：</span><span class="sxs-lookup"><span data-stu-id="806c9-189">To disable status code pages for specific requests in a Razor Pages handler method or in an MVC controller, use <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

## <a name="exception-handling-code"></a><span data-ttu-id="806c9-190">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="806c9-190">Exception-handling code</span></span>

<span data-ttu-id="806c9-191">异常处理页中的代码可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="806c9-191">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="806c9-192">建议在生产错误页面中包含纯静态内容。</span><span class="sxs-lookup"><span data-stu-id="806c9-192">It's often a good idea for production error pages to consist of purely static content.</span></span>

### <a name="response-headers"></a><span data-ttu-id="806c9-193">响应头</span><span class="sxs-lookup"><span data-stu-id="806c9-193">Response headers</span></span>

<span data-ttu-id="806c9-194">在响应头发送后：</span><span class="sxs-lookup"><span data-stu-id="806c9-194">Once the headers for a response are sent:</span></span>

* <span data-ttu-id="806c9-195">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="806c9-195">The app can't change the response's status code.</span></span>
* <span data-ttu-id="806c9-196">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="806c9-196">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="806c9-197">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="806c9-197">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="806c9-198">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="806c9-198">Server exception handling</span></span>

<span data-ttu-id="806c9-199">除了应用中的异常处理逻辑外，[HTTP 服务器实现](xref:fundamentals/servers/index)还能处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="806c9-199">In addition to the exception handling logic in your app, the [HTTP server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="806c9-200">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的“500 - 内部服务器错误”  响应。</span><span class="sxs-lookup"><span data-stu-id="806c9-200">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="806c9-201">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="806c9-201">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="806c9-202">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="806c9-202">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="806c9-203">当服务器处理请求时，发生的任何异常都将由服务器的异常处理进行处理。</span><span class="sxs-lookup"><span data-stu-id="806c9-203">Any exception that occurs when the server is handling the request is handled by the server's exception handling.</span></span> <span data-ttu-id="806c9-204">应用的自定义错误页面、异常处理中间件和筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="806c9-204">The app's custom error pages, exception handling middleware, and filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="806c9-205">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="806c9-205">Startup exception handling</span></span>

<span data-ttu-id="806c9-206">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="806c9-206">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="806c9-207">可以将主机配置为，[捕获启动错误](xref:fundamentals/host/web-host#capture-startup-errors)和[捕获详细错误](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="806c9-207">The host can be configured to [capture startup errors](xref:fundamentals/host/web-host#capture-startup-errors) and [capture detailed errors](xref:fundamentals/host/web-host#detailed-errors).</span></span>

<span data-ttu-id="806c9-208">仅当错误在主机地址/端口绑定后出现时，托管层才能显示捕获的启动错误的错误页。</span><span class="sxs-lookup"><span data-stu-id="806c9-208">The hosting layer can show an error page for a captured startup error only if the error occurs after host address/port binding.</span></span> <span data-ttu-id="806c9-209">如果绑定失败：</span><span class="sxs-lookup"><span data-stu-id="806c9-209">If binding fails:</span></span>

* <span data-ttu-id="806c9-210">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="806c9-210">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="806c9-211">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="806c9-211">The dotnet process crashes.</span></span>
* <span data-ttu-id="806c9-212">不会在 HTTP 服务器为 [Kestrel](xref:fundamentals/servers/kestrel) 时显示任何错误页。</span><span class="sxs-lookup"><span data-stu-id="806c9-212">No error page is displayed when the HTTP server is [Kestrel](xref:fundamentals/servers/kestrel).</span></span>

<span data-ttu-id="806c9-213">在 [IIS](/iis)（或 Azure 应用服务）或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”  。</span><span class="sxs-lookup"><span data-stu-id="806c9-213">When running on [IIS](/iis) (or Azure App Service) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="806c9-214">有关详细信息，请参阅 <xref:test/troubleshoot-azure-iis>。</span><span class="sxs-lookup"><span data-stu-id="806c9-214">For more information, see <xref:test/troubleshoot-azure-iis>.</span></span>

## <a name="database-error-page"></a><span data-ttu-id="806c9-215">数据库错误页</span><span class="sxs-lookup"><span data-stu-id="806c9-215">Database error page</span></span>

<span data-ttu-id="806c9-216">[数据库错误页](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>)中间件捕获与数据库相关的异常，可使用实体框架迁移来解析这些异常。</span><span class="sxs-lookup"><span data-stu-id="806c9-216">The [Database Error Page](<xref:Microsoft.AspNetCore.Builder.DatabaseErrorPageExtensions.UseDatabaseErrorPage*>) middleware captures database-related exceptions that can be resolved by using Entity Framework migrations.</span></span> <span data-ttu-id="806c9-217">当这些异常出现时，便会生成 HTML 响应，其中包含用于解决问题的可能操作的详细信息。</span><span class="sxs-lookup"><span data-stu-id="806c9-217">When these exceptions occur, an HTML response with details of possible actions to resolve the issue is generated.</span></span> <span data-ttu-id="806c9-218">应仅在开发环境中启用此页。</span><span class="sxs-lookup"><span data-stu-id="806c9-218">This page should be enabled only in the Development environment.</span></span> <span data-ttu-id="806c9-219">通过向 `Startup.Configure` 添加代码来启用此页：</span><span class="sxs-lookup"><span data-stu-id="806c9-219">Enable the page by adding code to `Startup.Configure`:</span></span>

```csharp
if (env.IsDevelopment())
{
    app.UseDatabaseErrorPage();
}
```

## <a name="exception-filters"></a><span data-ttu-id="806c9-220">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="806c9-220">Exception filters</span></span>

<span data-ttu-id="806c9-221">在 MVC 应用中，可以全局配置异常筛选器，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="806c9-221">In MVC apps, exception filters can be configured globally or on a per-controller or per-action basis.</span></span> <span data-ttu-id="806c9-222">在 Razor Pages 应用中，可以全局配置异常筛选器，也可以为每个页面模型单独配置。</span><span class="sxs-lookup"><span data-stu-id="806c9-222">In Razor Pages apps, they can be configured globally or per page model.</span></span> <span data-ttu-id="806c9-223">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="806c9-223">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="806c9-224">有关详细信息，请参阅 <xref:mvc/controllers/filters#exception-filters>。</span><span class="sxs-lookup"><span data-stu-id="806c9-224">For more information, see <xref:mvc/controllers/filters#exception-filters>.</span></span>

> [!TIP]
> <span data-ttu-id="806c9-225">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如异常处理中间件灵活。</span><span class="sxs-lookup"><span data-stu-id="806c9-225">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as the Exception Handling Middleware.</span></span> <span data-ttu-id="806c9-226">建议使用中间件。</span><span class="sxs-lookup"><span data-stu-id="806c9-226">We recommend using the middleware.</span></span> <span data-ttu-id="806c9-227">仅在需要根据选定 MVC 操作以不同方式执行错误处理时，才使用筛选器。</span><span class="sxs-lookup"><span data-stu-id="806c9-227">Use filters only where you need to perform error handling differently based on which MVC action is chosen.</span></span>

## <a name="model-state-errors"></a><span data-ttu-id="806c9-228">模型状态错误</span><span class="sxs-lookup"><span data-stu-id="806c9-228">Model state errors</span></span>

<span data-ttu-id="806c9-229">若要了解如何处理模型状态错误，请参阅[模型绑定](xref:mvc/models/model-binding)和[模型验证](xref:mvc/models/validation)。</span><span class="sxs-lookup"><span data-stu-id="806c9-229">For information about how to handle model state errors, see [Model binding](xref:mvc/models/model-binding) and [Model validation](xref:mvc/models/validation).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="806c9-230">其他资源</span><span class="sxs-lookup"><span data-stu-id="806c9-230">Additional resources</span></span>

* <xref:test/troubleshoot-azure-iis>
* <xref:host-and-deploy/azure-iis-errors-reference>
