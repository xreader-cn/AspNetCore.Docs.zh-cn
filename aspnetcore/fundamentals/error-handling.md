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
# <a name="handle-errors-in-aspnet-core"></a>处理 ASP.NET Core 中的错误

作者：[Tom Dykstra](https://github.com/tdykstra/)、[Luke Latham](https://github.com/guardrex) 和 [Steve Smith](https://ardalis.com/)

本文介绍了处理 ASP.NET Core 应用中常见错误的一些方法。

[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/fundamentals/error-handling/samples/2.x)（[如何下载](xref:index#how-to-download-a-sample)）

## <a name="developer-exception-page"></a>开发人员异常页

要将应用配置为显示有关异常的详细信息的页面，请使用开发人员异常页。 该页面通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。 向 `Startup.Configure` 方法添加代码行：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=5)]

将对 <xref:Microsoft.AspNetCore.Builder.DeveloperExceptionPageExtensions.UseDeveloperExceptionPage*> 的调用放在要对其捕获异常的任何中间件前面。

> [!WARNING]
> 仅当应用程序在开发环境中运行时才启用开发人员异常页。 否则当应用程序在生产环境中运行时，详细的异常信息会向公众泄露 有关配置环境的详细信息，请参阅 <xref:fundamentals/environments>。

要查看开发人员异常页，请将环境设置为 `Development`，运行示例应用，并向应用的基 URL 添加 `?throw=true`。 该页包括关于异常和请求的以下信息：

* 堆栈跟踪
* 查询字符串参数（如果有）
* Cookie（如果有）
* 标头

## <a name="configure-a-custom-exception-handling-page"></a>配置自定义异常处理页

如果应用未在开发环境中运行，则调用 <xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 扩展方法，添加异常处理中间件。 中间件：

* 捕获异常。
* 记录异常。
* 在备用管道中为指定的页或控制器重新执行请求。 如果响应已启动，则不会重新执行请求。

在示例应用的以下示例中，<xref:Microsoft.AspNetCore.Builder.ExceptionHandlerExtensions.UseExceptionHandler*> 在非开发环境中添加了异常处理中间件。 在捕获并记录异常后，扩展方法指定 `/Error` 终结点处重新执行了请求的错误页或控制器：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_DevExceptionPage&highlight=9)]

Razor Pages 应用模板在“Pages”文件夹中提供了一个错误页 (.cshtml) 和 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel> 类 (`ErrorModel`)。

在 MVC 应用中，以下错误处理程序方法包含在 MVC 应用模板中并在主控制器中显示：

```csharp
[AllowAnonymous]
public IActionResult Error()
{
    return View(new ErrorViewModel 
        { RequestId = Activity.Current?.Id ?? HttpContext.TraceIdentifier });
}
```

不要使用 HTTP 方法属性（如 `HttpGet`）修饰错误处理程序操作方法。 显式谓词可阻止某些请求访问方法。 允许匿名访问方法，以便未经身份验证的用户能够接收错误视图。

## <a name="configure-status-code-pages"></a>配置状态代码页

默认情况下，ASP.NET Core 应用不会为 HTTP 状态代码（如“404 - 未找到”）提供状态代码页。 应用返回状态代码和空响应正文。 要提供状态代码页，请使用状态代码页中间件。

该中间件通过 [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)中的 [Microsoft.AspNetCore.Diagnostics](https://www.nuget.org/packages/Microsoft.AspNetCore.Diagnostics/) 包提供。

向 `Startup.Configure` 方法添加代码行：

```csharp
app.UseStatusCodePages();
```

在请求处理中间件（例如，静态文件中间件和 MVC 中间件）之前调用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 方法。

默认情况下，状态代码页中间件为常见状态代码（如“404 - 未找到”）添加纯文本处理程序：

```
Status Code: 404; Not Found
```

中间件支持几种可用于自定义其行为的扩展方法。

重载 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 采用 lambda 表达式，可用于处理自定义错误处理逻辑和手动编写响应：

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePages)]

重载 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 采用内容类型和格式字符串，可用于自定义内容类型和响应文本：

```csharp
app.UseStatusCodePages("text/plain", "Status code page, status code: {0}");
```

### <a name="redirect-and-re-execute-extension-methods"></a>重定向和重新执行扩展方法

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>：

* 向客户端发送“302 - 已找到”状态代码。
* 将客户端重定向到 URL 模板中的位置。

[!code-csharp[](error-handling/samples/2.x/ErrorHandlingSample/Startup.cs?name=snippet_StatusCodePagesWithRedirect)]

通常，应用在以下情况下使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithRedirects*>：

* 应将客户端重定向到不同的终结点（通常在不同的应用处理错误的情况下）。 对于 Web 应用，客户端的浏览器地址栏反映重定向终结点。
* 不应保留原始状态代码并通过初始重定向响应返回该代码。

<xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>：

* 向客户端返回原始状态代码。
* 通过使用备用路径重新执行请求管道，从而生成响应正文。

```csharp
app.UseStatusCodePagesWithReExecute("/Error/{0}");
```

应用在以下情况下通常使用 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePagesWithReExecute*>：

* 处理请求，但不重定向到不同终结点。 对于 Web 应用，客户端的浏览器地址栏反映最初请求的终结点。
* 保留原始状态代码并通过响应返回该代码。

模板可能包括状态代码的占位符 (`{0}`)。 模板必须以正斜杠 (`/`) 开头。 使用占位符时，请确认终结点（页或控制器）可以处理路径段。 例如，错误的 Razor Page 应通过 `@page` 指令接受可选路径段值：

```cshtml
@page "{code?}"
```

可禁用 Razor 页处理程序方法或 MVC 控制器中的特定请求的状态代码页。 要禁用状态代码页，请尝试从请求的 [HttpContext.Features](xref:Microsoft.AspNetCore.Http.HttpContext.Features) 集合中检索 <xref:Microsoft.AspNetCore.Diagnostics.IStatusCodePagesFeature>，并在功能可用时禁用该功能：

```csharp
var statusCodePagesFeature = HttpContext.Features.Get<IStatusCodePagesFeature>();

if (statusCodePagesFeature != null)
{
    statusCodePagesFeature.Enabled = false;
}
```

若要在应用中使用指向终结点的 <xref:Microsoft.AspNetCore.Builder.StatusCodePagesExtensions.UseStatusCodePages*> 重载，请为终结点创建 MVC 视图或 Razor Page。 例如，Razor Pages 应用模板将生成以下页和页模型类：

Error.cshtml：

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

Error.cshtml.cs：

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

Error.cshtml.cs：

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

## <a name="exception-handling-code"></a>异常处理代码

异常处理页中的代码可能会引发异常。 建议在生产错误页面中包含纯静态内容。

此外，请注意，发送响应标头后：

* 应用无法更改响应的状态代码。
* 任何异常页或处理程序都无法运行。 必须完成响应或中止连接。

## <a name="server-exception-handling"></a>服务器异常处理

除应用中的异常处理逻辑外，[服务器实现](xref:fundamentals/servers/index)也可以处理一些异常。 如果服务器在发送响应标头之前捕获到异常，服务器将发送不包含响应正文的“500 - 内部服务器错误”响应。 如果服务器在发送响应标头后捕获到异常，服务器会关闭连接。 应用程序无法处理的请求将由服务器进行处理。 发生的任何异常都将由服务器进行处理。 任何配置的自定义错误页面或异常处理中间件或筛选器都不会影响此行为。

## <a name="startup-exception-handling"></a>启动异常处理

应用程序启动期间发生的异常仅可在承载层进行处理。 借助 [Web 主机](xref:fundamentals/host/web-host)，可以使用 `captureStartupErrors` 和 `detailedErrors` 键[配置主机在响应启动期间出现错误时的行为方式](xref:fundamentals/host/web-host#detailed-errors)。

仅当捕获的启动错误发生在主机地址/端口绑定之后，承载层才会为该错误显示错误页。 如果出于任何原因导致任何绑定失败：

* 托管层将记录关键异常。
* dotnet 进程崩溃。
* 当应用在 [Kestrel](xref:fundamentals/servers/kestrel) 服务器上运行时，不会显示错误页。

在 [IIS](/iis) 或 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 上运行应用时，如果无法启动进程，[ASP.NET Core 模块](xref:host-and-deploy/aspnet-core-module)将返回“502.5 - 进程失败”。 有关更多信息，请参见<xref:host-and-deploy/iis/troubleshoot>。 要了解如何排查 Azure 应用服务的启动问题，请参阅 <xref:host-and-deploy/azure-apps/troubleshoot>。

## <a name="aspnet-core-mvc-error-handling"></a>ASP.NET Core MVC 错误处理

[MVC](xref:mvc/overview) 应用还有一些其他的错误处理选项，例如配置异常筛选器和执行模型验证。

### <a name="exception-filters"></a>异常筛选器

在 MVC 应用中，异常筛选器可以进行全局配置，也可以为每个控制器或每个操作单独配置。 这些筛选器处理在执行控制器操作或其他筛选器时出现的任何未处理的异常。 不会以其他方式调用这些筛选器。 若要了解详细信息，请参阅 <xref:mvc/controllers/filters>。

> [!TIP]
> 异常筛选器适合捕获 MVC 操作内发生的异常，但它们不如错误处理中间件灵活。 建议使用中间件。 仅在需要根据所选 MVC 操作以不同方式执行错误处理时，才使用筛选器。

### <a name="handle-model-state-errors"></a>处理模型状态错误

[模型验证](xref:mvc/models/validation)在每个控制器操作被调用之前发生，操作方法负责检查 [ModelState.IsValid](xref:Microsoft.AspNetCore.Mvc.ModelBinding.ModelStateDictionary.IsValid) 并相应地作出反应。

某些应用使用标准约定处理[模型验证](xref:mvc/models/validation)错误，在这种情况下，使用[筛选器](xref:mvc/controllers/filters)可以更好地实施这种策略。 你需要使用无效模型状态测试操作的行为。 有关更多信息，请参见<xref:mvc/controllers/testing>。

## <a name="additional-resources"></a>其他资源

* <xref:host-and-deploy/azure-iis-errors-reference>
* <xref:host-and-deploy/iis/troubleshoot>
* <xref:host-and-deploy/azure-apps/troubleshoot>
