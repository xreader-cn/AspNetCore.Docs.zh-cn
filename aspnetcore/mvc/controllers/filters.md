---
title: ASP.NET Core 中的筛选器
author: Rick-Anderson
description: 了解筛选器的工作原理以及如何在 ASP.NET Core 中使用它们。
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
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
uid: mvc/controllers/filters
ms.openlocfilehash: 416fc292d82cf841b2134e23a8e494e3e8d945ca
ms.sourcegitcommit: f77a7467651bab61b24261da9dc5c1dd75fc1fa9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563978"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="d929a-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="d929a-104">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d929a-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d929a-105">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="d929a-106">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="d929a-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="d929a-107">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="d929a-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="d929a-108">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="d929a-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="d929a-109">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="d929a-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="d929a-110">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="d929a-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="d929a-111">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="d929a-112">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="d929a-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="d929a-113">本文档适用于 Razor 具有视图的页面、API 控制器和控制器。</span><span class="sxs-lookup"><span data-stu-id="d929a-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="d929a-114">筛选器不直接与[ Razor 组件](xref:blazor/components/index)一起使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="d929a-115">筛选器只能在以下情况下间接影响组件：</span><span class="sxs-lookup"><span data-stu-id="d929a-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="d929a-116">该组件嵌入在页面或视图中。</span><span class="sxs-lookup"><span data-stu-id="d929a-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="d929a-117">页面或控制器/视图使用此筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="d929a-118">[查看或下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="d929a-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="d929a-119">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="d929a-119">How filters work</span></span>

<span data-ttu-id="d929a-120">筛选器在 ASP.NET Core 操作调用管道（有时称为筛选器管道）内运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="d929a-121">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="d929a-124">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="d929a-124">Filter types</span></span>

<span data-ttu-id="d929a-125">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="d929a-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="d929a-126">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="d929a-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="d929a-127">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="d929a-128">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="d929a-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="d929a-129">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-129">Run after authorization.</span></span>  
  * <span data-ttu-id="d929a-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="d929a-131">例如，`OnResourceExecuting` 在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="d929a-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="d929a-133">[操作筛选器](#action-filters)：</span><span class="sxs-lookup"><span data-stu-id="d929a-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="d929a-134">在调用操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="d929a-135">可以更改传递到操作中的参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="d929a-136">可以更改从操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="d929a-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="d929a-137">页面 **不** 支持 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d929a-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="d929a-138">[异常筛选器](#exception-filters)在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="d929a-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="d929a-139">[结果筛选器](#result-filters)在执行操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="d929a-140">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="d929a-141">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="d929a-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="d929a-142">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="d929a-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="d929a-145">实现</span><span class="sxs-lookup"><span data-stu-id="d929a-145">Implementation</span></span>

<span data-ttu-id="d929a-146">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="d929a-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="d929a-147">同步筛选器在其管道阶段之前和之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="d929a-148">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="d929a-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d929a-150">在上面的代码中， [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) 是 [示例下载](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs)中的实用工具函数。</span><span class="sxs-lookup"><span data-stu-id="d929a-150">In the preceding code, [MyDebug](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs) is a utility function in the [sample download](https://github.com/dotnet/AspNetCore.Docs/blob/master/aspnetcore/mvc/controllers/filters/3.1sample/FiltersSample/Helper/MyDebug.cs).</span></span>

<span data-ttu-id="d929a-151">异步筛选器定义 `On-Stage-ExecutionAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-151">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="d929a-152">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>：</span><span class="sxs-lookup"><span data-stu-id="d929a-152">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="d929a-153">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="d929a-153">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="d929a-154">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="d929a-154">Multiple filter stages</span></span>

<span data-ttu-id="d929a-155">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-155">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="d929a-156">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类可实现：</span><span class="sxs-lookup"><span data-stu-id="d929a-156">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="d929a-157">同步：<xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="d929a-157">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="d929a-158">异步：<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d929a-158">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="d929a-159">筛选器接口的同步和异步版本任意实现一个，而不是同时实现 。</span><span class="sxs-lookup"><span data-stu-id="d929a-159">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d929a-160">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-160">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d929a-161">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-161">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d929a-162">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-162">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="d929a-163">当使用抽象类（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> ）时，仅重写每个筛选器类型的同步方法或异步方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-163">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous methods for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="d929a-164">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="d929a-164">Built-in filter attributes</span></span>

<span data-ttu-id="d929a-165">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-165">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="d929a-166">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="d929a-166">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="d929a-167">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="d929a-167">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="d929a-168">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="d929a-168">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="d929a-169">使用 [浏览器开发人员工具](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) 等工具来检查标头。</span><span class="sxs-lookup"><span data-stu-id="d929a-169">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="d929a-170">在响应标头下，将显示 `author: Rick Anderson`。</span><span class="sxs-lookup"><span data-stu-id="d929a-170">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="d929a-171">以下代码实现了 `ActionFilterAttribute`：</span><span class="sxs-lookup"><span data-stu-id="d929a-171">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="d929a-172">从配置系统读取标题和名称。</span><span class="sxs-lookup"><span data-stu-id="d929a-172">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="d929a-173">与前面的示例不同，以下代码不需要将筛选器参数添加到代码中。</span><span class="sxs-lookup"><span data-stu-id="d929a-173">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="d929a-174">将标题和名称添加到响应标头。</span><span class="sxs-lookup"><span data-stu-id="d929a-174">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d929a-175">使用[选项模式](xref:fundamentals/configuration/options)从[配置系统](xref:fundamentals/configuration/index)中提供配置选项。</span><span class="sxs-lookup"><span data-stu-id="d929a-175">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="d929a-176">例如，从 *appsettings.json* 文件：</span><span class="sxs-lookup"><span data-stu-id="d929a-176">For example, from the *appsettings.json* file:</span></span>

[!code-json[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="d929a-177">在 `StartUp.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="d929a-177">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="d929a-178">`PositionOptions` 类已通过 `"Position"` 配置区域添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="d929a-178">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="d929a-179">`MyActionFilterAttribute` 已添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="d929a-179">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="d929a-180">以下代码显示 `PositionOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="d929a-180">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="d929a-181">以下代码将 `MyActionFilterAttribute` 应用于 `Index2` 方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-181">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="d929a-182">在 `author: Rick Anderson` `Editor: Joe Smith` 调用终结点时，将显示 "响应标头"、和 `Sample/Index2` 。</span><span class="sxs-lookup"><span data-stu-id="d929a-182">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="d929a-183">下面的代码将 `MyActionFilterAttribute` 和应用于 `AddHeaderAttribute` Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="d929a-183">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="d929a-184">筛选器不能应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-184">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="d929a-185">它们可以应用于 Razor 页面模型或全局应用。</span><span class="sxs-lookup"><span data-stu-id="d929a-185">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="d929a-186">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="d929a-186">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="d929a-187">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-187">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="d929a-188">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="d929a-188">Filter scopes and order of execution</span></span>

<span data-ttu-id="d929a-189">可以将筛选器添加到管道中的以下三个 *范围* 之一：</span><span class="sxs-lookup"><span data-stu-id="d929a-189">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="d929a-190">在控制器操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-190">Using an attribute on a controller action.</span></span> <span data-ttu-id="d929a-191">筛选器属性不能应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-191">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="d929a-192">在控制器或页上使用特性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d929a-192">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="d929a-193">针对所有控制器、操作和页面全局 Razor 显示，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="d929a-193">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="d929a-194">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="d929a-194">Default order of execution</span></span>

<span data-ttu-id="d929a-195">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="d929a-195">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="d929a-196">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-196">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="d929a-197">在筛选器嵌套模式下，筛选器的 after 代码会按照与 before 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-197">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="d929a-198">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="d929a-198">The filter sequence:</span></span>

* <span data-ttu-id="d929a-199">全局筛选器的 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-199">The *before* code of global filters.</span></span>
  * <span data-ttu-id="d929a-200">控制器和 Razor 页面筛选器的前代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-200">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="d929a-201">操作方法筛选器的 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-201">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="d929a-202">操作方法筛选器的 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-202">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="d929a-203">控制器和 Razor 页面筛选器后的代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-203">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="d929a-204">全局筛选器的 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-204">The *after* code of global filters.</span></span>
  
<span data-ttu-id="d929a-205">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="d929a-205">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="d929a-206">序列</span><span class="sxs-lookup"><span data-stu-id="d929a-206">Sequence</span></span> | <span data-ttu-id="d929a-207">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="d929a-207">Filter scope</span></span> | <span data-ttu-id="d929a-208">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="d929a-208">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="d929a-209">1</span><span class="sxs-lookup"><span data-stu-id="d929a-209">1</span></span> | <span data-ttu-id="d929a-210">全球</span><span class="sxs-lookup"><span data-stu-id="d929a-210">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d929a-211">2</span><span class="sxs-lookup"><span data-stu-id="d929a-211">2</span></span> | <span data-ttu-id="d929a-212">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="d929a-212">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="d929a-213">3</span><span class="sxs-lookup"><span data-stu-id="d929a-213">3</span></span> | <span data-ttu-id="d929a-214">方法</span><span class="sxs-lookup"><span data-stu-id="d929a-214">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d929a-215">4</span><span class="sxs-lookup"><span data-stu-id="d929a-215">4</span></span> | <span data-ttu-id="d929a-216">方法</span><span class="sxs-lookup"><span data-stu-id="d929a-216">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d929a-217">5</span><span class="sxs-lookup"><span data-stu-id="d929a-217">5</span></span> | <span data-ttu-id="d929a-218">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="d929a-218">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d929a-219">6</span><span class="sxs-lookup"><span data-stu-id="d929a-219">6</span></span> | <span data-ttu-id="d929a-220">全球</span><span class="sxs-lookup"><span data-stu-id="d929a-220">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="d929a-221">控制器级别筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-221">Controller level filters</span></span>

<span data-ttu-id="d929a-222">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-222">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="d929a-223">这些方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-223">These methods:</span></span>

* <span data-ttu-id="d929a-224">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-224">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="d929a-225">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-225">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="d929a-226">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-226">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="d929a-227">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-227">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="d929a-228">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-228">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="d929a-229">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="d929a-229">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="d929a-230">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="d929a-230">The `TestController`:</span></span>

* <span data-ttu-id="d929a-231">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="d929a-231">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="d929a-232">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="d929a-232">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

[!INCLUDE[](~/includes/MyDisplayRouteInfo.md)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="d929a-233">导航到 `https://localhost:5001/Test/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="d929a-233">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="d929a-234">控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。</span><span class="sxs-lookup"><span data-stu-id="d929a-234">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="d929a-235">控制器级别筛选器无法设置为在将筛选器应用于方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-235">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="d929a-236">在下一节对 Order 进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="d929a-236">Order is explained in the next section.</span></span>

<span data-ttu-id="d929a-237">有关 Razor 页面，请 [参阅 Razor 通过重写筛选器方法实现页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="d929a-237">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="d929a-238">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="d929a-238">Overriding the default order</span></span>

<span data-ttu-id="d929a-239">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="d929a-239">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="d929a-240">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="d929a-240">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="d929a-241">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-241">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="d929a-242">在具有较高的 `Order` 值的筛选器之前运行 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-242">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="d929a-243">在具有较高的 `Order` 值的筛选器之后运行 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-243">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="d929a-244">使用构造函数参数设置了 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-244">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="d929a-245">请考虑以下控制器中的两个操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-245">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="d929a-246">在 `StartUp.ConfigureServices` 中添加了全局筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-246">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="d929a-247">3 个筛选器按下列顺序运行：</span><span class="sxs-lookup"><span data-stu-id="d929a-247">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MyAction2FilterAttribute.OnResultExecuting`
  * `MySampleActionFilter.OnActionExecuted`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="d929a-248">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="d929a-248">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="d929a-249">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="d929a-249">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="d929a-250">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="d929a-250">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="d929a-251">如前所述，控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="d929a-251">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="d929a-252">在前面的代码中，`MySampleActionFilter` 具有全局作用域，因此它在具有控制器作用域的 `MyAction2FilterAttribute` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-252">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="d929a-253">若要首先运行 `MyAction2FilterAttribute`，请将顺序设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="d929a-253">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="d929a-254">若要首先运行全局筛选器 `MySampleActionFilter`，请将 `Order` 设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="d929a-254">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="d929a-255">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="d929a-255">Cancellation and short-circuiting</span></span>

<span data-ttu-id="d929a-256">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-256">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="d929a-257">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="d929a-257">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d929a-258">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="d929a-258">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="d929a-259">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="d929a-259">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="d929a-260">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-260">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="d929a-261">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="d929a-261">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="d929a-262">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-262">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="d929a-263">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="d929a-263">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="d929a-264">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-264">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=1,15)]

## <a name="dependency-injection"></a><span data-ttu-id="d929a-265">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="d929a-265">Dependency injection</span></span>

<span data-ttu-id="d929a-266">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-266">Filters can be added by type or by instance.</span></span> <span data-ttu-id="d929a-267">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="d929a-267">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="d929a-268">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-268">If a type is added, it's type-activated.</span></span> <span data-ttu-id="d929a-269">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="d929a-269">A type-activated filter means:</span></span>

* <span data-ttu-id="d929a-270">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-270">An instance is created for each request.</span></span>
* <span data-ttu-id="d929a-271">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-271">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d929a-272">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-272">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d929a-273">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="d929a-273">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="d929a-274">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-274">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="d929a-275">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="d929a-275">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="d929a-276">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="d929a-276">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="d929a-277">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="d929a-277"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="d929a-278">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-278">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="d929a-279">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="d929a-279">Loggers are available from DI.</span></span> <span data-ttu-id="d929a-280">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="d929a-280">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="d929a-281">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="d929a-281">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="d929a-282">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="d929a-282">Logging added to filters:</span></span>

* <span data-ttu-id="d929a-283">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="d929a-283">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="d929a-284">不应记录操作或其他框架事件。</span><span class="sxs-lookup"><span data-stu-id="d929a-284">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="d929a-285">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="d929a-285">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="d929a-286">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d929a-286">ServiceFilterAttribute</span></span>

<span data-ttu-id="d929a-287">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-287">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="d929a-288"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-288">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="d929a-289">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="d929a-289">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d929a-290">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="d929a-290">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="d929a-291">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="d929a-291">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="d929a-292">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="d929a-292">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="d929a-293">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="d929a-293">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d929a-294">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="d929a-294">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="d929a-295">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-295">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="d929a-296">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-296">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="d929a-297">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-297">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="d929a-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="d929a-298"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d929a-299">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-299">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d929a-300">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-300">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="d929a-301">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d929a-301">TypeFilterAttribute</span></span>

<span data-ttu-id="d929a-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-302"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="d929a-303">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="d929a-303">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="d929a-304">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="d929a-304">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="d929a-305">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="d929a-305">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="d929a-306">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-306">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="d929a-307">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-307">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="d929a-308">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="d929a-308">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="d929a-309">提供提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="d929a-309">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d929a-310">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-310">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="d929a-311">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-311">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d929a-312">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="d929a-312">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="d929a-313">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-313">Authorization filters</span></span>

<span data-ttu-id="d929a-314">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-314">Authorization filters:</span></span>

* <span data-ttu-id="d929a-315">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-315">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="d929a-316">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="d929a-316">Control access to action methods.</span></span>
* <span data-ttu-id="d929a-317">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-317">Have a before method, but no after method.</span></span>

<span data-ttu-id="d929a-318">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="d929a-318">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="d929a-319">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-319">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="d929a-320">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-320">The built-in authorization filter:</span></span>

* <span data-ttu-id="d929a-321">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="d929a-321">Calls the authorization system.</span></span>
* <span data-ttu-id="d929a-322">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="d929a-322">Does not authorize requests.</span></span>

<span data-ttu-id="d929a-323">不会在授权筛选器中引发异常：</span><span class="sxs-lookup"><span data-stu-id="d929a-323">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="d929a-324">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-324">The exception will not be handled.</span></span>
* <span data-ttu-id="d929a-325">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-325">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="d929a-326">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="d929a-326">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="d929a-327">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="d929a-327">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="d929a-328">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-328">Resource filters</span></span>

<span data-ttu-id="d929a-329">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-329">Resource filters:</span></span>

* <span data-ttu-id="d929a-330">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-330">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="d929a-331">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="d929a-331">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="d929a-332">只有 [授权筛选器](#authorization-filters) 才会在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-332">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="d929a-333">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="d929a-333">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="d929a-334">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="d929a-334">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="d929a-335">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="d929a-335">Resource filter examples:</span></span>

* <span data-ttu-id="d929a-336">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="d929a-336">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="d929a-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="d929a-337">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/master/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="d929a-338">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="d929a-338">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="d929a-339">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="d929a-339">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="d929a-340">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-340">Action filters</span></span>

<span data-ttu-id="d929a-341">操作筛选器 **不适用于** Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="d929a-341">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="d929a-342">Razor 页面支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="d929a-342">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="d929a-343">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="d929a-343">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="d929a-344">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-344">Action filters:</span></span>

* <span data-ttu-id="d929a-345">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-345">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="d929a-346">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-346">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="d929a-347">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-347">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d929a-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-348">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="d929a-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="d929a-349"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="d929a-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-350"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="d929a-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-351"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="d929a-352">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="d929a-352">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="d929a-353">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-353">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="d929a-354">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="d929a-354">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d929a-355"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-355">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="d929a-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="d929a-356"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d929a-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="d929a-357"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="d929a-358">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="d929a-358">Setting this property to null:</span></span>

  * <span data-ttu-id="d929a-359">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-359">Effectively handles the exception.</span></span>
  * <span data-ttu-id="d929a-360">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="d929a-360">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="d929a-361">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="d929a-361">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="d929a-362">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-362">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="d929a-363">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="d929a-363">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="d929a-364">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="d929a-364">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="d929a-365">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="d929a-365">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="d929a-366">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="d929a-366">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="d929a-367">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="d929a-367">Validate model state.</span></span>
* <span data-ttu-id="d929a-368">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="d929a-368">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="d929a-369">使用特性批注的控制器 `[ApiController]` 自动验证模型状态并返回400响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-369">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="d929a-370">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="d929a-370">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="d929a-371">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="d929a-371">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="d929a-372">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="d929a-372">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="d929a-373">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="d929a-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d929a-374">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="d929a-374"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="d929a-375">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="d929a-375">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="d929a-376">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-376">Effectively handles an exception.</span></span>
  * <span data-ttu-id="d929a-377">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="d929a-377">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="d929a-378">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-378">Exception filters</span></span>

<span data-ttu-id="d929a-379">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-379">Exception filters:</span></span>

* <span data-ttu-id="d929a-380">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="d929a-380">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="d929a-381">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="d929a-381">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="d929a-382">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="d929a-382">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="d929a-383">以下代码测试异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-383">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="d929a-384">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-384">Exception filters:</span></span>

* <span data-ttu-id="d929a-385">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="d929a-385">Don't have before and after events.</span></span>
* <span data-ttu-id="d929a-386">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="d929a-386">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="d929a-387">处理在 Razor 页或控制器创建、 [模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-387">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="d929a-388">不要 **捕获资源** 筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-388">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="d929a-389">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-389">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="d929a-390">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-390">This stops propagation of the exception.</span></span> <span data-ttu-id="d929a-391">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="d929a-391">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="d929a-392">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="d929a-392">Only an action filter can do that.</span></span>

<span data-ttu-id="d929a-393">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-393">Exception filters:</span></span>

* <span data-ttu-id="d929a-394">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-394">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="d929a-395">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="d929a-395">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="d929a-396">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-396">Prefer middleware for exception handling.</span></span> <span data-ttu-id="d929a-397">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-397">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="d929a-398">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-398">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="d929a-399">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="d929a-399">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="d929a-400">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-400">Result filters</span></span>

<span data-ttu-id="d929a-401">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-401">Result filters:</span></span>

* <span data-ttu-id="d929a-402">实现接口：</span><span class="sxs-lookup"><span data-stu-id="d929a-402">Implement an interface:</span></span>
  * <span data-ttu-id="d929a-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d929a-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="d929a-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="d929a-404"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="d929a-405">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-405">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="d929a-406">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="d929a-406">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="d929a-407">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-407">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d929a-408">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="d929a-408">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="d929a-409">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="d929a-409">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="d929a-410">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="d929a-410">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="d929a-411">详细了解 [操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="d929a-411">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="d929a-412">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-412">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="d929a-413">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-413">Result filters are not executed when:</span></span>

* <span data-ttu-id="d929a-414">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-414">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="d929a-415">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-415">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="d929a-416"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-416">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="d929a-417">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-417">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="d929a-418">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="d929a-418">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="d929a-419">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-419">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="d929a-420">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="d929a-420">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d929a-421">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="d929a-421">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="d929a-422">如果响应已发送到客户端，则无法更改。</span><span class="sxs-lookup"><span data-stu-id="d929a-422">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="d929a-423">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="d929a-423">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="d929a-424">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="d929a-424">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="d929a-425">将 `Exception` 设置为 NULL 可有效地处理异常，并防止在管道的后续阶段引发该异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-425">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="d929a-426">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-426">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="d929a-427">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-427">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="d929a-428">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="d929a-428">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="d929a-429">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="d929a-429">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="d929a-430">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="d929a-430">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="d929a-431">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-431">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="d929a-432">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="d929a-432">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="d929a-433"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="d929a-433">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="d929a-434">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="d929a-434">This includes action results produced by:</span></span>

* <span data-ttu-id="d929a-435">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-435">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="d929a-436">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-436">Exception filters.</span></span>

<span data-ttu-id="d929a-437">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="d929a-437">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

## <a name="ifilterfactory"></a><span data-ttu-id="d929a-438">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d929a-438">IFilterFactory</span></span>

<span data-ttu-id="d929a-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="d929a-439"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="d929a-440">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-440">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="d929a-441">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="d929a-441">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="d929a-442">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-442">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="d929a-443">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="d929a-443">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="d929a-444">`IFilterFactory.IsReusable`:</span><span class="sxs-lookup"><span data-stu-id="d929a-444">`IFilterFactory.IsReusable`:</span></span>

* <span data-ttu-id="d929a-445">出厂时，由工厂创建的筛选器实例可在创建它的请求范围之外重复使用的提示。</span><span class="sxs-lookup"><span data-stu-id="d929a-445">Is a hint by the factory that the filter instance created by the factory may be reused outside of the request scope it was created within.</span></span>
* <span data-ttu-id="d929a-446">***不*** 应与依赖于非单一生存期的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-446">Should ***not*** be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d929a-447">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="d929a-447">The ASP.NET Core runtime doesn't guarantee:</span></span>

* <span data-ttu-id="d929a-448">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-448">That a single instance of the filter will be created.</span></span>
* <span data-ttu-id="d929a-449">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-449">The filter will not be re-requested from the DI container at some later point.</span></span>

> [!WARNING] 
> <span data-ttu-id="d929a-450">仅将配置 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> 为在 `true` 筛选器源明确的情况下返回，筛选器是无状态的，并且筛选器可在多个 HTTP 请求中安全使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-450">Only configure <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.IsReusable?displayProperty=nameWithType> to return `true` if the source of the filters is unambiguous, the filters are stateless, and the filters are safe to use across multiple HTTP requests.</span></span> <span data-ttu-id="d929a-451">例如，如果返回，则不要从已注册为作用域或暂时性的 DI 返回筛选器 `IFilterFactory.IsReusable` `true` 。</span><span class="sxs-lookup"><span data-stu-id="d929a-451">For instance, don't return filters from DI that are registered as scoped or transient if `IFilterFactory.IsReusable` returns `true`.</span></span>

<span data-ttu-id="d929a-452">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-452">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="d929a-453">在以下代码中应用了筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-453">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="d929a-454">通过运行[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d929a-454">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="d929a-455">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="d929a-455">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="d929a-456">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="d929a-456">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="d929a-457">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="d929a-457">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="d929a-458">**作者：**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="d929a-458">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="d929a-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="d929a-459">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="d929a-460">**内部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="d929a-460">**internal:** `My header`</span></span>

<span data-ttu-id="d929a-461">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="d929a-461">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="d929a-462">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d929a-462">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="d929a-463">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-463">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="d929a-464">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-464">Don't require passing parameters.</span></span>
* <span data-ttu-id="d929a-465">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-465">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="d929a-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="d929a-466"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d929a-467">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-467">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d929a-468">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-468">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="d929a-469">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-469">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="d929a-470">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-470">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="d929a-471">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="d929a-471">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="d929a-472">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-472">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="d929a-473">但筛选器又不同于中间件，它们是运行时的一部分，这意味着它们有权访问上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="d929a-473">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="d929a-474">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="d929a-474">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="d929a-475">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="d929a-475">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="d929a-476">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="d929a-476">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="d929a-477">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="d929a-477">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="d929a-478">后续操作</span><span class="sxs-lookup"><span data-stu-id="d929a-478">Next actions</span></span>

* <span data-ttu-id="d929a-479">请参阅 [筛选 Razor 页面的方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="d929a-479">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="d929a-480">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="d929a-480">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="d929a-481">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="d929a-481">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="d929a-482">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-482">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="d929a-483">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="d929a-483">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="d929a-484">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="d929a-484">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="d929a-485">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="d929a-485">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="d929a-486">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="d929a-486">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="d929a-487">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="d929a-487">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="d929a-488">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-488">Filters avoid duplicating code.</span></span> <span data-ttu-id="d929a-489">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="d929a-489">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="d929a-490">本文档适用于 Razor 具有视图的页面、API 控制器和控制器。</span><span class="sxs-lookup"><span data-stu-id="d929a-490">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="d929a-491">[查看或下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="d929a-491">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="d929a-492">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="d929a-492">How filters work</span></span>

<span data-ttu-id="d929a-493">筛选器在 ASP.NET Core 操作调用管道（有时称为筛选器管道）内运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-493">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="d929a-494">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-494">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 ASP.NET Core 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="d929a-497">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="d929a-497">Filter types</span></span>

<span data-ttu-id="d929a-498">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="d929a-498">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="d929a-499">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="d929a-499">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="d929a-500">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-500">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="d929a-501">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="d929a-501">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="d929a-502">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-502">Run after authorization.</span></span>  
  * <span data-ttu-id="d929a-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 可以在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-503"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="d929a-504">例如，`OnResourceExecuting` 可以在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-504">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="d929a-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 可以在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-505"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="d929a-506">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-506">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="d929a-507">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="d929a-507">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="d929a-508">页面 **不** 支持操作筛选器 Razor 。</span><span class="sxs-lookup"><span data-stu-id="d929a-508">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="d929a-509">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="d929a-509">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="d929a-510">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-510">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="d929a-511">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-511">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="d929a-512">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="d929a-512">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="d929a-513">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="d929a-513">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="d929a-516">实现</span><span class="sxs-lookup"><span data-stu-id="d929a-516">Implementation</span></span>

<span data-ttu-id="d929a-517">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="d929a-517">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="d929a-518">同步筛选器可以在其管道阶段之前 (`On-Stage-Executing`) 和之后 (`On-Stage-Executed`) 运行代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-518">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="d929a-519">例如，`OnActionExecuting` 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-519">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="d929a-520">`OnActionExecuted` 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-520">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d929a-521">异步筛选器定义 `On-Stage-ExecutionAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-521">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="d929a-522">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="d929a-522">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="d929a-523">每个 `On-Stage-ExecutionAsync` 方法采用执行筛选器的管道阶段的 `FilterType-ExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="d929a-523">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="d929a-524">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="d929a-524">Multiple filter stages</span></span>

<span data-ttu-id="d929a-525">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-525">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="d929a-526">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类实现 `IActionFilter`、`IResultFilter` 及其异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-526">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="d929a-527">筛选器接口的同步和异步版本任意实现一个，而不是同时实现 。</span><span class="sxs-lookup"><span data-stu-id="d929a-527">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="d929a-528">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-528">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="d929a-529">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-529">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="d929a-530">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-530">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="d929a-531">使用抽象类时（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>），将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-531">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="d929a-532">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="d929a-532">Built-in filter attributes</span></span>

<span data-ttu-id="d929a-533">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-533">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="d929a-534">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="d929a-534">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="d929a-535">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="d929a-535">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="d929a-536">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="d929a-536">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="d929a-537">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="d929a-537">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="d929a-538">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-538">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="d929a-539">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="d929a-539">Filter scopes and order of execution</span></span>

<span data-ttu-id="d929a-540">可以将筛选器添加到管道中的以下三个 *范围* 之一：</span><span class="sxs-lookup"><span data-stu-id="d929a-540">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="d929a-541">在操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-541">Using an attribute on an action.</span></span>
* <span data-ttu-id="d929a-542">在控制器上使用属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-542">Using an attribute on a controller.</span></span>
* <span data-ttu-id="d929a-543">所有控制器和操作的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="d929a-543">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="d929a-544">前面的代码使用 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 集合全局添加三个筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-544">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="d929a-545">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="d929a-545">Default order of execution</span></span>

<span data-ttu-id="d929a-546">当有同一类型的多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="d929a-546">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="d929a-547">全局筛选器涵盖类筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-547">Global filters surround class filters.</span></span> <span data-ttu-id="d929a-548">类筛选器涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-548">Class filters surround method filters.</span></span>

<span data-ttu-id="d929a-549">在筛选器嵌套模式下，筛选器的 after 代码会按照与 before 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-549">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="d929a-550">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="d929a-550">The filter sequence:</span></span>

* <span data-ttu-id="d929a-551">全局筛选器的 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-551">The *before* code of global filters.</span></span>
  * <span data-ttu-id="d929a-552">控制器筛选器的 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-552">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="d929a-553">操作方法筛选器的 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-553">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="d929a-554">操作方法筛选器的 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-554">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="d929a-555">控制器筛选器的 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-555">The *after* code of controller filters.</span></span>
* <span data-ttu-id="d929a-556">全局筛选器的 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-556">The *after* code of global filters.</span></span>
  
<span data-ttu-id="d929a-557">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="d929a-557">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="d929a-558">序列</span><span class="sxs-lookup"><span data-stu-id="d929a-558">Sequence</span></span> | <span data-ttu-id="d929a-559">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="d929a-559">Filter scope</span></span> | <span data-ttu-id="d929a-560">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="d929a-560">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="d929a-561">1</span><span class="sxs-lookup"><span data-stu-id="d929a-561">1</span></span> | <span data-ttu-id="d929a-562">全球</span><span class="sxs-lookup"><span data-stu-id="d929a-562">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d929a-563">2</span><span class="sxs-lookup"><span data-stu-id="d929a-563">2</span></span> | <span data-ttu-id="d929a-564">控制器</span><span class="sxs-lookup"><span data-stu-id="d929a-564">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d929a-565">3</span><span class="sxs-lookup"><span data-stu-id="d929a-565">3</span></span> | <span data-ttu-id="d929a-566">方法</span><span class="sxs-lookup"><span data-stu-id="d929a-566">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d929a-567">4</span><span class="sxs-lookup"><span data-stu-id="d929a-567">4</span></span> | <span data-ttu-id="d929a-568">方法</span><span class="sxs-lookup"><span data-stu-id="d929a-568">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d929a-569">5</span><span class="sxs-lookup"><span data-stu-id="d929a-569">5</span></span> | <span data-ttu-id="d929a-570">控制器</span><span class="sxs-lookup"><span data-stu-id="d929a-570">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="d929a-571">6</span><span class="sxs-lookup"><span data-stu-id="d929a-571">6</span></span> | <span data-ttu-id="d929a-572">全球</span><span class="sxs-lookup"><span data-stu-id="d929a-572">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="d929a-573">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="d929a-573">This sequence shows:</span></span>

* <span data-ttu-id="d929a-574">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="d929a-574">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="d929a-575">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="d929a-575">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="d929a-576">控制器和 Razor 页级筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-576">Controller and Razor Page level filters</span></span>

<span data-ttu-id="d929a-577">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-577">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="d929a-578">这些方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-578">These methods:</span></span>

* <span data-ttu-id="d929a-579">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-579">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="d929a-580">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-580">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="d929a-581">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-581">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="d929a-582">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="d929a-582">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="d929a-583">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-583">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="d929a-584">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="d929a-584">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="d929a-585">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="d929a-585">The `TestController`:</span></span>

* <span data-ttu-id="d929a-586">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="d929a-586">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="d929a-587">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="d929a-587">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="d929a-588">导航到 `https://localhost:5001/Test/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="d929a-588">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="d929a-589">有关 Razor 页面，请 [参阅 Razor 通过重写筛选器方法实现页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="d929a-589">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="d929a-590">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="d929a-590">Overriding the default order</span></span>

<span data-ttu-id="d929a-591">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="d929a-591">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="d929a-592">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="d929a-592">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="d929a-593">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-593">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="d929a-594">在具有较高的 `Order` 值的筛选器之前运行 before 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-594">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="d929a-595">在具有较高的 `Order` 值的筛选器之后运行 after 代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-595">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="d929a-596">可以使用构造函数参数设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-596">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="d929a-597">请考虑前面示例中所示的 3 个相同操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-597">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="d929a-598">如果控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="d929a-598">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="d929a-599">序列</span><span class="sxs-lookup"><span data-stu-id="d929a-599">Sequence</span></span> | <span data-ttu-id="d929a-600">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="d929a-600">Filter scope</span></span> | <span data-ttu-id="d929a-601">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="d929a-601">`Order` property</span></span> | <span data-ttu-id="d929a-602">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="d929a-602">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="d929a-603">1</span><span class="sxs-lookup"><span data-stu-id="d929a-603">1</span></span> | <span data-ttu-id="d929a-604">方法</span><span class="sxs-lookup"><span data-stu-id="d929a-604">Method</span></span> | <span data-ttu-id="d929a-605">0</span><span class="sxs-lookup"><span data-stu-id="d929a-605">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="d929a-606">2</span><span class="sxs-lookup"><span data-stu-id="d929a-606">2</span></span> | <span data-ttu-id="d929a-607">控制器</span><span class="sxs-lookup"><span data-stu-id="d929a-607">Controller</span></span> | <span data-ttu-id="d929a-608">1</span><span class="sxs-lookup"><span data-stu-id="d929a-608">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="d929a-609">3</span><span class="sxs-lookup"><span data-stu-id="d929a-609">3</span></span> | <span data-ttu-id="d929a-610">全球</span><span class="sxs-lookup"><span data-stu-id="d929a-610">Global</span></span> | <span data-ttu-id="d929a-611">2</span><span class="sxs-lookup"><span data-stu-id="d929a-611">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="d929a-612">4</span><span class="sxs-lookup"><span data-stu-id="d929a-612">4</span></span> | <span data-ttu-id="d929a-613">全球</span><span class="sxs-lookup"><span data-stu-id="d929a-613">Global</span></span> | <span data-ttu-id="d929a-614">2</span><span class="sxs-lookup"><span data-stu-id="d929a-614">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="d929a-615">5</span><span class="sxs-lookup"><span data-stu-id="d929a-615">5</span></span> | <span data-ttu-id="d929a-616">控制器</span><span class="sxs-lookup"><span data-stu-id="d929a-616">Controller</span></span> | <span data-ttu-id="d929a-617">1</span><span class="sxs-lookup"><span data-stu-id="d929a-617">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="d929a-618">6</span><span class="sxs-lookup"><span data-stu-id="d929a-618">6</span></span> | <span data-ttu-id="d929a-619">方法</span><span class="sxs-lookup"><span data-stu-id="d929a-619">Method</span></span> | <span data-ttu-id="d929a-620">0</span><span class="sxs-lookup"><span data-stu-id="d929a-620">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="d929a-621">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="d929a-621">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="d929a-622">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="d929a-622">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="d929a-623">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="d929a-623">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="d929a-624">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="d929a-624">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="d929a-625">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="d929a-625">Cancellation and short-circuiting</span></span>

<span data-ttu-id="d929a-626">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-626">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="d929a-627">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="d929a-627">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="d929a-628">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="d929a-628">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="d929a-629">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="d929a-629">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="d929a-630">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-630">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="d929a-631">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="d929a-631">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="d929a-632">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-632">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="d929a-633">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="d929a-633">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="d929a-634">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-634">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="d929a-635">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="d929a-635">Dependency injection</span></span>

<span data-ttu-id="d929a-636">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-636">Filters can be added by type or by instance.</span></span> <span data-ttu-id="d929a-637">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="d929a-637">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="d929a-638">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-638">If a type is added, it's type-activated.</span></span> <span data-ttu-id="d929a-639">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="d929a-639">A type-activated filter means:</span></span>

* <span data-ttu-id="d929a-640">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-640">An instance is created for each request.</span></span>
* <span data-ttu-id="d929a-641">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-641">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="d929a-642">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-642">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="d929a-643">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="d929a-643">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="d929a-644">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-644">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="d929a-645">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="d929a-645">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="d929a-646">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="d929a-646">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="d929a-647">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="d929a-647"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="d929a-648">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-648">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="d929a-649">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="d929a-649">Loggers are available from DI.</span></span> <span data-ttu-id="d929a-650">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="d929a-650">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="d929a-651">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="d929a-651">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="d929a-652">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="d929a-652">Logging added to filters:</span></span>

* <span data-ttu-id="d929a-653">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="d929a-653">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="d929a-654">不应记录操作或其他框架事件。</span><span class="sxs-lookup"><span data-stu-id="d929a-654">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="d929a-655">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="d929a-655">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="d929a-656">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d929a-656">ServiceFilterAttribute</span></span>

<span data-ttu-id="d929a-657">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-657">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="d929a-658"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-658">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="d929a-659">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="d929a-659">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d929a-660">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="d929a-660">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="d929a-661">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="d929a-661">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="d929a-662">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="d929a-662">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="d929a-663">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="d929a-663">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d929a-664">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="d929a-664">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="d929a-665">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-665">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="d929a-666">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-666">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="d929a-667">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-667">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="d929a-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="d929a-668"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d929a-669">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-669">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d929a-670">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-670">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="d929a-671">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="d929a-671">TypeFilterAttribute</span></span>

<span data-ttu-id="d929a-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-672"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="d929a-673">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="d929a-673">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="d929a-674">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="d929a-674">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="d929a-675">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="d929a-675">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="d929a-676">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-676">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="d929a-677">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-677">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="d929a-678">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="d929a-678">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="d929a-679">提供提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="d929a-679">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="d929a-680">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-680">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="d929a-681">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="d929a-681">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="d929a-682">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="d929a-682">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="d929a-683">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-683">Authorization filters</span></span>

<span data-ttu-id="d929a-684">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-684">Authorization filters:</span></span>

* <span data-ttu-id="d929a-685">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-685">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="d929a-686">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="d929a-686">Control access to action methods.</span></span>
* <span data-ttu-id="d929a-687">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-687">Have a before method, but no after method.</span></span>

<span data-ttu-id="d929a-688">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="d929a-688">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="d929a-689">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-689">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="d929a-690">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-690">The built-in authorization filter:</span></span>

* <span data-ttu-id="d929a-691">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="d929a-691">Calls the authorization system.</span></span>
* <span data-ttu-id="d929a-692">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="d929a-692">Does not authorize requests.</span></span>

<span data-ttu-id="d929a-693">不会在授权筛选器中引发异常：</span><span class="sxs-lookup"><span data-stu-id="d929a-693">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="d929a-694">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-694">The exception will not be handled.</span></span>
* <span data-ttu-id="d929a-695">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-695">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="d929a-696">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="d929a-696">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="d929a-697">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="d929a-697">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="d929a-698">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-698">Resource filters</span></span>

<span data-ttu-id="d929a-699">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-699">Resource filters:</span></span>

* <span data-ttu-id="d929a-700">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="d929a-701">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="d929a-701">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="d929a-702">只有 [授权筛选器](#authorization-filters) 才会在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="d929a-702">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="d929a-703">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="d929a-703">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="d929a-704">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="d929a-704">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="d929a-705">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="d929a-705">Resource filter examples:</span></span>

* <span data-ttu-id="d929a-706">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="d929a-706">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="d929a-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="d929a-707">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="d929a-708">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="d929a-708">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="d929a-709">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="d929a-709">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="d929a-710">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-710">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="d929a-711">操作筛选器 **不适用于** Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="d929a-711">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="d929a-712">Razor 页面支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="d929a-712">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="d929a-713">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="d929a-713">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="d929a-714">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-714">Action filters:</span></span>

* <span data-ttu-id="d929a-715">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="d929a-715">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="d929a-716">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-716">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="d929a-717">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-717">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="d929a-718"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-718">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="d929a-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="d929a-719"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="d929a-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-720"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="d929a-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-721"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="d929a-722">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="d929a-722">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="d929a-723">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-723">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="d929a-724">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="d929a-724">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d929a-725"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="d929a-725">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="d929a-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="d929a-726"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d929a-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="d929a-727"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="d929a-728">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="d929a-728">Setting this property to null:</span></span>

  * <span data-ttu-id="d929a-729">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-729">Effectively handles the exception.</span></span>
  * <span data-ttu-id="d929a-730">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="d929a-730">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="d929a-731">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="d929a-731">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="d929a-732">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-732">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="d929a-733">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="d929a-733">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="d929a-734">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="d929a-734">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="d929a-735">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="d929a-735">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="d929a-736">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="d929a-736">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="d929a-737">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="d929a-737">Validate model state.</span></span>
* <span data-ttu-id="d929a-738">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="d929a-738">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="d929a-739">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="d929a-739">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="d929a-740">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="d929a-740">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="d929a-741">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="d929a-741"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="d929a-742">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="d929a-742"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="d929a-743">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="d929a-743">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="d929a-744">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-744">Effectively handles an exception.</span></span>
  * <span data-ttu-id="d929a-745">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="d929a-745">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="d929a-746">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-746">Exception filters</span></span>

<span data-ttu-id="d929a-747">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-747">Exception filters:</span></span>

* <span data-ttu-id="d929a-748">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="d929a-748">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="d929a-749">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="d929a-749">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="d929a-750">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="d929a-750">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="d929a-751">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-751">Exception filters:</span></span>

* <span data-ttu-id="d929a-752">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="d929a-752">Don't have before and after events.</span></span>
* <span data-ttu-id="d929a-753">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="d929a-753">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="d929a-754">处理在 Razor 页或控制器创建、 [模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-754">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="d929a-755">不要 **捕获资源** 筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-755">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="d929a-756">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-756">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="d929a-757">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-757">This stops propagation of the exception.</span></span> <span data-ttu-id="d929a-758">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="d929a-758">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="d929a-759">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="d929a-759">Only an action filter can do that.</span></span>

<span data-ttu-id="d929a-760">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-760">Exception filters:</span></span>

* <span data-ttu-id="d929a-761">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-761">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="d929a-762">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="d929a-762">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="d929a-763">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-763">Prefer middleware for exception handling.</span></span> <span data-ttu-id="d929a-764">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-764">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="d929a-765">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-765">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="d929a-766">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="d929a-766">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="d929a-767">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="d929a-767">Result filters</span></span>

<span data-ttu-id="d929a-768">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-768">Result filters:</span></span>

* <span data-ttu-id="d929a-769">实现接口：</span><span class="sxs-lookup"><span data-stu-id="d929a-769">Implement an interface:</span></span>
  * <span data-ttu-id="d929a-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="d929a-770"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="d929a-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="d929a-771"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="d929a-772">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-772">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="d929a-773">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="d929a-773">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="d929a-774">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-774">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="d929a-775">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="d929a-775">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="d929a-776">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="d929a-776">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="d929a-777">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="d929a-777">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="d929a-778">详细了解 [操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="d929a-778">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="d929a-779">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-779">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="d929a-780">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-780">Result filters are not executed when:</span></span>

* <span data-ttu-id="d929a-781">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-781">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="d929a-782">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-782">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="d929a-783"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="d929a-783">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="d929a-784">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-784">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="d929a-785">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="d929a-785">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="d929a-786">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-786">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="d929a-787">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="d929a-787">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="d929a-788">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="d929a-788">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="d929a-789">如果响应已发送到客户端，则无法再更改。</span><span class="sxs-lookup"><span data-stu-id="d929a-789">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="d929a-790">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="d929a-790">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="d929a-791">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="d929a-791">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="d929a-792">将 `Exception` 设置为 NULL 可有效地处理异常，并防止 ASP.NET Core 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="d929a-792">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="d929a-793">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="d929a-793">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="d929a-794">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="d929a-794">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="d929a-795">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="d929a-795">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="d929a-796">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="d929a-796">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="d929a-797">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="d929a-797">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="d929a-798">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="d929a-798">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="d929a-799">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="d929a-799">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="d929a-800"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="d929a-800">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="d929a-801">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="d929a-801">This includes action results produced by:</span></span>

* <span data-ttu-id="d929a-802">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-802">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="d929a-803">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="d929a-803">Exception filters.</span></span>

<span data-ttu-id="d929a-804">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="d929a-804">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

## <a name="ifilterfactory"></a><span data-ttu-id="d929a-805">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d929a-805">IFilterFactory</span></span>

<span data-ttu-id="d929a-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="d929a-806"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="d929a-807">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-807">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="d929a-808">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="d929a-808">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="d929a-809">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="d929a-809">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="d929a-810">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="d929a-810">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="d929a-811">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-811">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="d929a-812">可以通过运行[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="d929a-812">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="d929a-813">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="d929a-813">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="d929a-814">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="d929a-814">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="d929a-815">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="d929a-815">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="d929a-816">**作者：**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="d929a-816">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="d929a-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="d929a-817">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="d929a-818">**内部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="d929a-818">**internal:** `My header`</span></span>

<span data-ttu-id="d929a-819">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="d929a-819">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="d929a-820">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="d929a-820">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="d929a-821">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="d929a-821">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="d929a-822">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="d929a-822">Don't require passing parameters.</span></span>
* <span data-ttu-id="d929a-823">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="d929a-823">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="d929a-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="d929a-824"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="d929a-825">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-825">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="d929a-826">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="d929a-826">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="d929a-827">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="d929a-827">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="d929a-828">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="d929a-828">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="d929a-829">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="d929a-829">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="d929a-830">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="d929a-830">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="d929a-831">但筛选器又不同于中间件，它们是 ASP.NET Core 运行时的一部分，这意味着它们有权访问 ASP.NET Core 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="d929a-831">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="d929a-832">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="d929a-832">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="d929a-833">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="d929a-833">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="d929a-834">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="d929a-834">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="d929a-835">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="d929a-835">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="d929a-836">后续操作</span><span class="sxs-lookup"><span data-stu-id="d929a-836">Next actions</span></span>

* <span data-ttu-id="d929a-837">请参阅 [筛选 Razor 页面的方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="d929a-837">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="d929a-838">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="d929a-838">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
