---
title: ASP.NET Core 中的 Razor 页面的筛选方法
author: Rick-Anderson
description: 了解如何为 ASP.NET Core 中的 Razor 页面创建筛选方法。
monikerRange: '>= aspnetcore-2.1'
ms.author: riande
ms.date: 04/05/2018
uid: razor-pages/filter
ms.openlocfilehash: 1da46c61617a01698e3c4b1fe6bf9825db6643fd
ms.sourcegitcommit: a166291c6708f5949c417874108332856b53b6a9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/18/2019
ms.locfileid: "72589953"
---
# <a name="filter-methods-for-razor-pages-in-aspnet-core"></a><span data-ttu-id="5a1db-103">ASP.NET Core 中的 Razor 页面的筛选方法</span><span class="sxs-lookup"><span data-stu-id="5a1db-103">Filter methods for Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="5a1db-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a1db-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5a1db-105">Razor 页面筛选器 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) 允许 Razor 页面在运行 Razor 页面处理程序的前后运行代码。</span><span class="sxs-lookup"><span data-stu-id="5a1db-105">Razor Page filters [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) allow Razor Pages to run code before and after a Razor Page handler is run.</span></span> <span data-ttu-id="5a1db-106">Razor 页面筛选器与 [ASP.NET Core MVC 操作筛选器](xref:mvc/controllers/filters#action-filters)类似，但它们不能应用于单个页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="5a1db-106">Razor Page filters are similar to [ASP.NET Core MVC action filters](xref:mvc/controllers/filters#action-filters), except they can't be applied to individual page handler methods.</span></span> 

<span data-ttu-id="5a1db-107">Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="5a1db-107">Razor Page filters:</span></span>

* <span data-ttu-id="5a1db-108">在选择处理程序方法后但在模型绑定发生前运行代码。</span><span class="sxs-lookup"><span data-stu-id="5a1db-108">Run code after a handler method has been selected, but before model binding occurs.</span></span>
* <span data-ttu-id="5a1db-109">在模型绑定完成后，执行处理程序方法前运行代码。</span><span class="sxs-lookup"><span data-stu-id="5a1db-109">Run code before the handler method executes, after model binding is complete.</span></span>
* <span data-ttu-id="5a1db-110">在执行处理程序方法后运行代码。</span><span class="sxs-lookup"><span data-stu-id="5a1db-110">Run code after the handler method executes.</span></span>
* <span data-ttu-id="5a1db-111">可在页面或全局范围内实现。</span><span class="sxs-lookup"><span data-stu-id="5a1db-111">Can be implemented on a page or globally.</span></span>
* <span data-ttu-id="5a1db-112">无法应用于特定的页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="5a1db-112">Cannot be applied to specific page handler methods.</span></span>

<span data-ttu-id="5a1db-113">代码可在使用页面构造函数或中间件执行处理程序方法前运行，但仅 Razor 页面筛选器可访问 [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext)。</span><span class="sxs-lookup"><span data-stu-id="5a1db-113">Code can be run before a handler method executes using the page constructor or middleware, but only Razor Page filters have access to [HttpContext](/dotnet/api/microsoft.aspnetcore.mvc.razorpages.pagemodel.httpcontext?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_RazorPages_PageModel_HttpContext).</span></span> <span data-ttu-id="5a1db-114">筛选器具有 [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) 派生参数，可用于访问 `HttpContext`。</span><span class="sxs-lookup"><span data-stu-id="5a1db-114">Filters have a [FilterContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.filtercontext?view=aspnetcore-2.0) derived parameter, which provides access to `HttpContext`.</span></span> <span data-ttu-id="5a1db-115">例如，[实现筛选器属性](#ifa)示例将标头添加到响应中，而使用构造函数或中间件则无法做到这点。</span><span class="sxs-lookup"><span data-stu-id="5a1db-115">For example, the [Implement a filter attribute](#ifa) sample adds a header to the response, something that can't be done with constructors or middleware.</span></span>

<span data-ttu-id="5a1db-116">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="5a1db-116">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/razor-pages/filter/sample/PageFilter) ([how to download](xref:index#how-to-download-a-sample))</span></span>

<span data-ttu-id="5a1db-117">Razor 页面筛选器提供的以下方法可在全局或页面级应用：</span><span class="sxs-lookup"><span data-stu-id="5a1db-117">Razor Page filters provide the following methods, which can be applied globally or at the page level:</span></span>

* <span data-ttu-id="5a1db-118">同步方法：</span><span class="sxs-lookup"><span data-stu-id="5a1db-118">Synchronous methods:</span></span>

  * <span data-ttu-id="5a1db-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0)：在选择处理程序方法后但在模型绑定发生前调用。</span><span class="sxs-lookup"><span data-stu-id="5a1db-119">[OnPageHandlerSelected](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerselected?view=aspnetcore-2.0) : Called after a handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5a1db-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0)：在执行处理器方法前，模型绑定完成后调用。</span><span class="sxs-lookup"><span data-stu-id="5a1db-120">[OnPageHandlerExecuting](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuting?view=aspnetcore-2.0) : Called before the handler method executes, after model binding is complete.</span></span>
  * <span data-ttu-id="5a1db-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0)：在执行处理器方法后，生成操作结果前调用。</span><span class="sxs-lookup"><span data-stu-id="5a1db-121">[OnPageHandlerExecuted](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter.onpagehandlerexecuted?view=aspnetcore-2.0) : Called after the handler method executes, before the action result.</span></span>

* <span data-ttu-id="5a1db-122">异步方法：</span><span class="sxs-lookup"><span data-stu-id="5a1db-122">Asynchronous methods:</span></span>

  * <span data-ttu-id="5a1db-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0)：在选择处理程序方法后，但在模型绑定发生前，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="5a1db-123">[OnPageHandlerSelectionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerselectionasync?view=aspnetcore-2.0) : Called asynchronously after the handler method has been selected, but before model binding occurs.</span></span>
  * <span data-ttu-id="5a1db-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0)：在调用处理程序方法前，但在模型绑定结束后，进行异步调用。</span><span class="sxs-lookup"><span data-stu-id="5a1db-124">[OnPageHandlerExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter.onpagehandlerexecutionasync?view=aspnetcore-2.0) : Called asynchronously before the handler method is invoked, after model binding is complete.</span></span>

> [!NOTE]
> <span data-ttu-id="5a1db-125">筛选器接口的同步和异步版本**任意**实现一个，而不是同时实现。</span><span class="sxs-lookup"><span data-stu-id="5a1db-125">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="5a1db-126">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="5a1db-126">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="5a1db-127">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="5a1db-127">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="5a1db-128">如果实现了这两个接口，则只调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="5a1db-128">If both interfaces are implemented, only the async methods are called.</span></span> <span data-ttu-id="5a1db-129">对页面中的替代应用相同的规则，同步替代或异步替代只能任选其一实现，不可二者皆选。</span><span class="sxs-lookup"><span data-stu-id="5a1db-129">The same rule applies to overrides in pages, implement the synchronous or the async version of the override, not both.</span></span>

## <a name="implement-razor-page-filters-globally"></a><span data-ttu-id="5a1db-130">全局实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="5a1db-130">Implement Razor Page filters globally</span></span>

<span data-ttu-id="5a1db-131">以下代码实现了 `IAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="5a1db-131">The following code implements `IAsyncPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SampleAsyncPageFilter.cs?name=snippet1)]

<span data-ttu-id="5a1db-132">在前面的代码中，[ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) 不是必需的。</span><span class="sxs-lookup"><span data-stu-id="5a1db-132">In the preceding code, [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger?view=aspnetcore-2.0) is not required.</span></span> <span data-ttu-id="5a1db-133">它在示例中用于提供应用程序的跟踪信息。</span><span class="sxs-lookup"><span data-stu-id="5a1db-133">It's used in the sample to provide trace information for the application.</span></span>

<span data-ttu-id="5a1db-134">以下代码启用 `Startup` 类中的 `SampleAsyncPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="5a1db-134">The following code enables the `SampleAsyncPageFilter` in the `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet2&highlight=11)]

<span data-ttu-id="5a1db-135">以下代码显示完整的 `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="5a1db-135">The following code shows the complete `Startup` class:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup.cs?name=snippet1)]

<span data-ttu-id="5a1db-136">以下代码调用 `AddFolderApplicationModelConvention` 将 `SampleAsyncPageFilter` 仅应用于 /subFolder 中的页面：</span><span class="sxs-lookup"><span data-stu-id="5a1db-136">The following code calls `AddFolderApplicationModelConvention` to apply the `SampleAsyncPageFilter` to only pages in */subFolder*:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Startup2.cs?name=snippet2)]

<span data-ttu-id="5a1db-137">以下代码实现同步的 `IPageFilter`：</span><span class="sxs-lookup"><span data-stu-id="5a1db-137">The following code implements the synchronous `IPageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/SamplePageFilter.cs?name=snippet1)]

<span data-ttu-id="5a1db-138">以下代码启用 `SamplePageFilter`：</span><span class="sxs-lookup"><span data-stu-id="5a1db-138">The following code enables the `SamplePageFilter`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/StartupSync.cs?name=snippet2&highlight=11)]

::: moniker range=">= aspnetcore-2.1"

## <a name="implement-razor-page-filters-by-overriding-filter-methods"></a><span data-ttu-id="5a1db-139">通过重写筛选器方法实现 Razor 页面筛选器</span><span class="sxs-lookup"><span data-stu-id="5a1db-139">Implement Razor Page filters by overriding filter methods</span></span>

<span data-ttu-id="5a1db-140">以下代码替代同步的 Razor 页面筛选器：</span><span class="sxs-lookup"><span data-stu-id="5a1db-140">The following code overrides the synchronous Razor Page filters:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Index.cshtml.cs)]

::: moniker-end

<a name="ifa"></a>

## <a name="implement-a-filter-attribute"></a><span data-ttu-id="5a1db-141">实现筛选器属性</span><span class="sxs-lookup"><span data-stu-id="5a1db-141">Implement a filter attribute</span></span>

<span data-ttu-id="5a1db-142">基于属性的内置筛选器 [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) 可以进行子类化。</span><span class="sxs-lookup"><span data-stu-id="5a1db-142">The built-in attribute-based filter [OnResultExecutionAsync](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncresultfilter.onresultexecutionasync?view=aspnetcore-2.0#Microsoft_AspNetCore_Mvc_Filters_IAsyncResultFilter_OnResultExecutionAsync_Microsoft_AspNetCore_Mvc_Filters_ResultExecutingContext_Microsoft_AspNetCore_Mvc_Filters_ResultExecutionDelegate_) filter can be subclassed.</span></span> <span data-ttu-id="5a1db-143">以下筛选器向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="5a1db-143">The following filter adds a header to the response:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Filters/AddHeaderAttribute.cs)]

<span data-ttu-id="5a1db-144">以下代码应用 `AddHeader` 属性：</span><span class="sxs-lookup"><span data-stu-id="5a1db-144">The following code applies the `AddHeader` attribute:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/Contact.cshtml.cs?name=snippet1)]

<span data-ttu-id="5a1db-145">有关重写顺序的说明，请参阅[重写默认顺序](xref:mvc/controllers/filters#overriding-the-default-order)。</span><span class="sxs-lookup"><span data-stu-id="5a1db-145">See [Overriding the default order](xref:mvc/controllers/filters#overriding-the-default-order) for instructions on overriding the order.</span></span>

<span data-ttu-id="5a1db-146">有关将筛选器管道与筛选器短路的说明，请参阅[取消和设置短路](xref:mvc/controllers/filters#cancellation-and-short-circuiting)。</span><span class="sxs-lookup"><span data-stu-id="5a1db-146">See [Cancellation and short circuiting](xref:mvc/controllers/filters#cancellation-and-short-circuiting) for instructions to short-circuit the filter pipeline from a filter.</span></span> 

<a name="auth"></a>

## <a name="authorize-filter-attribute"></a><span data-ttu-id="5a1db-147">授权筛选器属性</span><span class="sxs-lookup"><span data-stu-id="5a1db-147">Authorize filter attribute</span></span>

<span data-ttu-id="5a1db-148">[Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) 属性可应用于 `PageModel`：</span><span class="sxs-lookup"><span data-stu-id="5a1db-148">The [Authorize](/dotnet/api/microsoft.aspnetcore.authorization.authorizeattribute?view=aspnetcore-2.0) attribute can be applied to a `PageModel`:</span></span>

[!code-csharp[Main](filter/sample/PageFilter/Pages/ModelWithAuthFilter.cshtml.cs?highlight=7)]
