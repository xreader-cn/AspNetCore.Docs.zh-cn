---
title: ASP.NET Core 中的 Razor Pages 的筛选方法
author: Rick-Anderson
description: 了解如何为 ASP.NET Core 中的 Razor Pages 创建筛选方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 2/18/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: razor-pages/filter
ms.openlocfilehash: 68962d5a3a49e52510d72899e7dead2c1983d8b6
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82775513"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="fe9e5-103">ASP.NET Core 中的 Razor Pages 的筛选方法</span><span class="sxs-lookup"><span data-stu-id="fe9e5-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="fe9e5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fe9e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="fe9e5-105"> 页面筛选器 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允许 Razor Pages 在运行 Razor 页面处理程序的前后运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-105"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="fe9e5-106"> 页面筛选器与 [ASP.NET Core MVC 操作筛选器](xref:mvc/controllers/filters#action-filters)类似，但它们不能应用于单个页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-106"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="fe9e5-107"> 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-107"> Page filters:</span></span>

* <span data-ttu-id="fe9e5-108">在选择处理程序方法后但在模型绑定发生前运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="fe9e5-109">在模型绑定完成后，执行处理程序方法前运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="fe9e5-110">在执行处理程序方法后运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="fe9e5-111">可在页面或全局范围内实现。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="fe9e5-112">无法应用于特定的页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-112">Cannot be applied to specific page handler methods.</span></span>
* <span data-ttu-id="fe9e5-113">可以用[依赖项注入](xref:fundamentals/dependency-injection) (DI) 填充构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-113">Can have constructor dependencies populated by [Dependency Injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="fe9e5-114">有关详细信息，请参阅 [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) 和 [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute)。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-114">For more information, see [ServiceFilterAttribute](/aspnet/core/mvc/controllers/filters#servicefilterattribute) and [TypeFilterAttribute](/aspnet/core/mvc/controllers/filters#typefilterattribute).</span></span>

<span data-ttu-id="fe9e5-115">虽然页构造函数和中间件允许在处理程序方法执行之前执行自定义代码，但只有 Razor 页面筛选器允许访问 <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> 和页面。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-115">While page constructors and middleware enable executing custom code before a handler method executes, only Razor Page filters enable access to <xref:Microsoft.AspNetCore.Mvc.RazorPages.PageModel.HttpContext> and the page.</span></span> <span data-ttu-id="fe9e5-116">中间件可以访问 `HttpContext`，但不能访问“页面上下文”。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-116">Middleware has access to the `HttpContext`, but not to the "page context".</span></span> <span data-ttu-id="fe9e5-117">筛选器具有 <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> 派生参数，该参数提供对 `HttpContext` 的访问权限。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-117">Filters have a <xref:Microsoft.AspNetCore.Mvc.Filters.FilterContext> derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="fe9e5-118">例如，[实现筛选器属性](#ifa)示例将标头添加到响应中，而使用构造函数或中间件则无法做到这点。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-118">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="fe9e5-119">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fe9e5-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/3.1sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="fe9e5-120"> 页面筛选器提供的以下方法可在全局或页面级应用：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-120"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="fe9e5-121">同步方法：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-121">Synchronous methods:</span></span>

  * <span data-ttu-id="fe9e5-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生之前调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-122">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fe9e5-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在模型绑定完成后，执行处理程序方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-123">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="fe9e5-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在执行处理器方法后，生成操作结果前调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-124">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="fe9e5-125">异步方法：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-125">Asynchronous methods:</span></span>

  * <span data-ttu-id="fe9e5-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生前，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-126">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fe9e5-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在调用处理程序方法前，但在模型绑定结束后，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-127">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

<span data-ttu-id="fe9e5-128">筛选器接口的同步和异步版本任意实现一个，而不是同时实现   。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-128">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="fe9e5-129">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-129">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="fe9e5-130">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-130">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="fe9e5-131">如果两个接口都已实现，则只会调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-131">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="fe9e5-132">对页面中的替代应用相同的规则，同步替代或异步替代只能任选其一实现，不可二者皆选。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-132">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="fe9e5-133">全局实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="fe9e5-133">Implement Razor Page filters globally</span></span>

<span data-ttu-id="fe9e5-134">以下代码实现了 `IAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-134">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="fe9e5-135">在前面的代码中，`ProcessUserAgent.Write` 是用户提供的与用户代理字符串一起使用的代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-135">In the preceding code, `ProcessUserAgent.Write` is user supplied code that works with the user agent string.</span></span>

<span data-ttu-id="fe9e5-136">以下代码启用 `Startup` 类中的 `SampleAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-136">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup.cs?name=snippet2)]

<span data-ttu-id="fe9e5-137">以下代码调用 <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*>，将 `SampleAsyncPageFilter` 仅应用于 /Movies 中的页面  ：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-137">The following code calls <xref:Microsoft.AspNetCore.Mvc.ApplicationModels.PageConventionCollection.AddFolderApplicationModelConvention*> to apply the `SampleAsyncPageFilter` to only pages in */Movies*:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="fe9e5-138">以下代码实现同步的 `IPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-138">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="fe9e5-139">以下代码启用 `SamplePageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-139">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/StartupSync.cs?name=snippet2)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="fe9e5-140">通过重写筛选器方法实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="fe9e5-140">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="fe9e5-141">以下代码替代异步 Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-141">The following code overrides the asynchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Index.cshtml.cs?name=snippet)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="fe9e5-142">实现筛选器属性</span><span class="sxs-lookup"><span data-stu-id="fe9e5-142">Implement a filter attribute</span></span>

<span data-ttu-id="fe9e5-143">基于属性的内置筛选器 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> 可以进行子类化。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-143">The built-in attribute-based filter <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter.OnResultExecutionAsync*> filter can be subclassed.</span></span> <span data-ttu-id="fe9e5-144">以下筛选器向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-144">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="fe9e5-145">以下代码应用 `AddHeader` 属性：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-145">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/3.1sample/PageFilter/Pages/Movies/Test.cshtml.cs)]

<span data-ttu-id="fe9e5-146">使用浏览器开发人员工具等工具来检查标头。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-146">Use a tool such as the browser developer tools to examine the headers.</span></span> <span data-ttu-id="fe9e5-147">在响应标头下，将显示 `author: Rick` 。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-147">Under **Response Headers**, `author: Rick` is displayed.</span></span>

<span data-ttu-id="fe9e5-148">有关重写顺序的说明，请参阅[重写默认顺序](xref:mvc/controllers/filters#overriding-the-default-order)。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-148">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="fe9e5-149">有关将筛选器管道与筛选器短路的说明，请参阅[取消和设置短路](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-149">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span>

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="fe9e5-150">授权筛选器属性</span><span class="sxs-lookup"><span data-stu-id="fe9e5-150">Authorize filter attribute</span></span>

<span data-ttu-id="fe9e5-151">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性可应用于 `PageModel`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-151">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="fe9e5-152">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="fe9e5-152">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

Razor<span data-ttu-id="fe9e5-153"> 页面筛选器 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允许 Razor Pages 在运行 Razor 页面处理程序的前后运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-153"> Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> Razor<span data-ttu-id="fe9e5-154"> 页面筛选器与 [ASP.NET Core MVC 操作筛选器](xref:mvc/controllers/filters#action-filters)类似，但它们不能应用于单个页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-154"> Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span>

Razor<span data-ttu-id="fe9e5-155"> 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-155"> Page filters:</span></span>

* <span data-ttu-id="fe9e5-156">在选择处理程序方法后但在模型绑定发生前运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-156">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="fe9e5-157">在模型绑定完成后，执行处理程序方法前运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-157">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="fe9e5-158">在执行处理程序方法后运行代码。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-158">Run code after the handler method executes.</span></span>
* <span data-ttu-id="fe9e5-159">可在页面或全局范围内实现。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-159">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="fe9e5-160">无法应用于特定的页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-160">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="fe9e5-161">代码可在使用页面构造函数或中间件执行处理程序方法前运行，但仅 Razor 页面筛选器可访问 [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-161">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="fe9e5-162">筛选器具有 [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 派生参数，可用于访问 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-162">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="fe9e5-163">例如，[实现筛选器属性](#ifa)示例将标头添加到响应中，而使用构造函数或中间件则无法做到这点。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-163">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="fe9e5-164">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="fe9e5-164">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

Razor<span data-ttu-id="fe9e5-165"> 页面筛选器提供的以下方法可在全局或页面级应用：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-165"> Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="fe9e5-166">同步方法：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-166">Synchronous methods:</span></span>

  * <span data-ttu-id="fe9e5-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生之前调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-167">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fe9e5-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在模型绑定完成后，执行处理程序方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-168">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="fe9e5-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在执行处理器方法后，生成操作结果前调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-169">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="fe9e5-170">异步方法：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-170">Asynchronous methods:</span></span>

  * <span data-ttu-id="fe9e5-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生前，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-171">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="fe9e5-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在调用处理程序方法前，但在模型绑定结束后，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-172">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="fe9e5-173">筛选器接口的同步和异步版本**任意**实现一个，而不是同时实现。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-173">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="fe9e5-174">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-174">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="fe9e5-175">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-175">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="fe9e5-176">如果两个接口都已实现，则只会调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-176">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="fe9e5-177">对页面中的替代应用相同的规则，同步替代或异步替代只能任选其一实现，不可二者皆选。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-177">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="fe9e5-178">全局实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="fe9e5-178">Implement Razor Page filters globally</span></span>

<span data-ttu-id="fe9e5-179">以下代码实现了 `IAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-179">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="fe9e5-180">在前面的代码中，[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) 不是必需的。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-180">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="fe9e5-181">它在示例中用于提供应用程序的跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-181">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="fe9e5-182">以下代码启用 `Startup` 类中的 `SampleAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-182">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="fe9e5-183">以下代码显示完整的 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-183">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="fe9e5-184">以下代码调用 `AddFolderApplicationModelConvention` 将 `SampleAsyncPageFilter` 仅应用于 /subFolder 中的页面  ：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-184">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="fe9e5-185">以下代码实现同步的 `IPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-185">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="fe9e5-186">以下代码启用 `SamplePageFilter`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-186">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="fe9e5-187">通过重写筛选器方法实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="fe9e5-187">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="fe9e5-188">以下代码替代同步 Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-188">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="fe9e5-189">实现筛选器属性</span><span class="sxs-lookup"><span data-stu-id="fe9e5-189">Implement a filter attribute</span></span>

<span data-ttu-id="fe9e5-190">基于属性的内置筛选器 [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) 可以进行子类化。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-190">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="fe9e5-191">以下筛选器向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-191">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="fe9e5-192">以下代码应用 `AddHeader` 属性：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-192">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="fe9e5-193">有关重写顺序的说明，请参阅[重写默认顺序](xref:mvc/controllers/filters#overriding-the-default-order)。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-193">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="fe9e5-194">有关将筛选器管道与筛选器短路的说明，请参阅[取消和设置短路](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。</span><span class="sxs-lookup"><span data-stu-id="fe9e5-194">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="fe9e5-195">授权筛选器属性</span><span class="sxs-lookup"><span data-stu-id="fe9e5-195">Authorize filter attribute</span></span>

<span data-ttu-id="fe9e5-196">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性可应用于 `PageModel`：</span><span class="sxs-lookup"><span data-stu-id="fe9e5-196">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]

::: moniker-end
