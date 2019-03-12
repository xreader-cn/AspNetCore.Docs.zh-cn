---
title: 处理 ASP.NET Core 中的错误
author: tdykstra
description: 了解如何处理 ASP.NET Core 应用中的错误。
monikerRange: '>= aspnetcore-2.1'
ms.author: tdykstra
ms.custom: mvc
ms.date: 03/01/2019
uid: fundamentals/error-handling
ms.openlocfilehash: a2ae2cb25c8cc5048b189b4035abbfc32a29aaff
ms.sourcegitcommit: 036d4b03fd86ca5bb378198e29ecf2704257f7b2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/05/2019
ms.locfileid: "57345483"
---
# <a name="handle-errors-in-aspnet-core"></a><span data-ttu-id="25d3a-103">处理 ASP.NET Core 中的错误</span><span class="sxs-lookup"><span data-stu-id="25d3a-103">Handle errors in ASP.NET Core</span></span>

<span data-ttu-id="25d3a-104">作者：[Tom Dykstra](https://github.com/tdykstra/)、[Luke Latham](https://github.com/guardrex) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="25d3a-104">By [Tom Dykstra](https://github.com/tdykstra/), [Luke Latham](https://github.com/guardrex), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="25d3a-105">本文介绍了处理 ASP.NET Core 应用中常见错误的一些方法。</span><span class="sxs-lookup"><span data-stu-id="25d3a-105">This article covers common approaches to handling errors in ASP.NET Core apps.</span></span>

<span data-ttu-id="25d3a-106">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="25d3a-106">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="developer-exception-page"></a><span data-ttu-id="25d3a-107">开发人员异常页</span><span class="sxs-lookup"><span data-stu-id="25d3a-107">Developer Exception Page</span></span>

<span data-ttu-id="25d3a-108">要将应用配置为显示有关异常的详细信息的页面，请使用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="25d3a-108">To configure an app to display a page that shows detailed information about exceptions, use the *Developer Exception Page*.</span></span> <span data-ttu-id="25d3a-109">该页面通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="25d3a-109">The page is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span> <span data-ttu-id="25d3a-110">向 `Startup.Configure` 方法添加代码行：</span><span class="sxs-lookup"><span data-stu-id="25d3a-110">Add a line to the `Startup.Configure` method:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=5)]

<span data-ttu-id="25d3a-111">将对 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> 的调用放在要对其捕获异常的任何中间件前面。</span><span class="sxs-lookup"><span data-stu-id="25d3a-111">Place the call to <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> in front of any middleware where you want to catch exceptions.</span></span>

> [!WARNING]
> <span data-ttu-id="25d3a-112">仅当应用程序在开发环境中运行时才启用开发人员异常页。</span><span class="sxs-lookup"><span data-stu-id="25d3a-112">Enable the Developer Exception Page **only when the app is running in the Development environment**.</span></span> <span data-ttu-id="25d3a-113">否则当应用程序在生产环境中运行时，详细的异常信息会向公众泄露</span><span class="sxs-lookup"><span data-stu-id="25d3a-113">You don't want to share detailed exception information publicly when the app runs in production.</span></span> <span data-ttu-id="25d3a-114">有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。</span><span class="sxs-lookup"><span data-stu-id="25d3a-114">For more information on configuring environments, see <xref:fundamentals/environments>.</span></span>

<span data-ttu-id="25d3a-115">要查看开发人员异常页，请将环境设置为 `Development`，运行示例应用，并向应用的基 URL 添加 `?throw=true`。</span><span class="sxs-lookup"><span data-stu-id="25d3a-115">To see the Developer Exception Page, run the sample app with the environment set to `Development` and add `?throw=true` to the base URL of the app.</span></span> <span data-ttu-id="25d3a-116">该页包括关于异常和请求的以下信息：</span><span class="sxs-lookup"><span data-stu-id="25d3a-116">The page includes the following information about the exception and the request:</span></span>

* <span data-ttu-id="25d3a-117">堆栈跟踪</span><span class="sxs-lookup"><span data-stu-id="25d3a-117">Stack trace</span></span>
* <span data-ttu-id="25d3a-118">查询字符串参数（如果有）</span><span class="sxs-lookup"><span data-stu-id="25d3a-118">Query string parameters (if any)</span></span>
* <span data-ttu-id="25d3a-119">Cookie（如果有）</span><span class="sxs-lookup"><span data-stu-id="25d3a-119">Cookies (if any)</span></span>
* <span data-ttu-id="25d3a-120">标头</span><span class="sxs-lookup"><span data-stu-id="25d3a-120">Headers</span></span>

## <a name="configure-a-custom-exception-handling-page"></a><span data-ttu-id="25d3a-121">配置自定义异常处理页</span><span class="sxs-lookup"><span data-stu-id="25d3a-121">Configure a custom exception handling page</span></span>

<span data-ttu-id="25d3a-122">如果应用未在开发环境中运行，则调用 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 扩展方法，添加异常处理中间件。</span><span class="sxs-lookup"><span data-stu-id="25d3a-122">When the app isn't running in the Development environment, call the <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> extension method to add Exception Handling Middleware.</span></span> <span data-ttu-id="25d3a-123">中间件：</span><span class="sxs-lookup"><span data-stu-id="25d3a-123">The middleware:</span></span>

* <span data-ttu-id="25d3a-124">捕获异常。</span><span class="sxs-lookup"><span data-stu-id="25d3a-124">Catches exceptions.</span></span>
* <span data-ttu-id="25d3a-125">记录异常。</span><span class="sxs-lookup"><span data-stu-id="25d3a-125">Logs exceptions.</span></span>
* <span data-ttu-id="25d3a-126">在备用管道中为指定的页或控制器重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="25d3a-126">Re-executes the request in an alternate pipeline for the page or controller indicated.</span></span> <span data-ttu-id="25d3a-127">如果响应已启动，则不会重新执行请求。</span><span class="sxs-lookup"><span data-stu-id="25d3a-127">The request isn't re-executed if the response has started.</span></span>

<span data-ttu-id="25d3a-128">在示例应用的以下示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 在非开发环境中添加了异常处理中间件。</span><span class="sxs-lookup"><span data-stu-id="25d3a-128">In the following example from the sample app, <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> adds the Exception Handling Middleware in non-Development environments.</span></span> <span data-ttu-id="25d3a-129">在捕获并记录异常后，扩展方法指定 `/Error` 终结点处重新执行了请求的错误页或控制器：</span><span class="sxs-lookup"><span data-stu-id="25d3a-129">The extension method specifies an error page or controller at the `/Error` endpoint for re-executed requests after exceptions are caught and logged:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=9)]

<span data-ttu-id="25d3a-130">Razor Pages 应用模板在“Pages”文件夹中提供了一个错误页 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`)。</span><span class="sxs-lookup"><span data-stu-id="25d3a-130">The Razor Pages app template provides an Error page (*.cshtml*) and <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> class (`ErrorModel`) in the Pages folder.</span></span>

<span data-ttu-id="25d3a-131">在 MVC 应用中，以下错误处理程序方法包含在 MVC 应用模板中并在主控制器中显示：</span><span class="sxs-lookup"><span data-stu-id="25d3a-131">In an MVC app, the following error handler method is included in the MVC app template and appears in the Home controller:</span></span>

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

<span data-ttu-id="25d3a-132">不要使用 HTTP 方法属性（如 `HttpGet`）修饰错误处理程序操作方法。</span><span class="sxs-lookup"><span data-stu-id="25d3a-132">Don't decorate the error handler action method with HTTP method attributes, such as `HttpGet`.</span></span> <span data-ttu-id="25d3a-133">显式谓词可阻止某些请求访问方法。</span><span class="sxs-lookup"><span data-stu-id="25d3a-133">Explicit verbs prevent some requests from reaching the method.</span></span> <span data-ttu-id="25d3a-134">允许匿名访问方法，以便未经身份验证的用户能够接收错误视图。</span><span class="sxs-lookup"><span data-stu-id="25d3a-134">Allow anonymous access to the method so that unauthenticated users are able to receive the error view.</span></span>

## <a name="configure-status-code-pages"></a><span data-ttu-id="25d3a-135">配置状态代码页</span><span class="sxs-lookup"><span data-stu-id="25d3a-135">Configure status code pages</span></span>

<span data-ttu-id="25d3a-136">默认情况下，ASP.NET Core 应用不会为 HTTP 状态代码（如“404 - 未找到”）提供状态代码页。</span><span class="sxs-lookup"><span data-stu-id="25d3a-136">By default, an ASP.NET Core app doesn't provide a status code page for HTTP status codes, such as *404 - Not Found*.</span></span> <span data-ttu-id="25d3a-137">应用返回状态代码和空响应正文。</span><span class="sxs-lookup"><span data-stu-id="25d3a-137">The app returns a status code and an empty response body.</span></span> <span data-ttu-id="25d3a-138">要提供状态代码页，请使用状态代码页中间件。</span><span class="sxs-lookup"><span data-stu-id="25d3a-138">To provide status code pages, use Status Code Pages Middleware.</span></span>

<span data-ttu-id="25d3a-139">该中间件通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。</span><span class="sxs-lookup"><span data-stu-id="25d3a-139">The middleware is made available by the [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) package, which is available in the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app).</span></span>

<span data-ttu-id="25d3a-140">向 `Startup.Configure` 方法添加代码行：</span><span class="sxs-lookup"><span data-stu-id="25d3a-140">Add a line to the `Startup.Configure` method:</span></span>

```csharp
app.UseStatusCodePages();
```

<span data-ttu-id="25d3a-141">在请求处理中间件（例如，静态文件中间件和 MVC 中间件）之前调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 方法。</span><span class="sxs-lookup"><span data-stu-id="25d3a-141">Call the <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> method before request handling middleware (for example, Static File Middleware and MVC Middleware).</span></span>

<span data-ttu-id="25d3a-142">默认情况下，状态代码页中间件为常见状态代码（如“404 - 未找到”）添加纯文本处理程序：</span><span class="sxs-lookup"><span data-stu-id="25d3a-142">By default, Status Code Pages Middleware adds text-only handlers for common status codes, such as *404 - Not Found*:</span></span>

```
Status Code: 404; Not Found
```

<span data-ttu-id="25d3a-143">中间件支持几种可用于自定义其行为的扩展方法。</span><span class="sxs-lookup"><span data-stu-id="25d3a-143">The middleware supports several extension methods that allow you to customize its behavior.</span></span>

<span data-ttu-id="25d3a-144">重载 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 采用 lambda 表达式，可用于处理自定义错误处理逻辑和手动编写响应：</span><span class="sxs-lookup"><span data-stu-id="25d3a-144">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a lambda expression, which you can use to process custom error-handling logic and manually write the response:</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

<span data-ttu-id="25d3a-145">重载 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 采用内容类型和格式字符串，可用于自定义内容类型和响应文本：</span><span class="sxs-lookup"><span data-stu-id="25d3a-145">An overload of <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> takes a content type and format string, which you can use to customize the content type and response text:</span></span>

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a><span data-ttu-id="25d3a-146">重定向和重新执行扩展方法</span><span class="sxs-lookup"><span data-stu-id="25d3a-146">Redirect and re-execute extension methods</span></span>

<span data-ttu-id="25d3a-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>：</span><span class="sxs-lookup"><span data-stu-id="25d3a-147"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>:</span></span>

* <span data-ttu-id="25d3a-148">向客户端发送“302 - 已找到”状态代码。</span><span class="sxs-lookup"><span data-stu-id="25d3a-148">Sends a *302 - Found* status code to the client.</span></span>
* <span data-ttu-id="25d3a-149">将客户端重定向到 URL 模板中的位置。</span><span class="sxs-lookup"><span data-stu-id="25d3a-149">Redirects the client to the location provided in the URL template.</span></span>

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

<span data-ttu-id="25d3a-150">通常，应用在以下情况下使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>：</span><span class="sxs-lookup"><span data-stu-id="25d3a-150"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*> is commonly used when the app:</span></span>

* <span data-ttu-id="25d3a-151">应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。</span><span class="sxs-lookup"><span data-stu-id="25d3a-151">Should redirect the client to a different endpoint, usually in cases where a different app processes the error.</span></span> <span data-ttu-id="25d3a-152">对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。</span><span class="sxs-lookup"><span data-stu-id="25d3a-152">For web apps, the client's browser address bar reflects the redirected endpoint.</span></span>
* <span data-ttu-id="25d3a-153">不应保留原始状态代码并通过初始重定向响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="25d3a-153">Shouldn't preserve and return the original status code with the initial redirect response.</span></span>

<span data-ttu-id="25d3a-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>：</span><span class="sxs-lookup"><span data-stu-id="25d3a-154"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>:</span></span>

* <span data-ttu-id="25d3a-155">向客户端返回原始状态代码。</span><span class="sxs-lookup"><span data-stu-id="25d3a-155">Returns the original status code to the client.</span></span>
* <span data-ttu-id="25d3a-156">通过使用备用路径重新执行请求管道，从而生成响应正文。</span><span class="sxs-lookup"><span data-stu-id="25d3a-156">Generates the response body by re-executing the request pipeline using an alternate path.</span></span>

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

<span data-ttu-id="25d3a-157">应用在以下情况下通常使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>：</span><span class="sxs-lookup"><span data-stu-id="25d3a-157"><xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*> is commonly used when the app should:</span></span>

* <span data-ttu-id="25d3a-158">处理请求，但不重定向到不同终结点。</span><span class="sxs-lookup"><span data-stu-id="25d3a-158">Process the request without redirecting to a different endpoint.</span></span> <span data-ttu-id="25d3a-159">对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。</span><span class="sxs-lookup"><span data-stu-id="25d3a-159">For web apps, the client's browser address bar reflects the originally requested endpoint.</span></span>
* <span data-ttu-id="25d3a-160">保留原始状态代码并通过响应返回该代码。</span><span class="sxs-lookup"><span data-stu-id="25d3a-160">Preserve and return the original status code with the response.</span></span>

<span data-ttu-id="25d3a-161">模板可能包括状态代码的占位符 (`{0}`)。</span><span class="sxs-lookup"><span data-stu-id="25d3a-161">Templates may include a placeholder (`{0}`) for the status code.</span></span> <span data-ttu-id="25d3a-162">模板必须以正斜杠 (`/`) 开头。</span><span class="sxs-lookup"><span data-stu-id="25d3a-162">The template must start with a forward slash (`/`).</span></span> <span data-ttu-id="25d3a-163">使用占位符时，请确认终结点（页或控制器）可以处理路径段。</span><span class="sxs-lookup"><span data-stu-id="25d3a-163">When using a placeholder, confirm that the endpoint (page or controller) can process the path segment.</span></span> <span data-ttu-id="25d3a-164">例如，错误的 Razor Page 应通过 `@page` 指令接受可选路径段值：</span><span class="sxs-lookup"><span data-stu-id="25d3a-164">For example, a Razor Page for errors should accept the optional path segment value with the `@page` directive:</span></span>

```cshtml
@page "{code?}"
```

<span data-ttu-id="25d3a-165">可禁用 Razor 页处理程序方法或 MVC 控制器中的特定请求的状态代码页。</span><span class="sxs-lookup"><span data-stu-id="25d3a-165">Status code pages can be disabled for specific requests in a Razor Pages handler method or in an MVC controller.</span></span> <span data-ttu-id="25d3a-166">要禁用状态代码页，请尝试从请求的 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) 集合中检索 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>，并在功能可用时禁用该功能：</span><span class="sxs-lookup"><span data-stu-id="25d3a-166">To disable status code pages, attempt to retrieve the <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature> from the request's [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) collection and disable the feature if it's available:</span></span>

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

<span data-ttu-id="25d3a-167">若要在应用中使用指向终结点的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 重载，请为终结点创建 MVC 视图或 Razor Page。</span><span class="sxs-lookup"><span data-stu-id="25d3a-167">To use a <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> overload that points to an endpoint within the app, create an MVC view or Razor Page for the endpoint.</span></span> <span data-ttu-id="25d3a-168">例如，Razor Pages 应用模板将生成以下页和页模型类：</span><span class="sxs-lookup"><span data-stu-id="25d3a-168">For example, the Razor Pages app template produces the following page and page model class:</span></span>

<span data-ttu-id="25d3a-169">Error.cshtml：</span><span class="sxs-lookup"><span data-stu-id="25d3a-169">*Error.cshtml*:</span></span>

::: moniker range=">= aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to the <strong>Development</strong> environment displays 
    detailed information about the error that occurred.
</p>
<p>
    <strong>The Development environment shouldn't be enabled for deployed 
    applications.</strong> It can result in displaying sensitive information 
    from exceptions to end users. For local debugging, enable the 
    <strong>Development</strong> environment by setting the 
    <strong>ASPNETCORE_ENVIRONMENT</strong> environment variable to 
    <strong>Development</strong> and restarting the app.
</p>
```

<span data-ttu-id="25d3a-170">Error.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="25d3a-170">*Error.cshtml.cs*:</span></span>

```csharp
[ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, NoStore = true)]
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

::: moniker range="< aspnetcore-2.2"

```cshtml
@page
@model ErrorModel
@{
    ViewData["Title"] = "Error";
}

<h1 class="text-danger">Error.</h1>
<h2 class="text-danger">An error occurred while processing your request.</h2>

@if (Model.ShowRequestId)
{
    <p>
        <strong>Request ID:</strong> <code>@Model.RequestId</code>
    </p>
}

<h3>Development Mode</h3>
<p>
    Swapping to <strong>Development</strong> environment will display more detailed 
    information about the error that occurred.
</p>
<p>
    <strong>Development environment should not be enabled in deployed applications
    </strong>, as it can result in sensitive information from exceptions being 
    displayed to end users. For local debugging, development environment can be 
    enabled by setting the <strong>ASPNETCORE_ENVIRONMENT</strong> environment 
    variable to <strong>Development</strong>, and restarting the application.
</p>
```

<span data-ttu-id="25d3a-171">Error.cshtml.cs：</span><span class="sxs-lookup"><span data-stu-id="25d3a-171">*Error.cshtml.cs*:</span></span>

```csharp
public class ErrorModel : PageModel
{
    public string RequestId { get; set; }

    public bool ShowRequestId => !string.IsNullOrEmpty(RequestId);

    [ResponseCache(Duration = 0, Location = ResponseCacheLocation.None, 
        NoStore = true)]
    public void OnGet()
    {
        RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier;
    }
}
```

::: moniker-end

## <a name="exception-handling-code"></a><span data-ttu-id="25d3a-172">异常处理代码</span><span class="sxs-lookup"><span data-stu-id="25d3a-172">Exception-handling code</span></span>

<span data-ttu-id="25d3a-173">异常处理页中的代码可能会引发异常。</span><span class="sxs-lookup"><span data-stu-id="25d3a-173">Code in exception handling pages can throw exceptions.</span></span> <span data-ttu-id="25d3a-174">建议在生产错误页面中包含纯静态内容。</span><span class="sxs-lookup"><span data-stu-id="25d3a-174">It's often a good idea for production error pages to consist of purely static content.</span></span>

<span data-ttu-id="25d3a-175">此外，请注意，发送响应标头后：</span><span class="sxs-lookup"><span data-stu-id="25d3a-175">Also, be aware that once the headers for a response are sent:</span></span>

* <span data-ttu-id="25d3a-176">应用无法更改响应的状态代码。</span><span class="sxs-lookup"><span data-stu-id="25d3a-176">The app can't change the response's status code.</span></span>
* <span data-ttu-id="25d3a-177">任何异常页或处理程序都无法运行。</span><span class="sxs-lookup"><span data-stu-id="25d3a-177">Any exception pages or handlers can't run.</span></span> <span data-ttu-id="25d3a-178">必须完成响应或中止连接。</span><span class="sxs-lookup"><span data-stu-id="25d3a-178">The response must be completed or the connection aborted.</span></span>

## <a name="server-exception-handling"></a><span data-ttu-id="25d3a-179">服务器异常处理</span><span class="sxs-lookup"><span data-stu-id="25d3a-179">Server exception handling</span></span>

<span data-ttu-id="25d3a-180">除应用中的异常处理逻辑外，[服务器实现](xref:fundamentals/servers/index)也可以处理一些异常。</span><span class="sxs-lookup"><span data-stu-id="25d3a-180">In addition to the exception handling logic in your app, the [server implementation](xref:fundamentals/servers/index) can handle some exceptions.</span></span> <span data-ttu-id="25d3a-181">如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的“500 - 内部服务器错误”响应。</span><span class="sxs-lookup"><span data-stu-id="25d3a-181">If the server catches an exception before response headers are sent, the server sends a *500 - Internal Server Error* response without a response body.</span></span> <span data-ttu-id="25d3a-182">如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。</span><span class="sxs-lookup"><span data-stu-id="25d3a-182">If the server catches an exception after response headers are sent, the server closes the connection.</span></span> <span data-ttu-id="25d3a-183">应用程序无法处理的请求将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="25d3a-183">Requests that aren't handled by your app are handled by the server.</span></span> <span data-ttu-id="25d3a-184">发生的任何异常都将由服务器进行处理。</span><span class="sxs-lookup"><span data-stu-id="25d3a-184">Any exception that occurs is handled by the server's exception handling.</span></span> <span data-ttu-id="25d3a-185">任何配置的自定义错误页面或异常处理中间件或筛选器都不会影响此行为。</span><span class="sxs-lookup"><span data-stu-id="25d3a-185">Any configured custom error pages or exception handling middleware or filters don't affect this behavior.</span></span>

## <a name="startup-exception-handling"></a><span data-ttu-id="25d3a-186">启动异常处理</span><span class="sxs-lookup"><span data-stu-id="25d3a-186">Startup exception handling</span></span>

<span data-ttu-id="25d3a-187">应用程序启动期间发生的异常仅可在承载层进行处理。</span><span class="sxs-lookup"><span data-stu-id="25d3a-187">Only the hosting layer can handle exceptions that take place during app startup.</span></span> <span data-ttu-id="25d3a-188">借助 [Web 主机](xref:fundamentals/host/web-host)，可以使用 `captureStartupErrors` 和 `detailedErrors` 键[配置主机在响应启动期间出现错误时的行为方式](xref:fundamentals/host/web-host#detailed-errors)。</span><span class="sxs-lookup"><span data-stu-id="25d3a-188">Using [Web Host](xref:fundamentals/host/web-host), you can [configure how the host behaves in response to errors during startup](xref:fundamentals/host/web-host#detailed-errors) with the `captureStartupErrors` and `detailedErrors` keys.</span></span>

<span data-ttu-id="25d3a-189">仅当捕获的启动错误发生在主机地址/端口绑定之后，承载层才会为该错误显示错误页。</span><span class="sxs-lookup"><span data-stu-id="25d3a-189">Hosting can only show an error page for a captured startup error if the error occurs after host address/port binding.</span></span> <span data-ttu-id="25d3a-190">如果出于任何原因导致任何绑定失败：</span><span class="sxs-lookup"><span data-stu-id="25d3a-190">If any binding fails for any reason:</span></span>

* <span data-ttu-id="25d3a-191">托管层将记录关键异常。</span><span class="sxs-lookup"><span data-stu-id="25d3a-191">The hosting layer logs a critical exception.</span></span>
* <span data-ttu-id="25d3a-192">dotnet 进程崩溃。</span><span class="sxs-lookup"><span data-stu-id="25d3a-192">The dotnet process crashes.</span></span>
* <span data-ttu-id="25d3a-193">当应用在 [Kestrel](xref:fundamentals/servers/kestrel) 服务器上运行时，不会显示错误页。</span><span class="sxs-lookup"><span data-stu-id="25d3a-193">No error page is displayed when the app is running on the [Kestrel](xref:fundamentals/servers/kestrel) server.</span></span>

<span data-ttu-id="25d3a-194">在 [IIS](/iis) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。</span><span class="sxs-lookup"><span data-stu-id="25d3a-194">When running on [IIS](/iis) or [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview), a *502.5 - Process Failure* is returned by the [ASP.NET Core Module](xref:host-and-deploy/aspnet-core-module) if the process can't start.</span></span> <span data-ttu-id="25d3a-195">有关更多信息，请参见<xref:host-and-deploy/iis/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="25d3a-195">For more information, see <xref:host-and-deploy/iis/troubleshoot>.</span></span> <span data-ttu-id="25d3a-196">要了解如何排查 Azure 应用服务的启动问题，请参阅 <xref:host-and-deploy/azure-apps/troubleshoot>。</span><span class="sxs-lookup"><span data-stu-id="25d3a-196">For information on troubleshooting startup issues with Azure App Service, see <xref:host-and-deploy/azure-apps/troubleshoot>.</span></span>

## <a name="aspnet-core-mvc-error-handling"></a><span data-ttu-id="25d3a-197">ASP.NET Core MVC 错误处理</span><span class="sxs-lookup"><span data-stu-id="25d3a-197">ASP.NET Core MVC error handling</span></span>

<span data-ttu-id="25d3a-198">[MVC](xref:mvc/overview) 应用还有一些其他的错误处理选项，例如配置异常筛选器和执行模型验证。</span><span class="sxs-lookup"><span data-stu-id="25d3a-198">[MVC](xref:mvc/overview) apps have some additional options for handling errors, such as configuring exception filters and performing model validation.</span></span>

### <a name="exception-filters"></a><span data-ttu-id="25d3a-199">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="25d3a-199">Exception filters</span></span>

<span data-ttu-id="25d3a-200">在 MVC 应用中，异常筛选器可以进行全局配置，也可以为每个控制器或每个操作单独配置。</span><span class="sxs-lookup"><span data-stu-id="25d3a-200">Exception filters can be configured globally or on a per-controller or per-action basis in an MVC app.</span></span> <span data-ttu-id="25d3a-201">这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。</span><span class="sxs-lookup"><span data-stu-id="25d3a-201">These filters handle any unhandled exception that occurs during the execution of a controller action or another filter.</span></span> <span data-ttu-id="25d3a-202">不会以其他方式调用这些筛选器。</span><span class="sxs-lookup"><span data-stu-id="25d3a-202">These filters aren't called otherwise.</span></span> <span data-ttu-id="25d3a-203">若要了解详细信息，请参阅 <xref:mvc/controllers/filters>。</span><span class="sxs-lookup"><span data-stu-id="25d3a-203">To learn more, see <xref:mvc/controllers/filters>.</span></span>

> [!TIP]
> <span data-ttu-id="25d3a-204">异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如错误处理中间件灵活。</span><span class="sxs-lookup"><span data-stu-id="25d3a-204">Exception filters are useful for trapping exceptions that occur within MVC actions, but they're not as flexible as error handling middleware.</span></span> <span data-ttu-id="25d3a-205">建议使用中间件。</span><span class="sxs-lookup"><span data-stu-id="25d3a-205">We recommend the use of middleware.</span></span> <span data-ttu-id="25d3a-206">仅在需要根据所选 MVC 操作以不同方式执行错误处理时，才使用筛选器。</span><span class="sxs-lookup"><span data-stu-id="25d3a-206">Use filters only where you need to perform error handling *differently* based on which MVC action is chosen.</span></span>

### <a name="handle-model-state-errors"></a><span data-ttu-id="25d3a-207">处理模型状态错误</span><span class="sxs-lookup"><span data-stu-id="25d3a-207">Handle model state errors</span></span>

<span data-ttu-id="25d3a-208">[模型验证](xref:mvc/models/validation)在每个控制器操作被调用之前发生，操作方法负责检查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 并相应地作出反应。</span><span class="sxs-lookup"><span data-stu-id="25d3a-208">[Model validation](xref:mvc/models/validation) occurs prior to invoking each controller action, and it's the action method's responsibility to inspect [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) and react appropriately.</span></span>

<span data-ttu-id="25d3a-209">某些应用使用标准约定处理[模型验证](xref:mvc/models/validation)错误，在这种情况下，使用[筛选器](xref:mvc/controllers/filters)可以更好地实施这种策略。</span><span class="sxs-lookup"><span data-stu-id="25d3a-209">Some apps choose to follow a standard convention for dealing with [model validation](xref:mvc/models/validation) errors, in which case a [filter](xref:mvc/controllers/filters) may be an appropriate place to implement such a policy.</span></span> <span data-ttu-id="25d3a-210">你需要使用无效模型状态测试操作的行为。</span><span class="sxs-lookup"><span data-stu-id="25d3a-210">You should test how your actions behave with invalid model states.</span></span> <span data-ttu-id="25d3a-211">有关更多信息，请参见<xref:mvc/controllers/testing>。</span><span class="sxs-lookup"><span data-stu-id="25d3a-211">For more information, see <xref:mvc/controllers/testing>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="25d3a-212">其他资源</span><span class="sxs-lookup"><span data-stu-id="25d3a-212">Additional resources</span></span>

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
