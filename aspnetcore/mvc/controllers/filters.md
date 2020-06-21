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
ms.openlocfilehash: 068b471c1f5fa5f0ca87dd7b028badf70f8c1b67
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122160"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="8c105-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="8c105-104">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8c105-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8c105-105">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8c105-106">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="8c105-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8c105-107">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="8c105-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8c105-108">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="8c105-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8c105-109">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="8c105-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8c105-110">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="8c105-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8c105-111">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="8c105-112">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="8c105-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8c105-113">本文档适用于 Razor 具有视图的页面、API 控制器和控制器。</span><span class="sxs-lookup"><span data-stu-id="8c105-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span> <span data-ttu-id="8c105-114">筛选器不直接与[ Razor 组件](xref:blazor/components/index)一起使用。</span><span class="sxs-lookup"><span data-stu-id="8c105-114">Filters don't work directly with [Razor components](xref:blazor/components/index).</span></span> <span data-ttu-id="8c105-115">筛选器只能在以下情况下间接影响组件：</span><span class="sxs-lookup"><span data-stu-id="8c105-115">A filter can only indirectly affect a component when:</span></span>

* <span data-ttu-id="8c105-116">该组件嵌入在页面或视图中。</span><span class="sxs-lookup"><span data-stu-id="8c105-116">The component is embedded in a page or view.</span></span>
* <span data-ttu-id="8c105-117">页面或控制器/视图使用此筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-117">The page or controller/view uses the filter.</span></span>

<span data-ttu-id="8c105-118">[查看或下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="8c105-118">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8c105-119">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="8c105-119">How filters work</span></span>

<span data-ttu-id="8c105-120">筛选器在 ASP.NET Core 操作调用管道**（有时称为筛选器管道**）内运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-120">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span> <span data-ttu-id="8c105-121">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-121">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8c105-124">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="8c105-124">Filter types</span></span>

<span data-ttu-id="8c105-125">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="8c105-125">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8c105-126">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="8c105-126">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8c105-127">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-127">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="8c105-128">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="8c105-128">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8c105-129">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-129">Run after authorization.</span></span>  
  * <span data-ttu-id="8c105-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-130"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8c105-131">例如，`OnResourceExecuting` 在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-131">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="8c105-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-132"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8c105-133">[操作筛选器](#action-filters)：</span><span class="sxs-lookup"><span data-stu-id="8c105-133">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="8c105-134">在调用操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-134">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="8c105-135">可以更改传递到操作中的参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-135">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="8c105-136">可以更改从操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="8c105-136">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="8c105-137">页面**不**支持 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8c105-137">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8c105-138">[异常筛选器](#exception-filters)在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="8c105-138">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="8c105-139">[结果筛选器](#result-filters)在执行操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-139">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="8c105-140">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-140">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8c105-141">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="8c105-141">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8c105-142">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="8c105-142">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8c105-145">实现</span><span class="sxs-lookup"><span data-stu-id="8c105-145">Implementation</span></span>

<span data-ttu-id="8c105-146">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="8c105-146">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8c105-147">同步筛选器在其管道阶段之前和之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-147">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="8c105-148">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-148">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="8c105-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-149"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c105-150">异步筛选器定义 `On-Stage-ExecutionAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-150">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="8c105-151">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>：</span><span class="sxs-lookup"><span data-stu-id="8c105-151">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8c105-152">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="8c105-152">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8c105-153">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="8c105-153">Multiple filter stages</span></span>

<span data-ttu-id="8c105-154">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-154">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8c105-155">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类可实现：</span><span class="sxs-lookup"><span data-stu-id="8c105-155">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="8c105-156">同步：<xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c105-156">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="8c105-157">异步：<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c105-157">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="8c105-158">筛选器接口的同步和异步版本任意实现一个，而不是同时实现   。</span><span class="sxs-lookup"><span data-stu-id="8c105-158">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8c105-159">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-159">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8c105-160">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-160">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8c105-161">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-161">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8c105-162">使用抽象类（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>）时，将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-162">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8c105-163">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="8c105-163">Built-in filter attributes</span></span>

<span data-ttu-id="8c105-164">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-164">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8c105-165">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="8c105-165">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8c105-166">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="8c105-166">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8c105-167">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="8c105-167">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="8c105-168">使用[浏览器开发人员工具](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools)等工具来检查标头。</span><span class="sxs-lookup"><span data-stu-id="8c105-168">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="8c105-169">在响应标头下，将显示 `author: Rick Anderson` 。</span><span class="sxs-lookup"><span data-stu-id="8c105-169">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="8c105-170">以下代码实现了 `ActionFilterAttribute`：</span><span class="sxs-lookup"><span data-stu-id="8c105-170">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="8c105-171">从配置系统读取标题和名称。</span><span class="sxs-lookup"><span data-stu-id="8c105-171">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="8c105-172">与前面的示例不同，以下代码不需要将筛选器参数添加到代码中。</span><span class="sxs-lookup"><span data-stu-id="8c105-172">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="8c105-173">将标题和名称添加到响应标头。</span><span class="sxs-lookup"><span data-stu-id="8c105-173">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8c105-174">使用[选项模式](xref:fundamentals/configuration/options)从[配置系统](xref:fundamentals/configuration/index)中提供配置选项。</span><span class="sxs-lookup"><span data-stu-id="8c105-174">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="8c105-175">例如，从 appsettings.json 文件中：\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-175">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="8c105-176">在 `StartUp.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="8c105-176">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="8c105-177">`PositionOptions` 类已通过 `"Position"` 配置区域添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="8c105-177">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="8c105-178">`MyActionFilterAttribute` 已添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="8c105-178">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="8c105-179">以下代码显示 `PositionOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="8c105-179">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="8c105-180">以下代码将 `MyActionFilterAttribute` 应用于 `Index2` 方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-180">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="8c105-181">在**Response Headers** `author: Rick Anderson` `Editor: Joe Smith` 调用终结点时，将显示 "响应标头"、和 `Sample/Index2` 。</span><span class="sxs-lookup"><span data-stu-id="8c105-181">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="8c105-182">下面的代码将 `MyActionFilterAttribute` 和应用于 `AddHeaderAttribute` Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="8c105-182">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="8c105-183">筛选器不能应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-183">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="8c105-184">它们可以应用于 Razor 页面模型或全局应用。</span><span class="sxs-lookup"><span data-stu-id="8c105-184">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="8c105-185">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="8c105-185">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8c105-186">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-186">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8c105-187">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="8c105-187">Filter scopes and order of execution</span></span>

<span data-ttu-id="8c105-188">可以将筛选器添加到管道中的以下三个*范围*之一：</span><span class="sxs-lookup"><span data-stu-id="8c105-188">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8c105-189">在控制器操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-189">Using an attribute on a controller action.</span></span> <span data-ttu-id="8c105-190">筛选器属性不能应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-190">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="8c105-191">在控制器或页上使用特性 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8c105-191">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="8c105-192">针对所有控制器、操作和页面全局 Razor 显示，如以下代码所示：</span><span class="sxs-lookup"><span data-stu-id="8c105-192">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="8c105-193">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="8c105-193">Default order of execution</span></span>

<span data-ttu-id="8c105-194">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="8c105-194">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8c105-195">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-195">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="8c105-196">在筛选器嵌套模式下，筛选器的 after\*\* 代码会按照与 before\*\* 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-196">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8c105-197">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="8c105-197">The filter sequence:</span></span>

* <span data-ttu-id="8c105-198">全局筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-198">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8c105-199">控制器*before*和 Razor 页面筛选器的前代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-199">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="8c105-200">操作方法筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-200">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8c105-201">操作方法筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-201">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8c105-202">控制器*after*和 Razor 页面筛选器后的代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-202">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="8c105-203">全局筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-203">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8c105-204">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="8c105-204">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8c105-205">序列</span><span class="sxs-lookup"><span data-stu-id="8c105-205">Sequence</span></span> | <span data-ttu-id="8c105-206">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="8c105-206">Filter scope</span></span> | <span data-ttu-id="8c105-207">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="8c105-207">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8c105-208">1</span><span class="sxs-lookup"><span data-stu-id="8c105-208">1</span></span> | <span data-ttu-id="8c105-209">全球</span><span class="sxs-lookup"><span data-stu-id="8c105-209">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c105-210">2</span><span class="sxs-lookup"><span data-stu-id="8c105-210">2</span></span> | <span data-ttu-id="8c105-211">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="8c105-211">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="8c105-212">3</span><span class="sxs-lookup"><span data-stu-id="8c105-212">3</span></span> | <span data-ttu-id="8c105-213">方法</span><span class="sxs-lookup"><span data-stu-id="8c105-213">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c105-214">4</span><span class="sxs-lookup"><span data-stu-id="8c105-214">4</span></span> | <span data-ttu-id="8c105-215">方法</span><span class="sxs-lookup"><span data-stu-id="8c105-215">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c105-216">5</span><span class="sxs-lookup"><span data-stu-id="8c105-216">5</span></span> | <span data-ttu-id="8c105-217">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="8c105-217">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c105-218">6</span><span class="sxs-lookup"><span data-stu-id="8c105-218">6</span></span> | <span data-ttu-id="8c105-219">全球</span><span class="sxs-lookup"><span data-stu-id="8c105-219">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="8c105-220">控制器级别筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-220">Controller level filters</span></span>

<span data-ttu-id="8c105-221">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-221">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8c105-222">这些方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-222">These methods:</span></span>

* <span data-ttu-id="8c105-223">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-223">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8c105-224">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-224">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8c105-225">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-225">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8c105-226">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-226">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8c105-227">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-227">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8c105-228">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="8c105-228">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8c105-229">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="8c105-229">The `TestController`:</span></span>

* <span data-ttu-id="8c105-230">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="8c105-230">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8c105-231">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="8c105-231">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="8c105-232">导航到 `https://localhost:5001/Test2/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="8c105-232">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8c105-233">控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。</span><span class="sxs-lookup"><span data-stu-id="8c105-233">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="8c105-234">控制器级别筛选器无法设置为在将筛选器应用于方法之后运行\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="8c105-234">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="8c105-235">在下一节对 Order 进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="8c105-235">Order is explained in the next section.</span></span>

<span data-ttu-id="8c105-236">有关 Razor 页面，请[参阅 Razor 通过重写筛选器方法实现页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="8c105-236">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8c105-237">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="8c105-237">Overriding the default order</span></span>

<span data-ttu-id="8c105-238">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="8c105-238">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8c105-239">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="8c105-239">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8c105-240">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-240">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8c105-241">在具有较高的 `Order` 值的筛选器之前运行 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-241">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8c105-242">在具有较高的 `Order` 值的筛选器之后运行 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-242">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8c105-243">使用构造函数参数设置了 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-243">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="8c105-244">请考虑以下控制器中的两个操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-244">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="8c105-245">在 `StartUp.ConfigureServices` 中添加了全局筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-245">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="8c105-246">3 个筛选器按下列顺序运行：</span><span class="sxs-lookup"><span data-stu-id="8c105-246">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="8c105-247">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="8c105-247">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8c105-248">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="8c105-248">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8c105-249">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="8c105-249">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8c105-250">如前所述，控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="8c105-250">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="8c105-251">在前面的代码中，`MySampleActionFilter` 具有全局作用域，因此它在具有控制器作用域的 `MyAction2FilterAttribute` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-251">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="8c105-252">若要首先运行 `MyAction2FilterAttribute`，请将顺序设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="8c105-252">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="8c105-253">若要首先运行全局筛选器 `MySampleActionFilter`，请将 `Order` 设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="8c105-253">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8c105-254">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="8c105-254">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8c105-255">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-255">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8c105-256">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="8c105-256">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8c105-257">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="8c105-257">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8c105-258">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="8c105-258">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8c105-259">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-259">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8c105-260">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="8c105-260">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8c105-261">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-261">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8c105-262">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="8c105-262">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8c105-263">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-263">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="8c105-264">依赖项注入</span><span class="sxs-lookup"><span data-stu-id="8c105-264">Dependency injection</span></span>

<span data-ttu-id="8c105-265">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-265">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8c105-266">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="8c105-266">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8c105-267">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-267">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8c105-268">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="8c105-268">A type-activated filter means:</span></span>

* <span data-ttu-id="8c105-269">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-269">An instance is created for each request.</span></span>
* <span data-ttu-id="8c105-270">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-270">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8c105-271">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-271">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8c105-272">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="8c105-272">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8c105-273">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-273">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8c105-274">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="8c105-274">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8c105-275">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="8c105-275">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8c105-276">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="8c105-276"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8c105-277">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-277">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8c105-278">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="8c105-278">Loggers are available from DI.</span></span> <span data-ttu-id="8c105-279">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="8c105-279">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8c105-280">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="8c105-280">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8c105-281">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="8c105-281">Logging added to filters:</span></span>

* <span data-ttu-id="8c105-282">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="8c105-282">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8c105-283">不应记录操作或其他框架事件\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="8c105-283">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8c105-284">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="8c105-284">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8c105-285">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c105-285">ServiceFilterAttribute</span></span>

<span data-ttu-id="8c105-286">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-286">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8c105-287"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-287">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8c105-288">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="8c105-288">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c105-289">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="8c105-289">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="8c105-290">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="8c105-290">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8c105-291">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="8c105-291">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8c105-292">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-292">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c105-293">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="8c105-293">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8c105-294">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-294">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8c105-295">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-295">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8c105-296">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="8c105-296">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8c105-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="8c105-297"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c105-298">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-298">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c105-299">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-299">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8c105-300">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c105-300">TypeFilterAttribute</span></span>

<span data-ttu-id="8c105-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-301"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8c105-302">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="8c105-302">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8c105-303">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="8c105-303">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8c105-304">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="8c105-304">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8c105-305">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-305">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8c105-306">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-306">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8c105-307">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="8c105-307">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8c105-308">提供提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-308">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c105-309">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-309">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8c105-310">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="8c105-310">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8c105-311">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="8c105-311">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8c105-312">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-312">Authorization filters</span></span>

<span data-ttu-id="8c105-313">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-313">Authorization filters:</span></span>

* <span data-ttu-id="8c105-314">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-314">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8c105-315">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="8c105-315">Control access to action methods.</span></span>
* <span data-ttu-id="8c105-316">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-316">Have a before method, but no after method.</span></span>

<span data-ttu-id="8c105-317">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="8c105-317">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8c105-318">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-318">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8c105-319">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-319">The built-in authorization filter:</span></span>

* <span data-ttu-id="8c105-320">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="8c105-320">Calls the authorization system.</span></span>
* <span data-ttu-id="8c105-321">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="8c105-321">Does not authorize requests.</span></span>

<span data-ttu-id="8c105-322">不会在授权筛选器中引发异常\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="8c105-322">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8c105-323">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-323">The exception will not be handled.</span></span>
* <span data-ttu-id="8c105-324">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-324">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8c105-325">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="8c105-325">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8c105-326">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="8c105-326">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8c105-327">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-327">Resource filters</span></span>

<span data-ttu-id="8c105-328">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-328">Resource filters:</span></span>

* <span data-ttu-id="8c105-329">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-329">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8c105-330">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="8c105-330">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8c105-331">只有[授权筛选器](#authorization-filters)才会在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-331">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8c105-332">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="8c105-332">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8c105-333">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="8c105-333">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8c105-334">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="8c105-334">Resource filter examples:</span></span>

* <span data-ttu-id="8c105-335">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="8c105-335">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8c105-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="8c105-336">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8c105-337">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="8c105-337">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8c105-338">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="8c105-338">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8c105-339">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-339">Action filters</span></span>

<span data-ttu-id="8c105-340">操作筛选器**不适用于** Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="8c105-340">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="8c105-341">页面支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="8c105-341"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8c105-342">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="8c105-342">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8c105-343">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-343">Action filters:</span></span>

* <span data-ttu-id="8c105-344">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-344">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8c105-345">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-345">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8c105-346">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-346">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c105-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8c105-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="8c105-348"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="8c105-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-349"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8c105-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-350"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8c105-351">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="8c105-351">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8c105-352">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-352">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8c105-353">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="8c105-353">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c105-354"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-354">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8c105-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="8c105-355"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c105-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="8c105-356"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8c105-357">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="8c105-357">Setting this property to null:</span></span>

  * <span data-ttu-id="8c105-358">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-358">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8c105-359">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="8c105-359">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8c105-360">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="8c105-360">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8c105-361">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-361">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8c105-362">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="8c105-362">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8c105-363">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="8c105-363">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8c105-364">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="8c105-364">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8c105-365">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="8c105-365">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8c105-366">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="8c105-366">Validate model state.</span></span>
* <span data-ttu-id="8c105-367">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="8c105-367">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

> [!NOTE]
> <span data-ttu-id="8c105-368">使用特性批注的控制器 `[ApiController]` 自动验证模型状态并返回400响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-368">Controllers annotated with the `[ApiController]` attribute automatically validate model state and return a 400 response.</span></span> <span data-ttu-id="8c105-369">有关详细信息，请参阅[自动 HTTP 400 响应](xref:web-api/index#automatic-http-400-responses)。</span><span class="sxs-lookup"><span data-stu-id="8c105-369">For more information, see [Automatic HTTP 400 responses](xref:web-api/index#automatic-http-400-responses).</span></span>

<span data-ttu-id="8c105-370">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="8c105-370">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8c105-371">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="8c105-371">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8c105-372">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="8c105-372"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c105-373">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="8c105-373"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8c105-374">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="8c105-374">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8c105-375">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-375">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8c105-376">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="8c105-376">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8c105-377">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-377">Exception filters</span></span>

<span data-ttu-id="8c105-378">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-378">Exception filters:</span></span>

* <span data-ttu-id="8c105-379">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="8c105-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="8c105-380">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="8c105-380">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8c105-381">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="8c105-381">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8c105-382">以下代码测试异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-382">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="8c105-383">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-383">Exception filters:</span></span>

* <span data-ttu-id="8c105-384">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="8c105-384">Don't have before and after events.</span></span>
* <span data-ttu-id="8c105-385">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="8c105-385">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8c105-386">处理在 Razor 页或控制器创建、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-386">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8c105-387">不要**捕获资源**筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-387">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8c105-388">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-388">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8c105-389">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-389">This stops propagation of the exception.</span></span> <span data-ttu-id="8c105-390">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="8c105-390">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8c105-391">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="8c105-391">Only an action filter can do that.</span></span>

<span data-ttu-id="8c105-392">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-392">Exception filters:</span></span>

* <span data-ttu-id="8c105-393">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-393">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8c105-394">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="8c105-394">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8c105-395">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-395">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8c105-396">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器\*\*。</span><span class="sxs-lookup"><span data-stu-id="8c105-396">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8c105-397">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-397">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8c105-398">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="8c105-398">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8c105-399">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-399">Result filters</span></span>

<span data-ttu-id="8c105-400">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-400">Result filters:</span></span>

* <span data-ttu-id="8c105-401">实现接口：</span><span class="sxs-lookup"><span data-stu-id="8c105-401">Implement an interface:</span></span>
  * <span data-ttu-id="8c105-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c105-402"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8c105-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c105-403"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8c105-404">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-404">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8c105-405">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c105-405">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8c105-406">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-406">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c105-407">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="8c105-407">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8c105-408">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="8c105-408">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8c105-409">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="8c105-409">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8c105-410">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="8c105-410">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8c105-411">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-411">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8c105-412">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-412">Result filters are not executed when:</span></span>

* <span data-ttu-id="8c105-413">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-413">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8c105-414">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-414">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8c105-415"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-415">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8c105-416">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-416">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8c105-417">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="8c105-417">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="8c105-418">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-418">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8c105-419">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="8c105-419">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c105-420">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="8c105-420">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="8c105-421">如果响应已发送到客户端，则无法更改。</span><span class="sxs-lookup"><span data-stu-id="8c105-421">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="8c105-422">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="8c105-422">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8c105-423">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="8c105-423">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8c105-424">将 `Exception` 设置为 NULL 可有效地处理异常，并防止在管道的后续阶段引发该异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-424">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="8c105-425">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-425">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8c105-426">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-426">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8c105-427">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="8c105-427">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8c105-428">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="8c105-428">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8c105-429">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="8c105-429">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8c105-430">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-430">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8c105-431">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c105-431">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8c105-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="8c105-432">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8c105-433">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="8c105-433">This includes action results produced by:</span></span>

* <span data-ttu-id="8c105-434">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-434">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8c105-435">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-435">Exception filters.</span></span>

<span data-ttu-id="8c105-436">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”\*\* 状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="8c105-436">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8c105-437">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8c105-437">IFilterFactory</span></span>

<span data-ttu-id="8c105-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="8c105-438"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8c105-439">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-439">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8c105-440">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="8c105-440">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8c105-441">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-441">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8c105-442">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="8c105-442">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8c105-443">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-443">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8c105-444">在以下代码中应用了筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-444">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="8c105-445">通过运行[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8c105-445">Test the preceding code by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="8c105-446">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="8c105-446">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8c105-447">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="8c105-447">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8c105-448">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="8c105-448">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8c105-449">**作者：**`Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="8c105-449">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="8c105-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8c105-450">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8c105-451">**内部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="8c105-451">**internal:** `My header`</span></span>

<span data-ttu-id="8c105-452">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="8c105-452">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8c105-453">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8c105-453">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8c105-454">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-454">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8c105-455">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-455">Don't require passing parameters.</span></span>
* <span data-ttu-id="8c105-456">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-456">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8c105-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="8c105-457"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c105-458">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-458">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c105-459">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-459">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8c105-460">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-460">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8c105-461">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-461">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8c105-462">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="8c105-462">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8c105-463">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-463">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8c105-464">但筛选器又不同于中间件，它们是运行时的一部分，这意味着它们有权访问上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="8c105-464">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="8c105-465">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="8c105-465">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8c105-466">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="8c105-466">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8c105-467">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="8c105-467">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8c105-468">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="8c105-468">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8c105-469">后续操作</span><span class="sxs-lookup"><span data-stu-id="8c105-469">Next actions</span></span>

* <span data-ttu-id="8c105-470">请参阅[筛选 Razor 页面的方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="8c105-470">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8c105-471">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="8c105-471">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="8c105-472">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="8c105-472">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="8c105-473">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-473">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="8c105-474">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="8c105-474">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="8c105-475">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="8c105-475">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="8c105-476">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="8c105-476">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="8c105-477">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="8c105-477">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="8c105-478">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="8c105-478">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="8c105-479">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-479">Filters avoid duplicating code.</span></span> <span data-ttu-id="8c105-480">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="8c105-480">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="8c105-481">本文档适用于 Razor 具有视图的页面、API 控制器和控制器。</span><span class="sxs-lookup"><span data-stu-id="8c105-481">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="8c105-482">[查看或下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="8c105-482">[View or download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="8c105-483">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="8c105-483">How filters work</span></span>

<span data-ttu-id="8c105-484">筛选器在 ASP.NET Core 操作调用管道**（有时称为筛选器管道**）内运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-484">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="8c105-485">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-485">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 ASP.NET Core 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="8c105-488">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="8c105-488">Filter types</span></span>

<span data-ttu-id="8c105-489">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="8c105-489">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="8c105-490">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="8c105-490">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="8c105-491">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-491">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="8c105-492">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="8c105-492">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="8c105-493">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-493">Run after authorization.</span></span>  
  * <span data-ttu-id="8c105-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 可以在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-494"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="8c105-495">例如，`OnResourceExecuting` 可以在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-495">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="8c105-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 可以在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-496"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="8c105-497">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-497">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="8c105-498">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="8c105-498">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="8c105-499">页面**不**支持操作筛选器 Razor 。</span><span class="sxs-lookup"><span data-stu-id="8c105-499">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="8c105-500">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="8c105-500">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="8c105-501">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-501">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="8c105-502">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-502">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="8c105-503">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="8c105-503">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="8c105-504">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="8c105-504">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="8c105-507">实现</span><span class="sxs-lookup"><span data-stu-id="8c105-507">Implementation</span></span>

<span data-ttu-id="8c105-508">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="8c105-508">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="8c105-509">同步筛选器可以在其管道阶段之前 (`On-Stage-Executing`) 和之后 (`On-Stage-Executed`) 运行代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-509">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="8c105-510">例如，`OnActionExecuting` 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-510">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="8c105-511">`OnActionExecuted` 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-511">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c105-512">异步筛选器定义 `On-Stage-ExecutionAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-512">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="8c105-513">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="8c105-513">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="8c105-514">每个 `On-Stage-ExecutionAsync` 方法采用执行筛选器的管道阶段的 `FilterType-ExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="8c105-514">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="8c105-515">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="8c105-515">Multiple filter stages</span></span>

<span data-ttu-id="8c105-516">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-516">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="8c105-517">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类实现 `IActionFilter`、`IResultFilter` 及其异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-517">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="8c105-518">筛选器接口的同步和异步版本任意实现一个，而不是同时实现   。</span><span class="sxs-lookup"><span data-stu-id="8c105-518">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="8c105-519">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-519">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="8c105-520">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-520">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="8c105-521">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-521">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="8c105-522">使用抽象类时（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>），将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-522">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="8c105-523">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="8c105-523">Built-in filter attributes</span></span>

<span data-ttu-id="8c105-524">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-524">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="8c105-525">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="8c105-525">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="8c105-526">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="8c105-526">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="8c105-527">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="8c105-527">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="8c105-528">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="8c105-528">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="8c105-529">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-529">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="8c105-530">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="8c105-530">Filter scopes and order of execution</span></span>

<span data-ttu-id="8c105-531">可以将筛选器添加到管道中的以下三个*范围*之一：</span><span class="sxs-lookup"><span data-stu-id="8c105-531">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="8c105-532">在操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-532">Using an attribute on an action.</span></span>
* <span data-ttu-id="8c105-533">在控制器上使用属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-533">Using an attribute on a controller.</span></span>
* <span data-ttu-id="8c105-534">所有控制器和操作的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="8c105-534">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="8c105-535">前面的代码使用 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 集合全局添加三个筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-535">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="8c105-536">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="8c105-536">Default order of execution</span></span>

<span data-ttu-id="8c105-537">当有同一类型的多个筛选器时，作用域可确定筛选器执行的默认顺序\*\*。</span><span class="sxs-lookup"><span data-stu-id="8c105-537">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="8c105-538">全局筛选器涵盖类筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-538">Global filters surround class filters.</span></span> <span data-ttu-id="8c105-539">类筛选器涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-539">Class filters surround method filters.</span></span>

<span data-ttu-id="8c105-540">在筛选器嵌套模式下，筛选器的 after\*\* 代码会按照与 before\*\* 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-540">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="8c105-541">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="8c105-541">The filter sequence:</span></span>

* <span data-ttu-id="8c105-542">全局筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-542">The *before* code of global filters.</span></span>
  * <span data-ttu-id="8c105-543">控制器筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-543">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="8c105-544">操作方法筛选器的 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-544">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="8c105-545">操作方法筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-545">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="8c105-546">控制器筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-546">The *after* code of controller filters.</span></span>
* <span data-ttu-id="8c105-547">全局筛选器的 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-547">The *after* code of global filters.</span></span>
  
<span data-ttu-id="8c105-548">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="8c105-548">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="8c105-549">序列</span><span class="sxs-lookup"><span data-stu-id="8c105-549">Sequence</span></span> | <span data-ttu-id="8c105-550">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="8c105-550">Filter scope</span></span> | <span data-ttu-id="8c105-551">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="8c105-551">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="8c105-552">1</span><span class="sxs-lookup"><span data-stu-id="8c105-552">1</span></span> | <span data-ttu-id="8c105-553">全球</span><span class="sxs-lookup"><span data-stu-id="8c105-553">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c105-554">2</span><span class="sxs-lookup"><span data-stu-id="8c105-554">2</span></span> | <span data-ttu-id="8c105-555">控制器</span><span class="sxs-lookup"><span data-stu-id="8c105-555">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c105-556">3</span><span class="sxs-lookup"><span data-stu-id="8c105-556">3</span></span> | <span data-ttu-id="8c105-557">方法</span><span class="sxs-lookup"><span data-stu-id="8c105-557">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c105-558">4</span><span class="sxs-lookup"><span data-stu-id="8c105-558">4</span></span> | <span data-ttu-id="8c105-559">方法</span><span class="sxs-lookup"><span data-stu-id="8c105-559">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c105-560">5</span><span class="sxs-lookup"><span data-stu-id="8c105-560">5</span></span> | <span data-ttu-id="8c105-561">控制器</span><span class="sxs-lookup"><span data-stu-id="8c105-561">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="8c105-562">6</span><span class="sxs-lookup"><span data-stu-id="8c105-562">6</span></span> | <span data-ttu-id="8c105-563">全球</span><span class="sxs-lookup"><span data-stu-id="8c105-563">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="8c105-564">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="8c105-564">This sequence shows:</span></span>

* <span data-ttu-id="8c105-565">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="8c105-565">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="8c105-566">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="8c105-566">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="8c105-567">控制器和 Razor 页级筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-567">Controller and Razor Page level filters</span></span>

<span data-ttu-id="8c105-568">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-568">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="8c105-569">这些方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-569">These methods:</span></span>

* <span data-ttu-id="8c105-570">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-570">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="8c105-571">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-571">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="8c105-572">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-572">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="8c105-573">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="8c105-573">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="8c105-574">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-574">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="8c105-575">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="8c105-575">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="8c105-576">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="8c105-576">The `TestController`:</span></span>

* <span data-ttu-id="8c105-577">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="8c105-577">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="8c105-578">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="8c105-578">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="8c105-579">导航到 `https://localhost:5001/Test/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="8c105-579">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="8c105-580">有关 Razor 页面，请[参阅 Razor 通过重写筛选器方法实现页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="8c105-580">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="8c105-581">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="8c105-581">Overriding the default order</span></span>

<span data-ttu-id="8c105-582">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="8c105-582">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="8c105-583">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="8c105-583">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="8c105-584">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-584">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="8c105-585">在具有较高的 `Order` 值的筛选器之前运行 before\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-585">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="8c105-586">在具有较高的 `Order` 值的筛选器之后运行 after\*\* 代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-586">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="8c105-587">可以使用构造函数参数设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-587">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="8c105-588">请考虑前面示例中所示的 3 个相同操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-588">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="8c105-589">如果控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="8c105-589">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="8c105-590">序列</span><span class="sxs-lookup"><span data-stu-id="8c105-590">Sequence</span></span> | <span data-ttu-id="8c105-591">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="8c105-591">Filter scope</span></span> | <span data-ttu-id="8c105-592">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="8c105-592">`Order` property</span></span> | <span data-ttu-id="8c105-593">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="8c105-593">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="8c105-594">1</span><span class="sxs-lookup"><span data-stu-id="8c105-594">1</span></span> | <span data-ttu-id="8c105-595">方法</span><span class="sxs-lookup"><span data-stu-id="8c105-595">Method</span></span> | <span data-ttu-id="8c105-596">0</span><span class="sxs-lookup"><span data-stu-id="8c105-596">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="8c105-597">2</span><span class="sxs-lookup"><span data-stu-id="8c105-597">2</span></span> | <span data-ttu-id="8c105-598">控制器</span><span class="sxs-lookup"><span data-stu-id="8c105-598">Controller</span></span> | <span data-ttu-id="8c105-599">1</span><span class="sxs-lookup"><span data-stu-id="8c105-599">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8c105-600">3</span><span class="sxs-lookup"><span data-stu-id="8c105-600">3</span></span> | <span data-ttu-id="8c105-601">全球</span><span class="sxs-lookup"><span data-stu-id="8c105-601">Global</span></span> | <span data-ttu-id="8c105-602">2</span><span class="sxs-lookup"><span data-stu-id="8c105-602">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="8c105-603">4</span><span class="sxs-lookup"><span data-stu-id="8c105-603">4</span></span> | <span data-ttu-id="8c105-604">全球</span><span class="sxs-lookup"><span data-stu-id="8c105-604">Global</span></span> | <span data-ttu-id="8c105-605">2</span><span class="sxs-lookup"><span data-stu-id="8c105-605">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8c105-606">5</span><span class="sxs-lookup"><span data-stu-id="8c105-606">5</span></span> | <span data-ttu-id="8c105-607">控制器</span><span class="sxs-lookup"><span data-stu-id="8c105-607">Controller</span></span> | <span data-ttu-id="8c105-608">1</span><span class="sxs-lookup"><span data-stu-id="8c105-608">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="8c105-609">6</span><span class="sxs-lookup"><span data-stu-id="8c105-609">6</span></span> | <span data-ttu-id="8c105-610">方法</span><span class="sxs-lookup"><span data-stu-id="8c105-610">Method</span></span> | <span data-ttu-id="8c105-611">0</span><span class="sxs-lookup"><span data-stu-id="8c105-611">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="8c105-612">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="8c105-612">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="8c105-613">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="8c105-613">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="8c105-614">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="8c105-614">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="8c105-615">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="8c105-615">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="8c105-616">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="8c105-616">Cancellation and short-circuiting</span></span>

<span data-ttu-id="8c105-617">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-617">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="8c105-618">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="8c105-618">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="8c105-619">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="8c105-619">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="8c105-620">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="8c105-620">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="8c105-621">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-621">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="8c105-622">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="8c105-622">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="8c105-623">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-623">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="8c105-624">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="8c105-624">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="8c105-625">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-625">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="8c105-626">依赖项注入</span><span class="sxs-lookup"><span data-stu-id="8c105-626">Dependency injection</span></span>

<span data-ttu-id="8c105-627">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-627">Filters can be added by type or by instance.</span></span> <span data-ttu-id="8c105-628">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="8c105-628">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="8c105-629">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-629">If a type is added, it's type-activated.</span></span> <span data-ttu-id="8c105-630">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="8c105-630">A type-activated filter means:</span></span>

* <span data-ttu-id="8c105-631">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-631">An instance is created for each request.</span></span>
* <span data-ttu-id="8c105-632">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-632">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="8c105-633">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-633">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="8c105-634">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="8c105-634">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="8c105-635">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-635">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="8c105-636">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="8c105-636">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="8c105-637">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="8c105-637">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="8c105-638">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="8c105-638"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="8c105-639">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-639">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="8c105-640">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="8c105-640">Loggers are available from DI.</span></span> <span data-ttu-id="8c105-641">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="8c105-641">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="8c105-642">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="8c105-642">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="8c105-643">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="8c105-643">Logging added to filters:</span></span>

* <span data-ttu-id="8c105-644">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="8c105-644">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="8c105-645">不应记录操作或其他框架事件\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="8c105-645">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="8c105-646">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="8c105-646">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="8c105-647">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c105-647">ServiceFilterAttribute</span></span>

<span data-ttu-id="8c105-648">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-648">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="8c105-649"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-649">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="8c105-650">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="8c105-650">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c105-651">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="8c105-651">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="8c105-652">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="8c105-652">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="8c105-653">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="8c105-653">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="8c105-654">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-654">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c105-655">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="8c105-655">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="8c105-656">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-656">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="8c105-657">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-657">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="8c105-658">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="8c105-658">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="8c105-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="8c105-659"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c105-660">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-660">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c105-661">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-661">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="8c105-662">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="8c105-662">TypeFilterAttribute</span></span>

<span data-ttu-id="8c105-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-663"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="8c105-664">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="8c105-664">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="8c105-665">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="8c105-665">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="8c105-666">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="8c105-666">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="8c105-667">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-667">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="8c105-668">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-668">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="8c105-669">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="8c105-669">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="8c105-670">提供提示：筛选器实例可能在其创建的请求范围之外被重用。\*\*</span><span class="sxs-lookup"><span data-stu-id="8c105-670">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="8c105-671">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-671">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="8c105-672">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="8c105-672">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="8c105-673">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="8c105-673">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="8c105-674">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-674">Authorization filters</span></span>

<span data-ttu-id="8c105-675">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-675">Authorization filters:</span></span>

* <span data-ttu-id="8c105-676">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-676">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="8c105-677">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="8c105-677">Control access to action methods.</span></span>
* <span data-ttu-id="8c105-678">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-678">Have a before method, but no after method.</span></span>

<span data-ttu-id="8c105-679">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="8c105-679">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="8c105-680">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-680">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="8c105-681">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-681">The built-in authorization filter:</span></span>

* <span data-ttu-id="8c105-682">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="8c105-682">Calls the authorization system.</span></span>
* <span data-ttu-id="8c105-683">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="8c105-683">Does not authorize requests.</span></span>

<span data-ttu-id="8c105-684">不会在授权筛选器中引发异常\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="8c105-684">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="8c105-685">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-685">The exception will not be handled.</span></span>
* <span data-ttu-id="8c105-686">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-686">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="8c105-687">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="8c105-687">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="8c105-688">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="8c105-688">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="8c105-689">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-689">Resource filters</span></span>

<span data-ttu-id="8c105-690">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-690">Resource filters:</span></span>

* <span data-ttu-id="8c105-691">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-691">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="8c105-692">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="8c105-692">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="8c105-693">只有[授权筛选器](#authorization-filters)才会在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="8c105-693">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="8c105-694">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="8c105-694">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="8c105-695">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="8c105-695">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="8c105-696">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="8c105-696">Resource filter examples:</span></span>

* <span data-ttu-id="8c105-697">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="8c105-697">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="8c105-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="8c105-698">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="8c105-699">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="8c105-699">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="8c105-700">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="8c105-700">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="8c105-701">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-701">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="8c105-702">操作筛选器**不适用于** Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="8c105-702">Action filters do **not** apply to Razor Pages.</span></span> Razor<span data-ttu-id="8c105-703">页面支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> 。</span><span class="sxs-lookup"><span data-stu-id="8c105-703"> Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="8c105-704">有关详细信息，请参阅 [Razor Pages 的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="8c105-704">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="8c105-705">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-705">Action filters:</span></span>

* <span data-ttu-id="8c105-706">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="8c105-706">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="8c105-707">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-707">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="8c105-708">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-708">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="8c105-709"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-709">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="8c105-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="8c105-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="8c105-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-711"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="8c105-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-712"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="8c105-713">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="8c105-713">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="8c105-714">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-714">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="8c105-715">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="8c105-715">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c105-716"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="8c105-716">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="8c105-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="8c105-717"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c105-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="8c105-718"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="8c105-719">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="8c105-719">Setting this property to null:</span></span>

  * <span data-ttu-id="8c105-720">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-720">Effectively handles the exception.</span></span>
  * <span data-ttu-id="8c105-721">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="8c105-721">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="8c105-722">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="8c105-722">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="8c105-723">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-723">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="8c105-724">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="8c105-724">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="8c105-725">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="8c105-725">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="8c105-726">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="8c105-726">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="8c105-727">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="8c105-727">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="8c105-728">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="8c105-728">Validate model state.</span></span>
* <span data-ttu-id="8c105-729">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="8c105-729">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="8c105-730">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="8c105-730">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="8c105-731">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="8c105-731">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="8c105-732">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="8c105-732"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="8c105-733">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="8c105-733"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="8c105-734">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="8c105-734">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="8c105-735">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-735">Effectively handles an exception.</span></span>
  * <span data-ttu-id="8c105-736">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="8c105-736">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="8c105-737">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-737">Exception filters</span></span>

<span data-ttu-id="8c105-738">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-738">Exception filters:</span></span>

* <span data-ttu-id="8c105-739">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="8c105-739">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="8c105-740">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="8c105-740">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="8c105-741">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="8c105-741">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="8c105-742">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-742">Exception filters:</span></span>

* <span data-ttu-id="8c105-743">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="8c105-743">Don't have before and after events.</span></span>
* <span data-ttu-id="8c105-744">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="8c105-744">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="8c105-745">处理在 Razor 页或控制器创建、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-745">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="8c105-746">不要**捕获资源**筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-746">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="8c105-747">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-747">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="8c105-748">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-748">This stops propagation of the exception.</span></span> <span data-ttu-id="8c105-749">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="8c105-749">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="8c105-750">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="8c105-750">Only an action filter can do that.</span></span>

<span data-ttu-id="8c105-751">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-751">Exception filters:</span></span>

* <span data-ttu-id="8c105-752">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-752">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="8c105-753">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="8c105-753">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="8c105-754">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-754">Prefer middleware for exception handling.</span></span> <span data-ttu-id="8c105-755">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器\*\*。</span><span class="sxs-lookup"><span data-stu-id="8c105-755">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="8c105-756">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-756">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="8c105-757">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="8c105-757">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="8c105-758">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="8c105-758">Result filters</span></span>

<span data-ttu-id="8c105-759">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-759">Result filters:</span></span>

* <span data-ttu-id="8c105-760">实现接口：</span><span class="sxs-lookup"><span data-stu-id="8c105-760">Implement an interface:</span></span>
  * <span data-ttu-id="8c105-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c105-761"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="8c105-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="8c105-762"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="8c105-763">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-763">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="8c105-764">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c105-764">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="8c105-765">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-765">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="8c105-766">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="8c105-766">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="8c105-767">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="8c105-767">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="8c105-768">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="8c105-768">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="8c105-769">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="8c105-769">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="8c105-770">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-770">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="8c105-771">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-771">Result filters are not executed when:</span></span>

* <span data-ttu-id="8c105-772">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-772">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="8c105-773">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-773">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="8c105-774"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="8c105-774">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="8c105-775">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-775">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="8c105-776">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="8c105-776">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="8c105-777">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-777">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="8c105-778">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="8c105-778">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="8c105-779">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="8c105-779">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="8c105-780">如果响应已发送到客户端，则无法再更改。</span><span class="sxs-lookup"><span data-stu-id="8c105-780">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="8c105-781">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="8c105-781">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="8c105-782">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="8c105-782">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="8c105-783">将 `Exception` 设置为 NULL 可有效地处理异常，并防止 ASP.NET Core 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="8c105-783">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="8c105-784">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="8c105-784">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="8c105-785">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="8c105-785">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="8c105-786">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="8c105-786">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="8c105-787">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="8c105-787">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="8c105-788">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="8c105-788">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="8c105-789">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="8c105-789">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="8c105-790">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="8c105-790">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="8c105-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="8c105-791">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="8c105-792">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="8c105-792">This includes action results produced by:</span></span>

* <span data-ttu-id="8c105-793">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-793">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="8c105-794">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="8c105-794">Exception filters.</span></span>

<span data-ttu-id="8c105-795">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”\*\* 状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="8c105-795">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="8c105-796">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8c105-796">IFilterFactory</span></span>

<span data-ttu-id="8c105-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="8c105-797"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="8c105-798">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-798">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="8c105-799">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="8c105-799">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="8c105-800">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="8c105-800">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="8c105-801">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="8c105-801">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="8c105-802">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-802">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="8c105-803">可以通过运行[下载示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="8c105-803">The preceding code can be tested by running the [download sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="8c105-804">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="8c105-804">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="8c105-805">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="8c105-805">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="8c105-806">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="8c105-806">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="8c105-807">**作者：**`Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="8c105-807">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="8c105-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="8c105-808">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="8c105-809">**内部：**`My header`</span><span class="sxs-lookup"><span data-stu-id="8c105-809">**internal:** `My header`</span></span>

<span data-ttu-id="8c105-810">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="8c105-810">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="8c105-811">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="8c105-811">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="8c105-812">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="8c105-812">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="8c105-813">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="8c105-813">Don't require passing parameters.</span></span>
* <span data-ttu-id="8c105-814">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="8c105-814">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="8c105-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="8c105-815"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="8c105-816">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-816">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="8c105-817">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="8c105-817">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="8c105-818">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="8c105-818">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="8c105-819">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="8c105-819">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="8c105-820">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="8c105-820">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="8c105-821">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="8c105-821">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="8c105-822">但筛选器又不同于中间件，它们是 ASP.NET Core 运行时的一部分，这意味着它们有权访问 ASP.NET Core 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="8c105-822">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="8c105-823">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="8c105-823">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="8c105-824">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="8c105-824">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="8c105-825">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="8c105-825">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="8c105-826">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="8c105-826">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="8c105-827">后续操作</span><span class="sxs-lookup"><span data-stu-id="8c105-827">Next actions</span></span>

* <span data-ttu-id="8c105-828">请参阅[筛选 Razor 页面的方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="8c105-828">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="8c105-829">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="8c105-829">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
