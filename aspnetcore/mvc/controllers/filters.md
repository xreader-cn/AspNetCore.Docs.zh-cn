---
title: ASP.NET Core 中的筛选器
author: Rick-Anderson
description: 了解筛选器的工作原理以及如何在 ASP.NET Core 中使用它们。
ms.author: riande
ms.custom: mvc
ms.date: 1/1/2020
uid: mvc/controllers/filters
ms.openlocfilehash: 759c150e7f35f3f6a52947edc5ef41448dc227fe
ms.sourcegitcommit: 7dfe6cc8408ac6a4549c29ca57b0c67ec4baa8de
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/09/2020
ms.locfileid: "75828966"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="1edb7-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-103">Filters in ASP.NET Core</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="1edb7-104">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1edb7-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1edb7-105">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。 </span><span class="sxs-lookup"><span data-stu-id="1edb7-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="1edb7-106">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="1edb7-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="1edb7-107">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="1edb7-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="1edb7-108">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="1edb7-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="1edb7-109">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="1edb7-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="1edb7-110">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="1edb7-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="1edb7-111">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="1edb7-112">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="1edb7-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="1edb7-113">本文档适用于 Razor Pages、API 控制器和具有视图的控制器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="1edb7-114">[查看或下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="1edb7-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="1edb7-115">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="1edb7-115">How filters work</span></span>

<span data-ttu-id="1edb7-116">筛选器在 ASP.NET Core 操作调用管道  （有时称为筛选器管道  ）内运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="1edb7-117">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="1edb7-120">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="1edb7-120">Filter types</span></span>

<span data-ttu-id="1edb7-121">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="1edb7-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="1edb7-122">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="1edb7-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="1edb7-123">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-123">Authorization filters short-circuit the pipeline if the request is not authorized.</span></span>

* <span data-ttu-id="1edb7-124">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="1edb7-125">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-125">Run after authorization.</span></span>  
  * <span data-ttu-id="1edb7-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> runs code before the rest of the filter pipeline.</span></span> <span data-ttu-id="1edb7-127">例如，`OnResourceExecuting` 在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-127">For example, `OnResourceExecuting` runs code before model binding.</span></span>
  * <span data-ttu-id="1edb7-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> runs code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="1edb7-129">[操作筛选器](#action-filters)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-129">[Action filters](#action-filters):</span></span>

  * <span data-ttu-id="1edb7-130">在调用操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-130">Run code immediately before and after an action method is called.</span></span>
  * <span data-ttu-id="1edb7-131">可以更改传递到操作中的参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-131">Can change the arguments passed into an action.</span></span>
  * <span data-ttu-id="1edb7-132">可以更改从操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="1edb7-132">Can change the result returned from the action.</span></span>
  * <span data-ttu-id="1edb7-133">不可在 Razor Pages 中使用  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-133">Are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="1edb7-134">[异常筛选器](#exception-filters)在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="1edb7-134">[Exception filters](#exception-filters) apply global policies to unhandled exceptions that occur before the response body has been written to.</span></span>

* <span data-ttu-id="1edb7-135">[结果筛选器](#result-filters)在执行操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-135">[Result filters](#result-filters) run code immediately before and after the execution of action results.</span></span> <span data-ttu-id="1edb7-136">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-136">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="1edb7-137">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-137">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="1edb7-138">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="1edb7-138">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="1edb7-141">实现</span><span class="sxs-lookup"><span data-stu-id="1edb7-141">Implementation</span></span>

<span data-ttu-id="1edb7-142">通过不同的接口定义，筛选器同时支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="1edb7-142">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="1edb7-143">同步筛选器在其管道阶段之前和之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-143">Synchronous filters run code before and after their pipeline stage.</span></span> <span data-ttu-id="1edb7-144">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-144">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*> is called before the action method is called.</span></span> <span data-ttu-id="1edb7-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-145"><xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*> is called after the action method returns.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="1edb7-146">异步筛选器定义 `On-Stage-ExecutionAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-146">Asynchronous filters define an `On-Stage-ExecutionAsync` method.</span></span> <span data-ttu-id="1edb7-147">例如，<xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>：</span><span class="sxs-lookup"><span data-stu-id="1edb7-147">For example, <xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*>:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="1edb7-148">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-148">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`) that executes the action method.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="1edb7-149">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="1edb7-149">Multiple filter stages</span></span>

<span data-ttu-id="1edb7-150">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-150">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="1edb7-151">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类可实现：</span><span class="sxs-lookup"><span data-stu-id="1edb7-151">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements:</span></span>

* <span data-ttu-id="1edb7-152">同步：<xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span><span class="sxs-lookup"><span data-stu-id="1edb7-152">Synchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> and  <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter></span></span>
* <span data-ttu-id="1edb7-153">异步：<xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="1edb7-153">Asynchronous: <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
* <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>

<span data-ttu-id="1edb7-154">筛选器接口的同步和异步版本任意实现一个，而不是同时实现   。</span><span class="sxs-lookup"><span data-stu-id="1edb7-154">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="1edb7-155">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-155">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="1edb7-156">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-156">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="1edb7-157">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-157">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="1edb7-158">使用抽象类（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>）时，将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-158">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>, override only the synchronous methods or the asynchronous method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="1edb7-159">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="1edb7-159">Built-in filter attributes</span></span>

<span data-ttu-id="1edb7-160">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-160">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="1edb7-161">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="1edb7-161">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-162">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="1edb7-162">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="1edb7-163">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="1edb7-163">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="1edb7-164">使用[浏览器开发人员工具](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools)等工具来检查标头。</span><span class="sxs-lookup"><span data-stu-id="1edb7-164">Use a tool such as the [browser developer tools](https://developer.mozilla.org/docs/Learn/Common_questions/What_are_browser_developer_tools) to examine the headers.</span></span> <span data-ttu-id="1edb7-165">在响应标头下，将显示 `author: Rick Anderson`  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-165">Under **Response Headers**, `author: Rick Anderson` is displayed.</span></span>

<span data-ttu-id="1edb7-166">以下代码实现了 `ActionFilterAttribute`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-166">The following code implements an `ActionFilterAttribute` that:</span></span>

* <span data-ttu-id="1edb7-167">从配置系统读取标题和名称。</span><span class="sxs-lookup"><span data-stu-id="1edb7-167">Reads the title and name from the configuration system.</span></span> <span data-ttu-id="1edb7-168">与前面的示例不同，以下代码不需要将筛选器参数添加到代码中。</span><span class="sxs-lookup"><span data-stu-id="1edb7-168">Unlike the previous sample, the following code doesn't require filter parameters to be added to the code.</span></span>
* <span data-ttu-id="1edb7-169">将标题和名称添加到响应标头。</span><span class="sxs-lookup"><span data-stu-id="1edb7-169">Adds the title and name to the response header.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyActionFilterAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-170">使用[选项模式](xref:fundamentals/configuration/options)从[配置系统](xref:fundamentals/configuration/index)中提供配置选项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-170">The configuration options are provided from the [configuration system](xref:fundamentals/configuration/index) using the [options pattern](xref:fundamentals/configuration/options).</span></span> <span data-ttu-id="1edb7-171">例如，从 appsettings.json 文件中： </span><span class="sxs-lookup"><span data-stu-id="1edb7-171">For example, from the *appsettings.json* file:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/appsettings.json)]

<span data-ttu-id="1edb7-172">在 `StartUp.ConfigureServices` 中：</span><span class="sxs-lookup"><span data-stu-id="1edb7-172">In the `StartUp.ConfigureServices`:</span></span>

* <span data-ttu-id="1edb7-173">`PositionOptions` 类已通过 `"Position"` 配置区域添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-173">The `PositionOptions` class is added to the service container with the `"Position"` configuration area.</span></span>
* <span data-ttu-id="1edb7-174">`MyActionFilterAttribute` 已添加到服务容器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-174">The `MyActionFilterAttribute` is added to the service container.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupAF.cs?name=snippet)]

<span data-ttu-id="1edb7-175">以下代码显示 `PositionOptions` 类：</span><span class="sxs-lookup"><span data-stu-id="1edb7-175">The following code shows the `PositionOptions` class:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Helper/PositionOptions.cs?name=snippet)]

<span data-ttu-id="1edb7-176">以下代码将 `MyActionFilterAttribute` 应用于 `Index2` 方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-176">The following code applies the `MyActionFilterAttribute` to the `Index2` method:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet2&highlight=9)]

<span data-ttu-id="1edb7-177">在响应标头下，`author: Rick Anderson` 和 `Editor: Joe Smith` 在调用 `Sample/Index2` 终结点时显示  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-177">Under **Response Headers**, `author: Rick Anderson`, and `Editor: Joe Smith` is displayed when the `Sample/Index2` endpoint is called.</span></span>

<span data-ttu-id="1edb7-178">以下代码将 `MyActionFilterAttribute` 和 `AddHeaderAttribute` 应用于 Razor 页面：</span><span class="sxs-lookup"><span data-stu-id="1edb7-178">The following code applies the `MyActionFilterAttribute` and the `AddHeaderAttribute` to the Razor Page:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Pages/Movies/Index.cshtml.cs?name=snippet)]

<span data-ttu-id="1edb7-179">无法将筛选器应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-179">Filters cannot be applied to Razor Page handler methods.</span></span> <span data-ttu-id="1edb7-180">它们可以应用于 Razor 页面模型或全局应用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-180">They can be applied either to the Razor Page model or globally.</span></span>

<span data-ttu-id="1edb7-181">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="1edb7-181">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="1edb7-182">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-182">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="1edb7-183">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="1edb7-183">Filter scopes and order of execution</span></span>

<span data-ttu-id="1edb7-184">可以将筛选器添加到管道中的三个作用域  之一：</span><span class="sxs-lookup"><span data-stu-id="1edb7-184">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="1edb7-185">在控制器操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-185">Using an attribute on a controller action.</span></span> <span data-ttu-id="1edb7-186">无法将筛选器属性应用于 Razor 页面处理程序方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-186">Filter attributes cannot be applied to Razor Pages handler methods.</span></span>
* <span data-ttu-id="1edb7-187">在控制器或 Razor 页面上使用属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-187">Using an attribute on a controller or Razor Page.</span></span>
* <span data-ttu-id="1edb7-188">所有控制器、操作和 Razor 页面的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="1edb7-188">Globally for all controllers, actions, and Razor Pages as shown in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

### <a name="default-order-of-execution"></a><span data-ttu-id="1edb7-189">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="1edb7-189">Default order of execution</span></span>

<span data-ttu-id="1edb7-190">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="1edb7-190">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="1edb7-191">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-191">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="1edb7-192">在筛选器嵌套模式下，筛选器的 after  代码会按照与 before  代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-192">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="1edb7-193">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="1edb7-193">The filter sequence:</span></span>

* <span data-ttu-id="1edb7-194">全局筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-194">The *before* code of global filters.</span></span>
  * <span data-ttu-id="1edb7-195">控制器筛选器和 Razor 页面筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-195">The *before* code of controller and Razor Page filters.</span></span>
    * <span data-ttu-id="1edb7-196">操作方法筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-196">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="1edb7-197">操作方法筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-197">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="1edb7-198">控制器筛选器和 Razor 页面筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-198">The *after* code of controller and Razor Page filters.</span></span>
* <span data-ttu-id="1edb7-199">全局筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-199">The *after* code of global filters.</span></span>
  
<span data-ttu-id="1edb7-200">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="1edb7-200">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="1edb7-201">序列</span><span class="sxs-lookup"><span data-stu-id="1edb7-201">Sequence</span></span> | <span data-ttu-id="1edb7-202">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="1edb7-202">Filter scope</span></span> | <span data-ttu-id="1edb7-203">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-203">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="1edb7-204">1</span><span class="sxs-lookup"><span data-stu-id="1edb7-204">1</span></span> | <span data-ttu-id="1edb7-205">Global</span><span class="sxs-lookup"><span data-stu-id="1edb7-205">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="1edb7-206">2</span><span class="sxs-lookup"><span data-stu-id="1edb7-206">2</span></span> | <span data-ttu-id="1edb7-207">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="1edb7-207">Controller or Razor Page</span></span>| `OnActionExecuting` |
| <span data-ttu-id="1edb7-208">3</span><span class="sxs-lookup"><span data-stu-id="1edb7-208">3</span></span> | <span data-ttu-id="1edb7-209">方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-209">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="1edb7-210">4</span><span class="sxs-lookup"><span data-stu-id="1edb7-210">4</span></span> | <span data-ttu-id="1edb7-211">方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-211">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="1edb7-212">5</span><span class="sxs-lookup"><span data-stu-id="1edb7-212">5</span></span> | <span data-ttu-id="1edb7-213">控制器或 Razor 页面</span><span class="sxs-lookup"><span data-stu-id="1edb7-213">Controller or Razor Page</span></span> | `OnActionExecuted` |
| <span data-ttu-id="1edb7-214">6</span><span class="sxs-lookup"><span data-stu-id="1edb7-214">6</span></span> | <span data-ttu-id="1edb7-215">Global</span><span class="sxs-lookup"><span data-stu-id="1edb7-215">Global</span></span> | `OnActionExecuted` |

### <a name="controller-level-filters"></a><span data-ttu-id="1edb7-216">控制器级别筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-216">Controller level filters</span></span>

<span data-ttu-id="1edb7-217">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-217">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="1edb7-218">这些方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-218">These methods:</span></span>

* <span data-ttu-id="1edb7-219">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-219">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="1edb7-220">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-220">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="1edb7-221">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-221">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="1edb7-222">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-222">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="1edb7-223">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-223">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="1edb7-224">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-224">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="1edb7-225">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-225">The `TestController`:</span></span>

* <span data-ttu-id="1edb7-226">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="1edb7-226">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="1edb7-227">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-227">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<!-- test via  webBuilder.UseStartup<Startup>(); -->

<span data-ttu-id="1edb7-228">导航到 `https://localhost:5001/Test2/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="1edb7-228">Navigating to `https://localhost:5001/Test2/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="1edb7-229">控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-229">Controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue`.</span></span> <span data-ttu-id="1edb7-230">控制器级别筛选器无法设置为在将筛选器应用于方法之后运行  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-230">Controller level filters can **not** be set to run after filters applied to methods.</span></span> <span data-ttu-id="1edb7-231">在下一节对 Order 进行了介绍。</span><span class="sxs-lookup"><span data-stu-id="1edb7-231">Order is explained in the next section.</span></span>

<span data-ttu-id="1edb7-232">对于 Razor Pages，请参阅[通过重写筛选器方法实现 Razor 页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-232">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="1edb7-233">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="1edb7-233">Overriding the default order</span></span>

<span data-ttu-id="1edb7-234">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="1edb7-234">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="1edb7-235">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="1edb7-235">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="1edb7-236">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-236">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="1edb7-237">在具有较高的 `Order` 值的筛选器之前运行 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-237">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="1edb7-238">在具有较高的 `Order` 值的筛选器之后运行 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-238">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="1edb7-239">使用构造函数参数设置了 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-239">The `Order` property is set with a constructor parameter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test3Controller.cs?name=snippet)]

<span data-ttu-id="1edb7-240">请考虑以下控制器中的两个操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-240">Consider the two action filters in the following controller:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet)]

<span data-ttu-id="1edb7-241">在 `StartUp.ConfigureServices` 中添加了全局筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-241">A global filter is added in `StartUp.ConfigureServices`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder.cs?name=snippet)]

<span data-ttu-id="1edb7-242">3 个筛选器按下列顺序运行：</span><span class="sxs-lookup"><span data-stu-id="1edb7-242">The 3 filters run in the following order:</span></span>

* `Test2Controller.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `MyAction2FilterAttribute.OnActionExecuting`
      * `Test2Controller.FilterTest2`
    * `MySampleActionFilter.OnActionExecuted`
  * `MyAction2FilterAttribute.OnResultExecuting`
* `Test2Controller.OnActionExecuted`

<span data-ttu-id="1edb7-243">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="1edb7-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="1edb7-244">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="1edb7-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="1edb7-245">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="1edb7-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="1edb7-246">如前所述，控制器级别筛选器将 [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) 属性设置为 `int.MinValue`。对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-246">As mentioned previously, controller level filters set the [Order](https://github.com/dotnet/AspNetCore/blob/master/src/Mvc/Mvc.Core/src/Filters/ControllerActionFilter.cs#L15-L17) property to `int.MinValue` For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

<span data-ttu-id="1edb7-247">在前面的代码中，`MySampleActionFilter` 具有全局作用域，因此它在具有控制器作用域的 `MyAction2FilterAttribute` 之前运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-247">In the preceding code, `MySampleActionFilter` has global scope so it runs before `MyAction2FilterAttribute`, which has controller scope.</span></span> <span data-ttu-id="1edb7-248">若要首先运行 `MyAction2FilterAttribute`，请将顺序设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-248">To make `MyAction2FilterAttribute` run first, set the order to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/Test2Controller.cs?name=snippet2)]

<span data-ttu-id="1edb7-249">若要首先运行全局筛选器 `MySampleActionFilter`，请将 `Order` 设置为 `int.MinValue`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-249">To make the global filter `MySampleActionFilter` run first, set `Order` to `int.MinValue`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/StartupOrder2.cs?name=snippet&highlight=6)]

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="1edb7-250">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="1edb7-250">Cancellation and short-circuiting</span></span>

<span data-ttu-id="1edb7-251">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-251">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="1edb7-252">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="1edb7-252">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-253">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="1edb7-253">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="1edb7-254">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-254">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="1edb7-255">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-255">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="1edb7-256">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="1edb7-256">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="1edb7-257">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-257">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="1edb7-258">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="1edb7-258">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="1edb7-259">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-259">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

## <a name="dependency-injection"></a><span data-ttu-id="1edb7-260">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="1edb7-260">Dependency injection</span></span>

<span data-ttu-id="1edb7-261">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-261">Filters can be added by type or by instance.</span></span> <span data-ttu-id="1edb7-262">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="1edb7-262">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="1edb7-263">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-263">If a type is added, it's type-activated.</span></span> <span data-ttu-id="1edb7-264">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="1edb7-264">A type-activated filter means:</span></span>

* <span data-ttu-id="1edb7-265">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-265">An instance is created for each request.</span></span>
* <span data-ttu-id="1edb7-266">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-266">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="1edb7-267">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-267">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="1edb7-268">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="1edb7-268">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="1edb7-269">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-269">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="1edb7-270">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="1edb7-270">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="1edb7-271">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="1edb7-271">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="1edb7-272">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-272"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="1edb7-273">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-273">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="1edb7-274">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-274">Loggers are available from DI.</span></span> <span data-ttu-id="1edb7-275">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="1edb7-275">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="1edb7-276">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="1edb7-276">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="1edb7-277">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="1edb7-277">Logging added to filters:</span></span>

* <span data-ttu-id="1edb7-278">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="1edb7-278">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="1edb7-279">不应记录操作或其他框架事件  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-279">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="1edb7-280">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="1edb7-280">The built-in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="1edb7-281">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="1edb7-281">ServiceFilterAttribute</span></span>

<span data-ttu-id="1edb7-282">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-282">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="1edb7-283"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-283">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="1edb7-284">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-284">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="1edb7-285">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="1edb7-285">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Startup.cs?name=snippet&highlight=4)]

<span data-ttu-id="1edb7-286">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="1edb7-286">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="1edb7-287">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="1edb7-287">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="1edb7-288">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。 </span><span class="sxs-lookup"><span data-stu-id="1edb7-288">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="1edb7-289">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="1edb7-289">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="1edb7-290">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-290">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="1edb7-291">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-291">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="1edb7-292">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-292">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="1edb7-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-293"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="1edb7-294">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-294">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="1edb7-295">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-295">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="1edb7-296">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="1edb7-296">TypeFilterAttribute</span></span>

<span data-ttu-id="1edb7-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-297"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="1edb7-298">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="1edb7-298">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="1edb7-299">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="1edb7-299">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="1edb7-300">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="1edb7-300">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="1edb7-301">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-301">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="1edb7-302">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-302">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="1edb7-303">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="1edb7-303">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="1edb7-304">提供提示：筛选器实例可能在其创建的请求范围之外被重用。 </span><span class="sxs-lookup"><span data-stu-id="1edb7-304">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="1edb7-305">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-305">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="1edb7-306">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-306">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="1edb7-307">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="1edb7-307">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="1edb7-308">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-308">Authorization filters</span></span>

<span data-ttu-id="1edb7-309">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-309">Authorization filters:</span></span>

* <span data-ttu-id="1edb7-310">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-310">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="1edb7-311">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="1edb7-311">Control access to action methods.</span></span>
* <span data-ttu-id="1edb7-312">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-312">Have a before method, but no after method.</span></span>

<span data-ttu-id="1edb7-313">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="1edb7-313">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="1edb7-314">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-314">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="1edb7-315">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-315">The built-in authorization filter:</span></span>

* <span data-ttu-id="1edb7-316">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="1edb7-316">Calls the authorization system.</span></span>
* <span data-ttu-id="1edb7-317">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="1edb7-317">Does not authorize requests.</span></span>

<span data-ttu-id="1edb7-318">不会在授权筛选器中引发异常  ：</span><span class="sxs-lookup"><span data-stu-id="1edb7-318">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="1edb7-319">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-319">The exception will not be handled.</span></span>
* <span data-ttu-id="1edb7-320">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-320">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="1edb7-321">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="1edb7-321">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="1edb7-322">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-322">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="1edb7-323">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-323">Resource filters</span></span>

<span data-ttu-id="1edb7-324">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-324">Resource filters:</span></span>

* <span data-ttu-id="1edb7-325">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-325">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="1edb7-326">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="1edb7-326">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="1edb7-327">只有[授权筛选器](#authorization-filters)在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-327">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="1edb7-328">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-328">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="1edb7-329">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="1edb7-329">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="1edb7-330">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="1edb7-330">Resource filter examples:</span></span>

* <span data-ttu-id="1edb7-331">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-331">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="1edb7-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-332">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="1edb7-333">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="1edb7-333">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="1edb7-334">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="1edb7-334">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="1edb7-335">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-335">Action filters</span></span>

<span data-ttu-id="1edb7-336">操作筛选器不  应用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="1edb7-336">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="1edb7-337">Razor Pages 支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-337">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="1edb7-338">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-338">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="1edb7-339">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-339">Action filters:</span></span>

* <span data-ttu-id="1edb7-340">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-340">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="1edb7-341">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-341">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="1edb7-342">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-342">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="1edb7-343"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-343">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="1edb7-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="1edb7-344"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables reading the inputs to an action method.</span></span>
* <span data-ttu-id="1edb7-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-345"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="1edb7-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-346"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="1edb7-347">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="1edb7-347">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="1edb7-348">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-348">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="1edb7-349">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="1edb7-349">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="1edb7-350"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-350">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="1edb7-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="1edb7-351"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="1edb7-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-352"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="1edb7-353">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="1edb7-353">Setting this property to null:</span></span>

  * <span data-ttu-id="1edb7-354">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-354">Effectively handles the exception.</span></span>
  * <span data-ttu-id="1edb7-355">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="1edb7-355">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="1edb7-356">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="1edb7-356">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="1edb7-357">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-357">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="1edb7-358">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-358">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="1edb7-359">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-359">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="1edb7-360">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-360">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="1edb7-361">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="1edb7-361">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="1edb7-362">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="1edb7-362">Validate model state.</span></span>
* <span data-ttu-id="1edb7-363">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="1edb7-363">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-364">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="1edb7-364">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="1edb7-365">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="1edb7-365">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="1edb7-366">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="1edb7-366"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="1edb7-367">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-367"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="1edb7-368">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="1edb7-368">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="1edb7-369">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-369">Effectively handles an exception.</span></span>
  * <span data-ttu-id="1edb7-370">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="1edb7-370">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="1edb7-371">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-371">Exception filters</span></span>

<span data-ttu-id="1edb7-372">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-372">Exception filters:</span></span>

* <span data-ttu-id="1edb7-373">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-373">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span>
* <span data-ttu-id="1edb7-374">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="1edb7-374">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="1edb7-375">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="1edb7-375">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="1edb7-376">以下代码测试异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-376">The following code tests the exception filter:</span></span>

[!code-csharp[](filters/3.1sample/FiltersSample/Controllers/FailingController.cs?name=snippet)]

<span data-ttu-id="1edb7-377">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-377">Exception filters:</span></span>

* <span data-ttu-id="1edb7-378">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="1edb7-378">Don't have before and after events.</span></span>
* <span data-ttu-id="1edb7-379">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-379">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="1edb7-380">处理 Razor 页面或控制器创建、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-380">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="1edb7-381">请不要捕获资源筛选器、结果筛选器或 MVC 结果执行中发生的异常  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-381">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="1edb7-382">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="1edb7-382">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="1edb7-383">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-383">This stops propagation of the exception.</span></span> <span data-ttu-id="1edb7-384">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="1edb7-384">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="1edb7-385">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="1edb7-385">Only an action filter can do that.</span></span>

<span data-ttu-id="1edb7-386">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-386">Exception filters:</span></span>

* <span data-ttu-id="1edb7-387">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-387">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="1edb7-388">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="1edb7-388">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="1edb7-389">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-389">Prefer middleware for exception handling.</span></span> <span data-ttu-id="1edb7-390">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-390">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="1edb7-391">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-391">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="1edb7-392">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="1edb7-392">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="1edb7-393">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-393">Result filters</span></span>

<span data-ttu-id="1edb7-394">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-394">Result filters:</span></span>

* <span data-ttu-id="1edb7-395">实现接口：</span><span class="sxs-lookup"><span data-stu-id="1edb7-395">Implement an interface:</span></span>
  * <span data-ttu-id="1edb7-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="1edb7-396"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="1edb7-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="1edb7-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="1edb7-398">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-398">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="1edb7-399">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="1edb7-399">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="1edb7-400">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-400">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="1edb7-401">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="1edb7-401">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="1edb7-402">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="1edb7-402">An action returning a view includes all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="1edb7-403">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="1edb7-403">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="1edb7-404">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-404">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="1edb7-405">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-405">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="1edb7-406">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-406">Result filters are not executed when:</span></span>

* <span data-ttu-id="1edb7-407">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-407">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="1edb7-408">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-408">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="1edb7-409"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-409">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="1edb7-410">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="1edb7-410">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="1edb7-411">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="1edb7-411">Throwing an exception in `IResultFilter.OnResultExecuting`:</span></span>

* <span data-ttu-id="1edb7-412">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-412">Prevents execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="1edb7-413">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="1edb7-413">Is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="1edb7-414">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="1edb7-414">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has probably already been sent to the client.</span></span> <span data-ttu-id="1edb7-415">如果响应已发送到客户端，则无法更改。</span><span class="sxs-lookup"><span data-stu-id="1edb7-415">If the response has already been sent to the client, it cannot be changed.</span></span>

<span data-ttu-id="1edb7-416">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-416">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="1edb7-417">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-417">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="1edb7-418">将 `Exception` 设置为 NULL 可有效地处理异常，并防止在管道的后续阶段引发该异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-418">Setting `Exception` to null effectively handles an exception and prevents the exception from being thrown again later in the pipeline.</span></span> <span data-ttu-id="1edb7-419">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="1edb7-419">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="1edb7-420">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-420">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="1edb7-421">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="1edb7-421">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="1edb7-422">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-422">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="1edb7-423">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-423">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="1edb7-424">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-424">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="1edb7-425">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="1edb7-425">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="1edb7-426"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="1edb7-426">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="1edb7-427">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="1edb7-427">This includes action results produced by:</span></span>

* <span data-ttu-id="1edb7-428">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-428">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="1edb7-429">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-429">Exception filters.</span></span>

<span data-ttu-id="1edb7-430">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”  状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-430">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="1edb7-431">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="1edb7-431">IFilterFactory</span></span>

<span data-ttu-id="1edb7-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-432"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="1edb7-433">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-433">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="1edb7-434">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-434">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="1edb7-435">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-435">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="1edb7-436">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="1edb7-436">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="1edb7-437">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-437">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="1edb7-438">在以下代码中应用了筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-438">The filter is applied in the following code:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/SampleController.cs?name=snippet3&highlight=21)]

<span data-ttu-id="1edb7-439">通过运行[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="1edb7-439">Test the preceding code by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample):</span></span>

* <span data-ttu-id="1edb7-440">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="1edb7-440">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="1edb7-441">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-441">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="1edb7-442">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="1edb7-442">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="1edb7-443">**author:** `Rick Anderson`</span><span class="sxs-lookup"><span data-stu-id="1edb7-443">**author:** `Rick Anderson`</span></span>
* <span data-ttu-id="1edb7-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="1edb7-444">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="1edb7-445">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="1edb7-445">**internal:** `My header`</span></span>

<span data-ttu-id="1edb7-446">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="1edb7-446">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="1edb7-447">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="1edb7-447">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="1edb7-448">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-448">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="1edb7-449">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-449">Don't require passing parameters.</span></span>
* <span data-ttu-id="1edb7-450">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-450">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="1edb7-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-451"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="1edb7-452">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-452">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="1edb7-453">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-453">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="1edb7-454">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-454">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="1edb7-455">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-455">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="1edb7-456">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="1edb7-456">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="1edb7-457">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-457">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="1edb7-458">但筛选器又不同于中间件，它们是运行时的一部分，这意味着它们有权访问上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="1edb7-458">But filters differ from middleware in that they're part of the runtime, which means that they have access to context and constructs.</span></span>

<span data-ttu-id="1edb7-459">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="1edb7-459">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="1edb7-460">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-460">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="1edb7-461">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="1edb7-461">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/3.1sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="1edb7-462">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="1edb7-462">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="1edb7-463">后续操作</span><span class="sxs-lookup"><span data-stu-id="1edb7-463">Next actions</span></span>

* <span data-ttu-id="1edb7-464">请参阅 [Razor Pages 的筛选器方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-464">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="1edb7-465">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-465">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/3.1sample).</span></span>

::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1edb7-466">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="1edb7-466">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="1edb7-467">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。 </span><span class="sxs-lookup"><span data-stu-id="1edb7-467">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="1edb7-468">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="1edb7-468">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="1edb7-469">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="1edb7-469">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="1edb7-470">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="1edb7-470">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="1edb7-471">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="1edb7-471">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="1edb7-472">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="1edb7-472">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="1edb7-473">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-473">Filters avoid duplicating code.</span></span> <span data-ttu-id="1edb7-474">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="1edb7-474">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="1edb7-475">本文档适用于 Razor Pages、API 控制器和具有视图的控制器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-475">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="1edb7-476">[查看或下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="1edb7-476">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="1edb7-477">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="1edb7-477">How filters work</span></span>

<span data-ttu-id="1edb7-478">筛选器在 ASP.NET Core 操作调用管道  （有时称为筛选器管道  ）内运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-478">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="1edb7-479">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-479">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 ASP.NET Core 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="1edb7-482">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="1edb7-482">Filter types</span></span>

<span data-ttu-id="1edb7-483">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="1edb7-483">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="1edb7-484">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="1edb7-484">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="1edb7-485">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-485">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="1edb7-486">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-486">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="1edb7-487">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-487">Run after authorization.</span></span>  
  * <span data-ttu-id="1edb7-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 可以在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-488"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="1edb7-489">例如，`OnResourceExecuting` 可以在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-489">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="1edb7-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 可以在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-490"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="1edb7-491">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-491">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="1edb7-492">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="1edb7-492">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="1edb7-493">不可在 Razor Pages 中使用操作筛选器  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-493">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="1edb7-494">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="1edb7-494">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="1edb7-495">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-495">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="1edb7-496">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-496">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="1edb7-497">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-497">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="1edb7-498">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="1edb7-498">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="1edb7-501">实现</span><span class="sxs-lookup"><span data-stu-id="1edb7-501">Implementation</span></span>

<span data-ttu-id="1edb7-502">通过不同的接口定义，筛选器同时支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="1edb7-502">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="1edb7-503">同步筛选器可以在其管道阶段之前 (`On-Stage-Executing`) 和之后 (`On-Stage-Executed`) 运行代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-503">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="1edb7-504">例如，`OnActionExecuting` 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-504">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="1edb7-505">`OnActionExecuted` 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-505">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="1edb7-506">异步筛选器定义 `On-Stage-ExecutionAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-506">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="1edb7-507">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-507">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="1edb7-508">每个 `On-Stage-ExecutionAsync` 方法采用执行筛选器的管道阶段的 `FilterType-ExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-508">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="1edb7-509">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="1edb7-509">Multiple filter stages</span></span>

<span data-ttu-id="1edb7-510">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-510">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="1edb7-511">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类实现 `IActionFilter`、`IResultFilter` 及其异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-511">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="1edb7-512">筛选器接口的同步和异步版本任意实现一个，而不是同时实现   。</span><span class="sxs-lookup"><span data-stu-id="1edb7-512">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="1edb7-513">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-513">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="1edb7-514">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-514">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="1edb7-515">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-515">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="1edb7-516">使用抽象类时（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>），将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-516">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="1edb7-517">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="1edb7-517">Built-in filter attributes</span></span>

<span data-ttu-id="1edb7-518">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-518">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="1edb7-519">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="1edb7-519">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-520">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="1edb7-520">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="1edb7-521">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="1edb7-521">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="1edb7-522">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="1edb7-522">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="1edb7-523">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-523">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="1edb7-524">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="1edb7-524">Filter scopes and order of execution</span></span>

<span data-ttu-id="1edb7-525">可以将筛选器添加到管道中的三个作用域  之一：</span><span class="sxs-lookup"><span data-stu-id="1edb7-525">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="1edb7-526">在操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-526">Using an attribute on an action.</span></span>
* <span data-ttu-id="1edb7-527">在控制器上使用属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-527">Using an attribute on a controller.</span></span>
* <span data-ttu-id="1edb7-528">所有控制器和操作的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="1edb7-528">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="1edb7-529">前面的代码使用 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 集合全局添加三个筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-529">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="1edb7-530">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="1edb7-530">Default order of execution</span></span>

<span data-ttu-id="1edb7-531">当有同一类型的多个筛选器时，作用域可确定筛选器执行的默认顺序  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-531">When there are multiple filters *of the same type*, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="1edb7-532">全局筛选器涵盖类筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-532">Global filters surround class filters.</span></span> <span data-ttu-id="1edb7-533">类筛选器涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-533">Class filters surround method filters.</span></span>

<span data-ttu-id="1edb7-534">在筛选器嵌套模式下，筛选器的 after  代码会按照与 before  代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-534">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="1edb7-535">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="1edb7-535">The filter sequence:</span></span>

* <span data-ttu-id="1edb7-536">全局筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-536">The *before* code of global filters.</span></span>
  * <span data-ttu-id="1edb7-537">控制器筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-537">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="1edb7-538">操作方法筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-538">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="1edb7-539">操作方法筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-539">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="1edb7-540">控制器筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-540">The *after* code of controller filters.</span></span>
* <span data-ttu-id="1edb7-541">全局筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-541">The *after* code of global filters.</span></span>
  
<span data-ttu-id="1edb7-542">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="1edb7-542">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="1edb7-543">序列</span><span class="sxs-lookup"><span data-stu-id="1edb7-543">Sequence</span></span> | <span data-ttu-id="1edb7-544">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="1edb7-544">Filter scope</span></span> | <span data-ttu-id="1edb7-545">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-545">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="1edb7-546">1</span><span class="sxs-lookup"><span data-stu-id="1edb7-546">1</span></span> | <span data-ttu-id="1edb7-547">Global</span><span class="sxs-lookup"><span data-stu-id="1edb7-547">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="1edb7-548">2</span><span class="sxs-lookup"><span data-stu-id="1edb7-548">2</span></span> | <span data-ttu-id="1edb7-549">控制器</span><span class="sxs-lookup"><span data-stu-id="1edb7-549">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="1edb7-550">3</span><span class="sxs-lookup"><span data-stu-id="1edb7-550">3</span></span> | <span data-ttu-id="1edb7-551">方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-551">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="1edb7-552">4</span><span class="sxs-lookup"><span data-stu-id="1edb7-552">4</span></span> | <span data-ttu-id="1edb7-553">方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-553">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="1edb7-554">5</span><span class="sxs-lookup"><span data-stu-id="1edb7-554">5</span></span> | <span data-ttu-id="1edb7-555">控制器</span><span class="sxs-lookup"><span data-stu-id="1edb7-555">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="1edb7-556">6</span><span class="sxs-lookup"><span data-stu-id="1edb7-556">6</span></span> | <span data-ttu-id="1edb7-557">Global</span><span class="sxs-lookup"><span data-stu-id="1edb7-557">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="1edb7-558">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="1edb7-558">This sequence shows:</span></span>

* <span data-ttu-id="1edb7-559">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="1edb7-559">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="1edb7-560">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="1edb7-560">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="1edb7-561">控制器和 Razor 页面级筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-561">Controller and Razor Page level filters</span></span>

<span data-ttu-id="1edb7-562">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-562">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="1edb7-563">这些方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-563">These methods:</span></span>

* <span data-ttu-id="1edb7-564">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-564">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="1edb7-565">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-565">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="1edb7-566">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-566">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="1edb7-567">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-567">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="1edb7-568">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-568">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="1edb7-569">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-569">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="1edb7-570">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-570">The `TestController`:</span></span>

* <span data-ttu-id="1edb7-571">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作。</span><span class="sxs-lookup"><span data-stu-id="1edb7-571">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action.</span></span>
* <span data-ttu-id="1edb7-572">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-572">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="1edb7-573">导航到 `https://localhost:5001/Test/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="1edb7-573">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="1edb7-574">对于 Razor Pages，请参阅[通过重写筛选器方法实现 Razor 页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-574">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="1edb7-575">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="1edb7-575">Overriding the default order</span></span>

<span data-ttu-id="1edb7-576">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="1edb7-576">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="1edb7-577">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="1edb7-577">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="1edb7-578">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-578">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="1edb7-579">在具有较高的 `Order` 值的筛选器之前运行 before  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-579">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="1edb7-580">在具有较高的 `Order` 值的筛选器之后运行 after  代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-580">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="1edb7-581">可以使用构造函数参数设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-581">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="1edb7-582">请考虑前面示例中所示的 3 个相同操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-582">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="1edb7-583">如果控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="1edb7-583">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="1edb7-584">序列</span><span class="sxs-lookup"><span data-stu-id="1edb7-584">Sequence</span></span> | <span data-ttu-id="1edb7-585">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="1edb7-585">Filter scope</span></span> | <span data-ttu-id="1edb7-586">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="1edb7-586">`Order` property</span></span> | <span data-ttu-id="1edb7-587">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-587">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="1edb7-588">1</span><span class="sxs-lookup"><span data-stu-id="1edb7-588">1</span></span> | <span data-ttu-id="1edb7-589">方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-589">Method</span></span> | <span data-ttu-id="1edb7-590">0</span><span class="sxs-lookup"><span data-stu-id="1edb7-590">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="1edb7-591">2</span><span class="sxs-lookup"><span data-stu-id="1edb7-591">2</span></span> | <span data-ttu-id="1edb7-592">控制器</span><span class="sxs-lookup"><span data-stu-id="1edb7-592">Controller</span></span> | <span data-ttu-id="1edb7-593">1</span><span class="sxs-lookup"><span data-stu-id="1edb7-593">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="1edb7-594">3</span><span class="sxs-lookup"><span data-stu-id="1edb7-594">3</span></span> | <span data-ttu-id="1edb7-595">Global</span><span class="sxs-lookup"><span data-stu-id="1edb7-595">Global</span></span> | <span data-ttu-id="1edb7-596">2</span><span class="sxs-lookup"><span data-stu-id="1edb7-596">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="1edb7-597">4</span><span class="sxs-lookup"><span data-stu-id="1edb7-597">4</span></span> | <span data-ttu-id="1edb7-598">Global</span><span class="sxs-lookup"><span data-stu-id="1edb7-598">Global</span></span> | <span data-ttu-id="1edb7-599">2</span><span class="sxs-lookup"><span data-stu-id="1edb7-599">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="1edb7-600">5</span><span class="sxs-lookup"><span data-stu-id="1edb7-600">5</span></span> | <span data-ttu-id="1edb7-601">控制器</span><span class="sxs-lookup"><span data-stu-id="1edb7-601">Controller</span></span> | <span data-ttu-id="1edb7-602">1</span><span class="sxs-lookup"><span data-stu-id="1edb7-602">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="1edb7-603">6</span><span class="sxs-lookup"><span data-stu-id="1edb7-603">6</span></span> | <span data-ttu-id="1edb7-604">方法</span><span class="sxs-lookup"><span data-stu-id="1edb7-604">Method</span></span> | <span data-ttu-id="1edb7-605">0</span><span class="sxs-lookup"><span data-stu-id="1edb7-605">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="1edb7-606">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="1edb7-606">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="1edb7-607">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="1edb7-607">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="1edb7-608">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="1edb7-608">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="1edb7-609">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-609">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="1edb7-610">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="1edb7-610">Cancellation and short-circuiting</span></span>

<span data-ttu-id="1edb7-611">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-611">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="1edb7-612">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="1edb7-612">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-613">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="1edb7-613">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="1edb7-614">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-614">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="1edb7-615">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-615">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="1edb7-616">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="1edb7-616">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="1edb7-617">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-617">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="1edb7-618">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="1edb7-618">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="1edb7-619">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-619">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="1edb7-620">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="1edb7-620">Dependency injection</span></span>

<span data-ttu-id="1edb7-621">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-621">Filters can be added by type or by instance.</span></span> <span data-ttu-id="1edb7-622">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="1edb7-622">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="1edb7-623">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-623">If a type is added, it's type-activated.</span></span> <span data-ttu-id="1edb7-624">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="1edb7-624">A type-activated filter means:</span></span>

* <span data-ttu-id="1edb7-625">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-625">An instance is created for each request.</span></span>
* <span data-ttu-id="1edb7-626">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-626">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="1edb7-627">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-627">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="1edb7-628">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="1edb7-628">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="1edb7-629">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-629">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="1edb7-630">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="1edb7-630">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="1edb7-631">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="1edb7-631">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="1edb7-632">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-632"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="1edb7-633">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-633">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="1edb7-634">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-634">Loggers are available from DI.</span></span> <span data-ttu-id="1edb7-635">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="1edb7-635">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="1edb7-636">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="1edb7-636">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="1edb7-637">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="1edb7-637">Logging added to filters:</span></span>

* <span data-ttu-id="1edb7-638">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="1edb7-638">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="1edb7-639">不应记录操作或其他框架事件  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-639">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="1edb7-640">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="1edb7-640">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="1edb7-641">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="1edb7-641">ServiceFilterAttribute</span></span>

<span data-ttu-id="1edb7-642">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-642">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="1edb7-643"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-643">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="1edb7-644">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-644">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="1edb7-645">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="1edb7-645">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="1edb7-646">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="1edb7-646">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="1edb7-647">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="1edb7-647">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="1edb7-648">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。 </span><span class="sxs-lookup"><span data-stu-id="1edb7-648">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="1edb7-649">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="1edb7-649">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="1edb7-650">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-650">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="1edb7-651">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-651">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="1edb7-652">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-652">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="1edb7-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-653"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="1edb7-654">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-654">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="1edb7-655">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-655">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="1edb7-656">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="1edb7-656">TypeFilterAttribute</span></span>

<span data-ttu-id="1edb7-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-657"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="1edb7-658">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="1edb7-658">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="1edb7-659">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="1edb7-659">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="1edb7-660">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="1edb7-660">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="1edb7-661">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-661">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="1edb7-662">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-662">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="1edb7-663">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="1edb7-663">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="1edb7-664">提供提示：筛选器实例可能在其创建的请求范围之外被重用。 </span><span class="sxs-lookup"><span data-stu-id="1edb7-664">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="1edb7-665">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-665">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="1edb7-666">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-666">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="1edb7-667">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="1edb7-667">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="1edb7-668">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-668">Authorization filters</span></span>

<span data-ttu-id="1edb7-669">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-669">Authorization filters:</span></span>

* <span data-ttu-id="1edb7-670">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-670">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="1edb7-671">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="1edb7-671">Control access to action methods.</span></span>
* <span data-ttu-id="1edb7-672">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-672">Have a before method, but no after method.</span></span>

<span data-ttu-id="1edb7-673">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="1edb7-673">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="1edb7-674">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-674">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="1edb7-675">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-675">The built-in authorization filter:</span></span>

* <span data-ttu-id="1edb7-676">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="1edb7-676">Calls the authorization system.</span></span>
* <span data-ttu-id="1edb7-677">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="1edb7-677">Does not authorize requests.</span></span>

<span data-ttu-id="1edb7-678">不会在授权筛选器中引发异常  ：</span><span class="sxs-lookup"><span data-stu-id="1edb7-678">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="1edb7-679">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-679">The exception will not be handled.</span></span>
* <span data-ttu-id="1edb7-680">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-680">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="1edb7-681">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="1edb7-681">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="1edb7-682">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-682">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="1edb7-683">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-683">Resource filters</span></span>

<span data-ttu-id="1edb7-684">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-684">Resource filters:</span></span>

* <span data-ttu-id="1edb7-685">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-685">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="1edb7-686">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="1edb7-686">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="1edb7-687">只有[授权筛选器](#authorization-filters)在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-687">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="1edb7-688">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="1edb7-688">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="1edb7-689">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="1edb7-689">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="1edb7-690">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="1edb7-690">Resource filter examples:</span></span>

* <span data-ttu-id="1edb7-691">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-691">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="1edb7-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-692">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="1edb7-693">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="1edb7-693">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="1edb7-694">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="1edb7-694">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="1edb7-695">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-695">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="1edb7-696">操作筛选器不  应用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="1edb7-696">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="1edb7-697">Razor Pages 支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-697">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="1edb7-698">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-698">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="1edb7-699">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-699">Action filters:</span></span>

* <span data-ttu-id="1edb7-700">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="1edb7-700">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="1edb7-701">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-701">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="1edb7-702">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-702">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="1edb7-703"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-703">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="1edb7-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="1edb7-704"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="1edb7-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-705"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="1edb7-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-706"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="1edb7-707">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="1edb7-707">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="1edb7-708">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-708">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="1edb7-709">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="1edb7-709">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="1edb7-710"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-710">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="1edb7-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="1edb7-711"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="1edb7-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-712"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="1edb7-713">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="1edb7-713">Setting this property to null:</span></span>

  * <span data-ttu-id="1edb7-714">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-714">Effectively handles the exception.</span></span>
  * <span data-ttu-id="1edb7-715">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="1edb7-715">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="1edb7-716">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="1edb7-716">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="1edb7-717">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-717">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="1edb7-718">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-718">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="1edb7-719">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-719">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="1edb7-720">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-720">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="1edb7-721">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="1edb7-721">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="1edb7-722">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="1edb7-722">Validate model state.</span></span>
* <span data-ttu-id="1edb7-723">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="1edb7-723">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="1edb7-724">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="1edb7-724">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="1edb7-725">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="1edb7-725">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="1edb7-726">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="1edb7-726"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="1edb7-727">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-727"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="1edb7-728">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="1edb7-728">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="1edb7-729">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-729">Effectively handles an exception.</span></span>
  * <span data-ttu-id="1edb7-730">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="1edb7-730">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="1edb7-731">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-731">Exception filters</span></span>

<span data-ttu-id="1edb7-732">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-732">Exception filters:</span></span>

* <span data-ttu-id="1edb7-733">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-733">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="1edb7-734">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="1edb7-734">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="1edb7-735">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="1edb7-735">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilter.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="1edb7-736">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-736">Exception filters:</span></span>

* <span data-ttu-id="1edb7-737">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="1edb7-737">Don't have before and after events.</span></span>
* <span data-ttu-id="1edb7-738">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-738">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="1edb7-739">处理 Razor 页面或控制器创建、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-739">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="1edb7-740">请不要捕获资源筛选器、结果筛选器或 MVC 结果执行中发生的异常  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-740">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="1edb7-741">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="1edb7-741">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="1edb7-742">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-742">This stops propagation of the exception.</span></span> <span data-ttu-id="1edb7-743">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="1edb7-743">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="1edb7-744">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="1edb7-744">Only an action filter can do that.</span></span>

<span data-ttu-id="1edb7-745">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-745">Exception filters:</span></span>

* <span data-ttu-id="1edb7-746">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-746">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="1edb7-747">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="1edb7-747">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="1edb7-748">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-748">Prefer middleware for exception handling.</span></span> <span data-ttu-id="1edb7-749">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器  。</span><span class="sxs-lookup"><span data-stu-id="1edb7-749">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="1edb7-750">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-750">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="1edb7-751">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="1edb7-751">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="1edb7-752">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="1edb7-752">Result filters</span></span>

<span data-ttu-id="1edb7-753">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-753">Result filters:</span></span>

* <span data-ttu-id="1edb7-754">实现接口：</span><span class="sxs-lookup"><span data-stu-id="1edb7-754">Implement an interface:</span></span>
  * <span data-ttu-id="1edb7-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="1edb7-755"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="1edb7-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="1edb7-756"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="1edb7-757">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-757">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="1edb7-758">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="1edb7-758">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="1edb7-759">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-759">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="1edb7-760">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="1edb7-760">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="1edb7-761">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="1edb7-761">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="1edb7-762">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="1edb7-762">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="1edb7-763">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-763">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="1edb7-764">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-764">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="1edb7-765">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-765">Result filters are not executed when:</span></span>

* <span data-ttu-id="1edb7-766">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-766">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="1edb7-767">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-767">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="1edb7-768"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="1edb7-768">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="1edb7-769">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="1edb7-769">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="1edb7-770">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="1edb7-770">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="1edb7-771">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-771">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="1edb7-772">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="1edb7-772">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="1edb7-773">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时，响应可能已发送到客户端。</span><span class="sxs-lookup"><span data-stu-id="1edb7-773">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs, the response has likely already been sent to the client.</span></span> <span data-ttu-id="1edb7-774">如果响应已发送到客户端，则无法再更改。</span><span class="sxs-lookup"><span data-stu-id="1edb7-774">If the response has already been sent to the client, it cannot be changed further.</span></span>

<span data-ttu-id="1edb7-775">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-775">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="1edb7-776">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="1edb7-776">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="1edb7-777">将 `Exception` 设置为 NULL 可有效地处理异常，并防止 ASP.NET Core 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="1edb7-777">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="1edb7-778">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="1edb7-778">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="1edb7-779">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="1edb7-779">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="1edb7-780">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="1edb7-780">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="1edb7-781">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="1edb7-781">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="1edb7-782">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-782">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="1edb7-783">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="1edb7-783">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="1edb7-784">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="1edb7-784">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="1edb7-785"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="1edb7-785">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="1edb7-786">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="1edb7-786">This includes action results produced by:</span></span>

* <span data-ttu-id="1edb7-787">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-787">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="1edb7-788">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="1edb7-788">Exception filters.</span></span>

<span data-ttu-id="1edb7-789">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”  状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="1edb7-789">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="1edb7-790">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="1edb7-790">IFilterFactory</span></span>

<span data-ttu-id="1edb7-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-791"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="1edb7-792">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-792">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="1edb7-793">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-793">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="1edb7-794">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="1edb7-794">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="1edb7-795">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="1edb7-795">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="1edb7-796">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-796">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="1edb7-797">可以通过运行[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="1edb7-797">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="1edb7-798">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="1edb7-798">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="1edb7-799">导航到 `https://localhost:5001/Sample/HeaderWithFactory`。</span><span class="sxs-lookup"><span data-stu-id="1edb7-799">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`.</span></span>

<span data-ttu-id="1edb7-800">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="1edb7-800">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="1edb7-801">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="1edb7-801">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="1edb7-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="1edb7-802">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="1edb7-803">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="1edb7-803">**internal:** `My header`</span></span>

<span data-ttu-id="1edb7-804">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="1edb7-804">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="1edb7-805">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="1edb7-805">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="1edb7-806">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="1edb7-806">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="1edb7-807">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="1edb7-807">Don't require passing parameters.</span></span>
* <span data-ttu-id="1edb7-808">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="1edb7-808">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="1edb7-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="1edb7-809"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="1edb7-810">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-810">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="1edb7-811">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="1edb7-811">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="1edb7-812">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="1edb7-812">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="1edb7-813">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="1edb7-813">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="1edb7-814">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="1edb7-814">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="1edb7-815">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="1edb7-815">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="1edb7-816">但筛选器又不同于中间件，它们是 ASP.NET Core 运行时的一部分，这意味着它们有权访问 ASP.NET Core 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="1edb7-816">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="1edb7-817">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="1edb7-817">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="1edb7-818">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="1edb7-818">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,22)]

<span data-ttu-id="1edb7-819">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="1edb7-819">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="1edb7-820">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="1edb7-820">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="1edb7-821">后续操作</span><span class="sxs-lookup"><span data-stu-id="1edb7-821">Next actions</span></span>

* <span data-ttu-id="1edb7-822">请参阅 [Razor Pages 的筛选器方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-822">See [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>
* <span data-ttu-id="1edb7-823">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="1edb7-823">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

::: moniker-end
