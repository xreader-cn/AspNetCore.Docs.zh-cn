---
title: ASP.NET Core 中的筛选器
author: Rick-Anderson
description: 了解筛选器的工作原理以及如何在 ASP.NET Core 中使用它们。
ms.author: riande
ms.custom: mvc
ms.date: 02/04/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/controllers/filters
ms.openlocfilehash: 7272e05b408ac6f8daeda586c6f40fcc5bd1f6eb
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82776781"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="b4911-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="b4911-104">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b4911-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b4911-105">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="b4911-106">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="b4911-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="b4911-107">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="b4911-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="b4911-108">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="b4911-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="b4911-109">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="b4911-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="b4911-110">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="b4911-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="b4911-111">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="b4911-112">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="b4911-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="b4911-113">本文档适用于 Razor Pages、API 控制器和具有视图的控制器。</span><span class="sxs-lookup"><span data-stu-id="b4911-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="b4911-114">筛选器无法直接与 [ Razor 组件](xref:blazor/components)一起使用。</span><span class="sxs-lookup"><span data-stu-id="b4911-114">Filters don't work directly with [Razor components](xref:blazor/components).</span></span> <span data-ttu-id="b4911-115">筛选器只能在以下情况下间接影响组件：</span><span class="sxs-lookup"><span data-stu-id="b4911-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="b4911-116">该组件嵌入在页面或视图中。</span><span class="sxs-lookup"><span data-stu-id="b4911-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="b4911-117">页面或控制器/视图使用此筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="b4911-118">[查看或下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b4911-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="b4911-119">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="b4911-119">How filters work</span></span>

<span data-ttu-id="b4911-120">筛选器在 ASP.NET Core 操作调用管道**（有时称为筛选器管道**）内运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="b4911-121">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="b4911-124">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="b4911-124">Filter types</span></span>

<span data-ttu-id="b4911-125">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="b4911-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="b4911-126">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="b4911-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="b4911-127">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="b4911-128">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="b4911-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="b4911-129">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-129">Run after authorization.</span></span>  
  * <span data-ttu-id="b4911-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="b4911-131">例如，`OnResourceExecuting` 在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="b4911-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="b4911-133">[操作筛选器](#action-filters)：</span><span class="sxs-lookup"><span data-stu-id="b4911-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="b4911-134">在调用操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="b4911-135">可以更改传递到操作中的参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="b4911-136">可以更改从操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="b4911-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="b4911-137">不可在 Razor Pages 中使用\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="b4911-138">[异常筛选器](#exception-filters)在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="b4911-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="b4911-139">[结果筛选器](#result-filters)在执行操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="b4911-140">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="b4911-141">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="b4911-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="b4911-142">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="b4911-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="b4911-145">实现</span><span class="sxs-lookup"><span data-stu-id="b4911-145">Implementation</span></span>

<span data-ttu-id="b4911-146">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="b4911-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="b4911-147">同步筛选器在其管道阶段之前和之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="b4911-148">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="b4911-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="b4911-150">异步筛选器定义 `On-Stage-ExecutionAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="b4911-151">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>：</span><span class="sxs-lookup"><span data-stu-id="b4911-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="b4911-152">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="b4911-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="b4911-153">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="b4911-153">Multiple filter stages</span></span>

<span data-ttu-id="b4911-154">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="b4911-155">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类可实现：</span><span class="sxs-lookup"><span data-stu-id="b4911-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="b4911-156">同步：<xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="b4911-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="b4911-157">异步：<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="b4911-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="b4911-158">实现**筛选**器接口的同步或异步版本，**而不**是两者都实现。</span><span class="sxs-lookup"><span data-stu-id="b4911-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="b4911-159">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="b4911-160">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="b4911-161">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="b4911-162">使用抽象类（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>）时，将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="b4911-163">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="b4911-163">Built-in filter attributes</span></span>

<span data-ttu-id="b4911-164">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="b4911-165">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="b4911-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-166">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="b4911-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="b4911-167">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="b4911-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="b4911-168">使用[浏览器开发人员工具](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools)等工具来检查标头。</span><span class="sxs-lookup"><span data-stu-id="b4911-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="b4911-169">在**响应标头** `author: Rick Anderson`下显示。</span><span class="sxs-lookup"><span data-stu-id="b4911-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="b4911-170">以下代码实现了 `ActionFilterAttribute`：</span><span class="sxs-lookup"><span data-stu-id="b4911-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="b4911-171">从配置系统读取标题和名称。</span><span class="sxs-lookup"><span data-stu-id="b4911-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="b4911-172">与前面的示例不同，以下代码不需要将筛选器参数添加到代码中。</span><span class="sxs-lookup"><span data-stu-id="b4911-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="b4911-173">将标题和名称添加到响应标头。</span><span class="sxs-lookup"><span data-stu-id="b4911-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-174">使用[选项模式](xref:fundamentals/configuration/options)从[配置系统](xref:fundamentals/configuration/index)中提供配置选项。</span><span class="sxs-lookup"><span data-stu-id="b4911-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="b4911-175">例如，从 appsettings.json 文件中：\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="b4911-176">在 `StartUp.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="b4911-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="b4911-177">`PositionOptions` 类已通过 `"Position"` 配置区域添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="b4911-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="b4911-178">`MyActionFilterAttribute` 已添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="b4911-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="b4911-179">以下代码显示 `PositionOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="b4911-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="b4911-180">以下代码将 `MyActionFilterAttribute` 应用于 `Index2` 方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="b4911-181">在调用`Sample/Index2`终结点`author: Rick Anderson`时， `Editor: Joe Smith`将显示 "**响应标头**"、和。</span><span class="sxs-lookup"><span data-stu-id="b4911-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="b4911-182">以下代码将 `MyActionFilterAttribute` 和 `AddHeaderAttribute` 应用于 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="b4911-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="b4911-183">无法将筛选器应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="b4911-184">它们可以应用于 Razor 页面模型或全局应用。</span><span class="sxs-lookup"><span data-stu-id="b4911-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="b4911-185">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="b4911-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="b4911-186">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="b4911-187">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="b4911-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="b4911-188">可以将筛选器添加到管道中的以下三个*范围*之一：</span><span class="sxs-lookup"><span data-stu-id="b4911-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="b4911-189">在控制器操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="b4911-190">无法将筛选器属性应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="b4911-191">在控制器或 Razor 页面上使用属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="b4911-192">所有控制器、操作和 Razor 页面的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="b4911-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="b4911-193">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="b4911-193">Default order of execution</span></span>

<span data-ttu-id="b4911-194">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="b4911-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="b4911-195">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="b4911-196">在筛选器嵌套模式下，筛选器的 after\*\* 代码会按照与 before\*\* 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="b4911-197">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="b4911-197">The filter sequence:</span></span>

* <span data-ttu-id="b4911-198">全局筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="b4911-199">控制器筛选器和 Razor 页面筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="b4911-200">操作方法筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="b4911-201">操作方法筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="b4911-202">控制器筛选器和 Razor 页面筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="b4911-203">全局筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="b4911-204">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="b4911-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="b4911-205">序列</span><span class="sxs-lookup"><span data-stu-id="b4911-205">Sequence</span></span> | <span data-ttu-id="b4911-206">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="b4911-206">Filter scope</span></span> | <span data-ttu-id="b4911-207">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="b4911-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="b4911-208">1</span><span class="sxs-lookup"><span data-stu-id="b4911-208">1</span></span> | <span data-ttu-id="b4911-209">全局</span><span class="sxs-lookup"><span data-stu-id="b4911-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="b4911-210">2</span><span class="sxs-lookup"><span data-stu-id="b4911-210">2</span></span> | <span data-ttu-id="b4911-211">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="b4911-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="b4911-212">3</span><span class="sxs-lookup"><span data-stu-id="b4911-212">3</span></span> | <span data-ttu-id="b4911-213">方法</span><span class="sxs-lookup"><span data-stu-id="b4911-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="b4911-214">4</span><span class="sxs-lookup"><span data-stu-id="b4911-214">4</span></span> | <span data-ttu-id="b4911-215">方法</span><span class="sxs-lookup"><span data-stu-id="b4911-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="b4911-216">5</span><span class="sxs-lookup"><span data-stu-id="b4911-216">5</span></span> | <span data-ttu-id="b4911-217">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="b4911-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="b4911-218">6</span><span class="sxs-lookup"><span data-stu-id="b4911-218">6</span></span> | <span data-ttu-id="b4911-219">全局</span><span class="sxs-lookup"><span data-stu-id="b4911-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="b4911-220">控制器级别筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-220">Controller level filters</span></span>

<span data-ttu-id="b4911-221">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="b4911-222">这些方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-222">These methods:</span></span>

* <span data-ttu-id="b4911-223">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="b4911-224">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="b4911-225">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="b4911-226">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="b4911-227">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="b4911-228">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="b4911-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="b4911-229">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="b4911-229">The `TestController`:</span></span>

* <span data-ttu-id="b4911-230">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="b4911-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="b4911-231">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="b4911-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="b4911-232">导航到 `https://localhost:5001/Test2/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="b4911-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="b4911-233">控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。</span><span class="sxs-lookup"><span data-stu-id="b4911-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="b4911-234">控制器级别筛选器无法设置为在将筛选器应用于方法之后运行\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="b4911-235">在下一节对 Order 进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="b4911-235">Order is explained in the next section.</span></span>

<span data-ttu-id="b4911-236">对于 Razor Pages，请参阅[通过重写筛选器方法实现 Razor 页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="b4911-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="b4911-237">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="b4911-237">Overriding the default order</span></span>

<span data-ttu-id="b4911-238">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="b4911-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="b4911-239">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="b4911-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="b4911-240">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="b4911-241">在具有较高的 `Order` 值的筛选器之前运行 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="b4911-242">在具有较高的 `Order` 值的筛选器之后运行 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="b4911-243">使用构造函数参数设置了 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="b4911-244">请考虑以下控制器中的两个操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="b4911-245">在 `StartUp.ConfigureServices` 中添加了全局筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="b4911-246">3 个筛选器按下列顺序运行：</span><span class="sxs-lookup"><span data-stu-id="b4911-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="b4911-247">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="b4911-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="b4911-248">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="b4911-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="b4911-249">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="b4911-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="b4911-250">如前所述，控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="b4911-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="b4911-251">在前面的代码中，`MySampleActionFilter` 具有全局作用域，因此它在具有控制器作用域的 `MyAction2FilterAttribute` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="b4911-252">若要首先运行 `MyAction2FilterAttribute`，请将顺序设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="b4911-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="b4911-253">若要首先运行全局筛选器 `MySampleActionFilter`，请将 `Order` 设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="b4911-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="b4911-254">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="b4911-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="b4911-255">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="b4911-256">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="b4911-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-257">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="b4911-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="b4911-258">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="b4911-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="b4911-259">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="b4911-260">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="b4911-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="b4911-261">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="b4911-262">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="b4911-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="b4911-263">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="b4911-264">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="b4911-264">Dependency injection</span></span>

<span data-ttu-id="b4911-265">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="b4911-266">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="b4911-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="b4911-267">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="b4911-268">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="b4911-268">A type-activated filter means:</span></span>

* <span data-ttu-id="b4911-269">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-269">An instance is created for each request.</span></span>
* <span data-ttu-id="b4911-270">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="b4911-271">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="b4911-272">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="b4911-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="b4911-273">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="b4911-274">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="b4911-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="b4911-275">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="b4911-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="b4911-276">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="b4911-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="b4911-277">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="b4911-278">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="b4911-278">Loggers are available from DI.</span></span> <span data-ttu-id="b4911-279">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="b4911-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="b4911-280">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="b4911-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="b4911-281">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="b4911-281">Logging added to filters:</span></span>

* <span data-ttu-id="b4911-282">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="b4911-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="b4911-283">不应记录操作或其他框架事件\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="b4911-284">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="b4911-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="b4911-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="b4911-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="b4911-286">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="b4911-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="b4911-288">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="b4911-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="b4911-289">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="b4911-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="b4911-290">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="b4911-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="b4911-291">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="b4911-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="b4911-292">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="b4911-293">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="b4911-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="b4911-294">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="b4911-295">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="b4911-296">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="b4911-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="b4911-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="b4911-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="b4911-298">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="b4911-299">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="b4911-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="b4911-300">TypeFilterAttribute</span></span>

<span data-ttu-id="b4911-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="b4911-302">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="b4911-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="b4911-303">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="b4911-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="b4911-304">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="b4911-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="b4911-305">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="b4911-306">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="b4911-307">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="b4911-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="b4911-308">提供提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="b4911-309">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="b4911-310">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="b4911-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="b4911-311">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="b4911-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="b4911-312">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-312">Authorization filters</span></span>

<span data-ttu-id="b4911-313">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-313">Authorization filters:</span></span>

* <span data-ttu-id="b4911-314">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="b4911-315">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="b4911-315">Control access to action methods.</span></span>
* <span data-ttu-id="b4911-316">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="b4911-317">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="b4911-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="b4911-318">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="b4911-319">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="b4911-320">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="b4911-320">Calls the authorization system.</span></span>
* <span data-ttu-id="b4911-321">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="b4911-321">Does not authorize requests.</span></span>

<span data-ttu-id="b4911-322">不会在授权筛选器中引发异常\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="b4911-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="b4911-323">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-323">The exception will not be handled.</span></span>
* <span data-ttu-id="b4911-324">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="b4911-325">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="b4911-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="b4911-326">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="b4911-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="b4911-327">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-327">Resource filters</span></span>

<span data-ttu-id="b4911-328">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-328">Resource filters:</span></span>

* <span data-ttu-id="b4911-329">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="b4911-330">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="b4911-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="b4911-331">只有[授权筛选器](#authorization-filters)才会在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="b4911-332">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="b4911-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="b4911-333">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="b4911-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="b4911-334">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="b4911-334">Resource filter examples:</span></span>

* <span data-ttu-id="b4911-335">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="b4911-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="b4911-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="b4911-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="b4911-337">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="b4911-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="b4911-338">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="b4911-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="b4911-339">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-339">Action filters</span></span>

<span data-ttu-id="b4911-340">操作筛选器不\*\*\*\* 应用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="b4911-340">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="b4911-341">Razor Pages 支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>。</span><span class="sxs-lookup"><span data-stu-id="b4911-341">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="b4911-342">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="b4911-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="b4911-343">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-343">Action filters:</span></span>

* <span data-ttu-id="b4911-344">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="b4911-345">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="b4911-346">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="b4911-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="b4911-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="b4911-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="b4911-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="b4911-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="b4911-351">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="b4911-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="b4911-352">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="b4911-353">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="b4911-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="b4911-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="b4911-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="b4911-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="b4911-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b4911-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="b4911-357">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="b4911-357">Setting this property to null:</span></span>

  * <span data-ttu-id="b4911-358">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="b4911-359">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="b4911-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="b4911-360">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="b4911-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="b4911-361">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="b4911-362">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="b4911-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="b4911-363">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="b4911-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="b4911-364">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="b4911-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="b4911-365">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="b4911-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="b4911-366">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="b4911-366">Validate model state.</span></span>
* <span data-ttu-id="b4911-367">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="b4911-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-368">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="b4911-368">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="b4911-369">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="b4911-369">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="b4911-370">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="b4911-370"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="b4911-371">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b4911-371"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="b4911-372">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="b4911-372">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="b4911-373">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-373">Effectively handles an exception.</span></span>
  * <span data-ttu-id="b4911-374">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="b4911-374">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="b4911-375">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-375">Exception filters</span></span>

<span data-ttu-id="b4911-376">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-376">Exception filters:</span></span>

* <span data-ttu-id="b4911-377">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="b4911-377">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="b4911-378">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="b4911-378">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="b4911-379">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="b4911-379">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="b4911-380">以下代码测试异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-380">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="b4911-381">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-381">Exception filters:</span></span>

* <span data-ttu-id="b4911-382">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="b4911-382">Don't have before and after events.</span></span>
* <span data-ttu-id="b4911-383">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="b4911-383">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="b4911-384">处理 Razor 页面或控制器创建、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-384">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="b4911-385">不要**捕获资源**筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-385">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="b4911-386">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="b4911-386">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="b4911-387">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-387">This stops propagation of the exception.</span></span> <span data-ttu-id="b4911-388">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="b4911-388">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="b4911-389">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="b4911-389">Only an action filter can do that.</span></span>

<span data-ttu-id="b4911-390">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-390">Exception filters:</span></span>

* <span data-ttu-id="b4911-391">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-391">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="b4911-392">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="b4911-392">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="b4911-393">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-393">Prefer middleware for exception handling.</span></span> <span data-ttu-id="b4911-394">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-394">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="b4911-395">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-395">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="b4911-396">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="b4911-396">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="b4911-397">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-397">Result filters</span></span>

<span data-ttu-id="b4911-398">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-398">Result filters:</span></span>

* <span data-ttu-id="b4911-399">实现接口：</span><span class="sxs-lookup"><span data-stu-id="b4911-399">Implement an interface:</span></span>
  * <span data-ttu-id="b4911-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="b4911-400"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="b4911-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="b4911-401"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="b4911-402">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-402">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="b4911-403">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="b4911-403">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="b4911-404">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-404">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="b4911-405">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="b4911-405">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="b4911-406">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="b4911-406">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="b4911-407">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="b4911-407">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="b4911-408">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="b4911-408">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="b4911-409">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-409">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="b4911-410">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-410">Result filters are not executed when:</span></span>

* <span data-ttu-id="b4911-411">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-411">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="b4911-412">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-412">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="b4911-413"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-413">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="b4911-414">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="b4911-414">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="b4911-415">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="b4911-415">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="b4911-416">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-416">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="b4911-417">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="b4911-417">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="b4911-418">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="b4911-418">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="b4911-419">如果响应已发送到客户端，则无法更改。</span><span class="sxs-lookup"><span data-stu-id="b4911-419">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="b4911-420">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b4911-420">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="b4911-421">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b4911-421">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="b4911-422">将 `Exception` 设置为 NULL 可有效地处理异常，并防止在管道的后续阶段引发该异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-422">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="b4911-423">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="b4911-423">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="b4911-424">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-424">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="b4911-425">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="b4911-425">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="b4911-426">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="b4911-426">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="b4911-427">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="b4911-427">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="b4911-428">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-428">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="b4911-429">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="b4911-429">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="b4911-430"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="b4911-430">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="b4911-431">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="b4911-431">This includes action results produced by:</span></span>

* <span data-ttu-id="b4911-432">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-432">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="b4911-433">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-433">Exception filters.</span></span>

<span data-ttu-id="b4911-434">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”\*\* 状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="b4911-434">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="b4911-435">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="b4911-435">IFilterFactory</span></span>

<span data-ttu-id="b4911-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="b4911-436"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="b4911-437">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-437">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="b4911-438">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="b4911-438">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="b4911-439">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-439">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="b4911-440">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="b4911-440">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="b4911-441">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-441">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="b4911-442">在以下代码中应用了筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-442">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="b4911-443">通过运行[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="b4911-443">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="b4911-444">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="b4911-444">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="b4911-445">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="b4911-445">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="b4911-446">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="b4911-446">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="b4911-447">**作者：**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="b4911-447">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="b4911-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="b4911-448">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="b4911-449">**内部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="b4911-449">**internal:** `My header`</span></span>

<span data-ttu-id="b4911-450">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="b4911-450">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="b4911-451">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="b4911-451">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="b4911-452">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-452">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="b4911-453">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-453">Don't require passing parameters.</span></span>
* <span data-ttu-id="b4911-454">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-454">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="b4911-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="b4911-455"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="b4911-456">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-456">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="b4911-457">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-457">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="b4911-458">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-458">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="b4911-459">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-459">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="b4911-460">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="b4911-460">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="b4911-461">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-461">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="b4911-462">但筛选器又不同于中间件，它们是运行时的一部分，这意味着它们有权访问上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="b4911-462">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="b4911-463">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="b4911-463">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="b4911-464">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="b4911-464">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="b4911-465">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="b4911-465">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="b4911-466">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="b4911-466">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="b4911-467">后续操作</span><span class="sxs-lookup"><span data-stu-id="b4911-467">Next actions</span></span>

* <span data-ttu-id="b4911-468">请参阅 [Razor Pages 的筛选器方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="b4911-468">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="b4911-469">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="b4911-469">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="b4911-470">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="b4911-470">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="b4911-471">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-471">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="b4911-472">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="b4911-472">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="b4911-473">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="b4911-473">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="b4911-474">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="b4911-474">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="b4911-475">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="b4911-475">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="b4911-476">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="b4911-476">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="b4911-477">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-477">Filters avoid duplicating code.</span></span> <span data-ttu-id="b4911-478">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="b4911-478">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="b4911-479">本文档适用于 Razor Pages、API 控制器和具有视图的控制器。</span><span class="sxs-lookup"><span data-stu-id="b4911-479">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="b4911-480">[查看或下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="b4911-480">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="b4911-481">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="b4911-481">How filters work</span></span>

<span data-ttu-id="b4911-482">筛选器在 ASP.NET Core 操作调用管道**（有时称为筛选器管道**）内运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-482">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="b4911-483">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-483">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 ASP.NET Core 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="b4911-486">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="b4911-486">Filter types</span></span>

<span data-ttu-id="b4911-487">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="b4911-487">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="b4911-488">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="b4911-488">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="b4911-489">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-489">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="b4911-490">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="b4911-490">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="b4911-491">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-491">Run after authorization.</span></span>  
  * <span data-ttu-id="b4911-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 可以在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-492"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="b4911-493">例如，`OnResourceExecuting` 可以在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-493">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="b4911-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 可以在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="b4911-495">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-495">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="b4911-496">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="b4911-496">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="b4911-497">不可在 Razor Pages 中使用操作筛选器\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-497">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="b4911-498">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="b4911-498">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="b4911-499">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-499">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="b4911-500">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-500">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="b4911-501">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="b4911-501">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="b4911-502">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="b4911-502">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="b4911-505">实现</span><span class="sxs-lookup"><span data-stu-id="b4911-505">Implementation</span></span>

<span data-ttu-id="b4911-506">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="b4911-506">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="b4911-507">同步筛选器可以在其管道阶段之前 (`On-Stage-Executing`) 和之后 (`On-Stage-Executed`) 运行代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-507">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="b4911-508">例如，`OnActionExecuting` 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-508">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="b4911-509">`OnActionExecuted` 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-509">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="b4911-510">异步筛选器定义 `On-Stage-ExecutionAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-510">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="b4911-511">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="b4911-511">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="b4911-512">每个 `On-Stage-ExecutionAsync` 方法采用执行筛选器的管道阶段的 `FilterType-ExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="b4911-512">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="b4911-513">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="b4911-513">Multiple filter stages</span></span>

<span data-ttu-id="b4911-514">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-514">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="b4911-515">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类实现 `IActionFilter`、`IResultFilter` 及其异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-515">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="b4911-516">实现**筛选**器接口的同步或异步版本，**而不**是两者都实现。</span><span class="sxs-lookup"><span data-stu-id="b4911-516">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="b4911-517">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-517">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="b4911-518">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-518">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="b4911-519">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-519">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="b4911-520">使用抽象类时（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>），将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-520">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="b4911-521">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="b4911-521">Built-in filter attributes</span></span>

<span data-ttu-id="b4911-522">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-522">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="b4911-523">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="b4911-523">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-524">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="b4911-524">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="b4911-525">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="b4911-525">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="b4911-526">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="b4911-526">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="b4911-527">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-527">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="b4911-528">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="b4911-528">Filter scopes and order of execution</span></span>

<span data-ttu-id="b4911-529">可以将筛选器添加到管道中的以下三个*范围*之一：</span><span class="sxs-lookup"><span data-stu-id="b4911-529">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="b4911-530">在操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-530">Using an attribute on an action.</span></span>
* <span data-ttu-id="b4911-531">在控制器上使用属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-531">Using an attribute on a controller.</span></span>
* <span data-ttu-id="b4911-532">所有控制器和操作的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="b4911-532">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="b4911-533">前面的代码使用 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 集合全局添加三个筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-533">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="b4911-534">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="b4911-534">Default order of execution</span></span>

<span data-ttu-id="b4911-535">当有同一类型的多个筛选器时，作用域可确定筛选器执行的默认顺序\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-535">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="b4911-536">全局筛选器涵盖类筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-536">Global filters surround class filters.</span></span> <span data-ttu-id="b4911-537">类筛选器涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-537">Class filters surround method filters.</span></span>

<span data-ttu-id="b4911-538">在筛选器嵌套模式下，筛选器的 after\*\* 代码会按照与 before\*\* 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-538">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="b4911-539">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="b4911-539">The filter sequence:</span></span>

* <span data-ttu-id="b4911-540">全局筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-540">The *before* code of global filters.</span></span>
  * <span data-ttu-id="b4911-541">控制器筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-541">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="b4911-542">操作方法筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-542">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="b4911-543">操作方法筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-543">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="b4911-544">控制器筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-544">The *after* code of controller filters.</span></span>
* <span data-ttu-id="b4911-545">全局筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-545">The *after* code of global filters.</span></span>
  
<span data-ttu-id="b4911-546">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="b4911-546">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="b4911-547">序列</span><span class="sxs-lookup"><span data-stu-id="b4911-547">Sequence</span></span> | <span data-ttu-id="b4911-548">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="b4911-548">Filter scope</span></span> | <span data-ttu-id="b4911-549">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="b4911-549">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="b4911-550">1</span><span class="sxs-lookup"><span data-stu-id="b4911-550">1</span></span> | <span data-ttu-id="b4911-551">全局</span><span class="sxs-lookup"><span data-stu-id="b4911-551">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="b4911-552">2</span><span class="sxs-lookup"><span data-stu-id="b4911-552">2</span></span> | <span data-ttu-id="b4911-553">控制器</span><span class="sxs-lookup"><span data-stu-id="b4911-553">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="b4911-554">3</span><span class="sxs-lookup"><span data-stu-id="b4911-554">3</span></span> | <span data-ttu-id="b4911-555">方法</span><span class="sxs-lookup"><span data-stu-id="b4911-555">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="b4911-556">4</span><span class="sxs-lookup"><span data-stu-id="b4911-556">4</span></span> | <span data-ttu-id="b4911-557">方法</span><span class="sxs-lookup"><span data-stu-id="b4911-557">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="b4911-558">5</span><span class="sxs-lookup"><span data-stu-id="b4911-558">5</span></span> | <span data-ttu-id="b4911-559">控制器</span><span class="sxs-lookup"><span data-stu-id="b4911-559">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="b4911-560">6</span><span class="sxs-lookup"><span data-stu-id="b4911-560">6</span></span> | <span data-ttu-id="b4911-561">全局</span><span class="sxs-lookup"><span data-stu-id="b4911-561">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="b4911-562">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="b4911-562">This sequence shows:</span></span>

* <span data-ttu-id="b4911-563">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="b4911-563">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="b4911-564">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="b4911-564">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="b4911-565">控制器和 Razor 页面级筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-565">Controller and Razor Page level filters</span></span>

<span data-ttu-id="b4911-566">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-566">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="b4911-567">这些方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-567">These methods:</span></span>

* <span data-ttu-id="b4911-568">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-568">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="b4911-569">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-569">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="b4911-570">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-570">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="b4911-571">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="b4911-571">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="b4911-572">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-572">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="b4911-573">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="b4911-573">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="b4911-574">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="b4911-574">The `TestController`:</span></span>

* <span data-ttu-id="b4911-575">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="b4911-575">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="b4911-576">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="b4911-576">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="b4911-577">导航到 `https://localhost:5001/Test/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="b4911-577">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="b4911-578">对于 Razor Pages，请参阅[通过重写筛选器方法实现 Razor 页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="b4911-578">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="b4911-579">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="b4911-579">Overriding the default order</span></span>

<span data-ttu-id="b4911-580">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="b4911-580">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="b4911-581">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="b4911-581">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="b4911-582">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-582">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="b4911-583">在具有较高的 `Order` 值的筛选器之前运行 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-583">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="b4911-584">在具有较高的 `Order` 值的筛选器之后运行 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-584">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="b4911-585">可以使用构造函数参数设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-585">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="b4911-586">请考虑前面示例中所示的 3 个相同操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-586">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="b4911-587">如果控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="b4911-587">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="b4911-588">序列</span><span class="sxs-lookup"><span data-stu-id="b4911-588">Sequence</span></span> | <span data-ttu-id="b4911-589">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="b4911-589">Filter scope</span></span> | <span data-ttu-id="b4911-590">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="b4911-590">`Order` property</span></span> | <span data-ttu-id="b4911-591">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="b4911-591">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="b4911-592">1</span><span class="sxs-lookup"><span data-stu-id="b4911-592">1</span></span> | <span data-ttu-id="b4911-593">方法</span><span class="sxs-lookup"><span data-stu-id="b4911-593">Method</span></span> | <span data-ttu-id="b4911-594">0</span><span class="sxs-lookup"><span data-stu-id="b4911-594">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="b4911-595">2</span><span class="sxs-lookup"><span data-stu-id="b4911-595">2</span></span> | <span data-ttu-id="b4911-596">控制器</span><span class="sxs-lookup"><span data-stu-id="b4911-596">Controller</span></span> | <span data-ttu-id="b4911-597">1</span><span class="sxs-lookup"><span data-stu-id="b4911-597">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="b4911-598">3</span><span class="sxs-lookup"><span data-stu-id="b4911-598">3</span></span> | <span data-ttu-id="b4911-599">全局</span><span class="sxs-lookup"><span data-stu-id="b4911-599">Global</span></span> | <span data-ttu-id="b4911-600">2</span><span class="sxs-lookup"><span data-stu-id="b4911-600">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="b4911-601">4</span><span class="sxs-lookup"><span data-stu-id="b4911-601">4</span></span> | <span data-ttu-id="b4911-602">全局</span><span class="sxs-lookup"><span data-stu-id="b4911-602">Global</span></span> | <span data-ttu-id="b4911-603">2</span><span class="sxs-lookup"><span data-stu-id="b4911-603">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="b4911-604">5</span><span class="sxs-lookup"><span data-stu-id="b4911-604">5</span></span> | <span data-ttu-id="b4911-605">控制器</span><span class="sxs-lookup"><span data-stu-id="b4911-605">Controller</span></span> | <span data-ttu-id="b4911-606">1</span><span class="sxs-lookup"><span data-stu-id="b4911-606">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="b4911-607">6</span><span class="sxs-lookup"><span data-stu-id="b4911-607">6</span></span> | <span data-ttu-id="b4911-608">方法</span><span class="sxs-lookup"><span data-stu-id="b4911-608">Method</span></span> | <span data-ttu-id="b4911-609">0</span><span class="sxs-lookup"><span data-stu-id="b4911-609">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="b4911-610">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="b4911-610">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="b4911-611">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="b4911-611">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="b4911-612">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="b4911-612">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="b4911-613">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="b4911-613">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="b4911-614">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="b4911-614">Cancellation and short-circuiting</span></span>

<span data-ttu-id="b4911-615">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-615">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="b4911-616">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="b4911-616">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-617">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="b4911-617">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="b4911-618">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="b4911-618">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="b4911-619">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-619">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="b4911-620">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="b4911-620">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="b4911-621">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-621">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="b4911-622">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="b4911-622">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="b4911-623">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-623">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="b4911-624">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="b4911-624">Dependency injection</span></span>

<span data-ttu-id="b4911-625">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-625">Filters can be added by type or by instance.</span></span> <span data-ttu-id="b4911-626">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="b4911-626">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="b4911-627">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-627">If a type is added, it's type-activated.</span></span> <span data-ttu-id="b4911-628">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="b4911-628">A type-activated filter means:</span></span>

* <span data-ttu-id="b4911-629">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-629">An instance is created for each request.</span></span>
* <span data-ttu-id="b4911-630">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-630">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="b4911-631">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-631">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="b4911-632">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="b4911-632">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="b4911-633">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-633">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="b4911-634">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="b4911-634">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="b4911-635">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="b4911-635">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="b4911-636">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="b4911-636"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="b4911-637">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-637">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="b4911-638">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="b4911-638">Loggers are available from DI.</span></span> <span data-ttu-id="b4911-639">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="b4911-639">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="b4911-640">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="b4911-640">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="b4911-641">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="b4911-641">Logging added to filters:</span></span>

* <span data-ttu-id="b4911-642">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="b4911-642">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="b4911-643">不应记录操作或其他框架事件\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-643">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="b4911-644">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="b4911-644">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="b4911-645">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="b4911-645">ServiceFilterAttribute</span></span>

<span data-ttu-id="b4911-646">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-646">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="b4911-647"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-647">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="b4911-648">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="b4911-648">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="b4911-649">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="b4911-649">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="b4911-650">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="b4911-650">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="b4911-651">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="b4911-651">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="b4911-652">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-652">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="b4911-653">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="b4911-653">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="b4911-654">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-654">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="b4911-655">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-655">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="b4911-656">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="b4911-656">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="b4911-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="b4911-657"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="b4911-658">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-658">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="b4911-659">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-659">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="b4911-660">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="b4911-660">TypeFilterAttribute</span></span>

<span data-ttu-id="b4911-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-661"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="b4911-662">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="b4911-662">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="b4911-663">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="b4911-663">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="b4911-664">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="b4911-664">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="b4911-665">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-665">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="b4911-666">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-666">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="b4911-667">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="b4911-667">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="b4911-668">提供提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="b4911-668">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="b4911-669">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-669">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="b4911-670">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="b4911-670">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="b4911-671">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="b4911-671">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="b4911-672">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-672">Authorization filters</span></span>

<span data-ttu-id="b4911-673">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-673">Authorization filters:</span></span>

* <span data-ttu-id="b4911-674">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-674">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="b4911-675">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="b4911-675">Control access to action methods.</span></span>
* <span data-ttu-id="b4911-676">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-676">Have a before method, but no after method.</span></span>

<span data-ttu-id="b4911-677">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="b4911-677">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="b4911-678">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-678">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="b4911-679">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-679">The built-in authorization filter:</span></span>

* <span data-ttu-id="b4911-680">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="b4911-680">Calls the authorization system.</span></span>
* <span data-ttu-id="b4911-681">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="b4911-681">Does not authorize requests.</span></span>

<span data-ttu-id="b4911-682">不会在授权筛选器中引发异常\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="b4911-682">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="b4911-683">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-683">The exception will not be handled.</span></span>
* <span data-ttu-id="b4911-684">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-684">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="b4911-685">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="b4911-685">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="b4911-686">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="b4911-686">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="b4911-687">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-687">Resource filters</span></span>

<span data-ttu-id="b4911-688">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-688">Resource filters:</span></span>

* <span data-ttu-id="b4911-689">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-689">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="b4911-690">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="b4911-690">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="b4911-691">只有[授权筛选器](#authorization-filters)才会在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="b4911-691">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="b4911-692">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="b4911-692">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="b4911-693">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="b4911-693">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="b4911-694">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="b4911-694">Resource filter examples:</span></span>

* <span data-ttu-id="b4911-695">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="b4911-695">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="b4911-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="b4911-696">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="b4911-697">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="b4911-697">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="b4911-698">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="b4911-698">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="b4911-699">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-699">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="b4911-700">操作筛选器**不适用于** Razor页面。</span><span class="sxs-lookup"><span data-stu-id="b4911-700">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="b4911-701">页面支持<xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter>和<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="b4911-701"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="b4911-702">有关详细信息，请参阅[ Razor筛选页面方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="b4911-702">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="b4911-703">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-703">Action filters:</span></span>

* <span data-ttu-id="b4911-704">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="b4911-704">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="b4911-705">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-705">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="b4911-706">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-706">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="b4911-707"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-707">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="b4911-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="b4911-708"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="b4911-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-709"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="b4911-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-710"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="b4911-711">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="b4911-711">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="b4911-712">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-712">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="b4911-713">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="b4911-713">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="b4911-714"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="b4911-714">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="b4911-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="b4911-715"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="b4911-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b4911-716"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="b4911-717">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="b4911-717">Setting this property to null:</span></span>

  * <span data-ttu-id="b4911-718">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-718">Effectively handles the exception.</span></span>
  * <span data-ttu-id="b4911-719">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="b4911-719">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="b4911-720">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="b4911-720">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="b4911-721">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-721">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="b4911-722">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="b4911-722">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="b4911-723">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="b4911-723">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="b4911-724">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="b4911-724">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="b4911-725">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="b4911-725">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="b4911-726">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="b4911-726">Validate model state.</span></span>
* <span data-ttu-id="b4911-727">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="b4911-727">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="b4911-728">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="b4911-728">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="b4911-729">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="b4911-729">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="b4911-730">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="b4911-730"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="b4911-731">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b4911-731"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="b4911-732">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="b4911-732">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="b4911-733">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-733">Effectively handles an exception.</span></span>
  * <span data-ttu-id="b4911-734">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="b4911-734">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="b4911-735">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-735">Exception filters</span></span>

<span data-ttu-id="b4911-736">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-736">Exception filters:</span></span>

* <span data-ttu-id="b4911-737">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="b4911-737">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="b4911-738">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="b4911-738">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="b4911-739">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="b4911-739">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="b4911-740">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-740">Exception filters:</span></span>

* <span data-ttu-id="b4911-741">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="b4911-741">Don't have before and after events.</span></span>
* <span data-ttu-id="b4911-742">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="b4911-742">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="b4911-743">处理在页或控制器创建Razor 、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-743">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="b4911-744">不要**捕获资源**筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-744">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="b4911-745">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="b4911-745">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="b4911-746">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-746">This stops propagation of the exception.</span></span> <span data-ttu-id="b4911-747">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="b4911-747">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="b4911-748">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="b4911-748">Only an action filter can do that.</span></span>

<span data-ttu-id="b4911-749">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-749">Exception filters:</span></span>

* <span data-ttu-id="b4911-750">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-750">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="b4911-751">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="b4911-751">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="b4911-752">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-752">Prefer middleware for exception handling.</span></span> <span data-ttu-id="b4911-753">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器\*\*。</span><span class="sxs-lookup"><span data-stu-id="b4911-753">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="b4911-754">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-754">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="b4911-755">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="b4911-755">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="b4911-756">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="b4911-756">Result filters</span></span>

<span data-ttu-id="b4911-757">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-757">Result filters:</span></span>

* <span data-ttu-id="b4911-758">实现接口：</span><span class="sxs-lookup"><span data-stu-id="b4911-758">Implement an interface:</span></span>
  * <span data-ttu-id="b4911-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="b4911-759"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="b4911-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="b4911-760"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="b4911-761">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-761">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="b4911-762">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="b4911-762">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="b4911-763">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-763">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="b4911-764">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="b4911-764">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="b4911-765">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="b4911-765">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="b4911-766">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="b4911-766">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="b4911-767">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="b4911-767">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="b4911-768">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-768">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="b4911-769">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-769">Result filters are not executed when:</span></span>

* <span data-ttu-id="b4911-770">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-770">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="b4911-771">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-771">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="b4911-772"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="b4911-772">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="b4911-773">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="b4911-773">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="b4911-774">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="b4911-774">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="b4911-775">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-775">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="b4911-776">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="b4911-776">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="b4911-777">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="b4911-777">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="b4911-778">如果响应已发送到客户端，则无法再更改。</span><span class="sxs-lookup"><span data-stu-id="b4911-778">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="b4911-779">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="b4911-779">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="b4911-780">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="b4911-780">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="b4911-781">将 `Exception` 设置为 NULL 可有效地处理异常，并防止 ASP.NET Core 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="b4911-781">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="b4911-782">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="b4911-782">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="b4911-783">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="b4911-783">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="b4911-784">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="b4911-784">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="b4911-785">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="b4911-785">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="b4911-786">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="b4911-786">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="b4911-787">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="b4911-787">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="b4911-788">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="b4911-788">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="b4911-789"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="b4911-789">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="b4911-790">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="b4911-790">This includes action results produced by:</span></span>

* <span data-ttu-id="b4911-791">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-791">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="b4911-792">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="b4911-792">Exception filters.</span></span>

<span data-ttu-id="b4911-793">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”\*\* 状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="b4911-793">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="b4911-794">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="b4911-794">IFilterFactory</span></span>

<span data-ttu-id="b4911-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="b4911-795"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="b4911-796">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-796">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="b4911-797">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="b4911-797">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="b4911-798">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="b4911-798">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="b4911-799">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="b4911-799">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="b4911-800">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-800">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="b4911-801">可以通过运行[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="b4911-801">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="b4911-802">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="b4911-802">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="b4911-803">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="b4911-803">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="b4911-804">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="b4911-804">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="b4911-805">**作者：**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="b4911-805">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="b4911-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="b4911-806">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="b4911-807">**内部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="b4911-807">**internal:** `My header`</span></span>

<span data-ttu-id="b4911-808">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="b4911-808">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="b4911-809">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="b4911-809">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="b4911-810">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="b4911-810">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="b4911-811">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="b4911-811">Don't require passing parameters.</span></span>
* <span data-ttu-id="b4911-812">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="b4911-812">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="b4911-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="b4911-813"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="b4911-814">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-814">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="b4911-815">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="b4911-815">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="b4911-816">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="b4911-816">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="b4911-817">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="b4911-817">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="b4911-818">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="b4911-818">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="b4911-819">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="b4911-819">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="b4911-820">但筛选器又不同于中间件，它们是 ASP.NET Core 运行时的一部分，这意味着它们有权访问 ASP.NET Core 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="b4911-820">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="b4911-821">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="b4911-821">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="b4911-822">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="b4911-822">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="b4911-823">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="b4911-823">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="b4911-824">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="b4911-824">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="b4911-825">后续操作</span><span class="sxs-lookup"><span data-stu-id="b4911-825">Next actions</span></span>

* <span data-ttu-id="b4911-826">请参阅[筛选页面Razor的方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="b4911-826">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="b4911-827">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="b4911-827">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
