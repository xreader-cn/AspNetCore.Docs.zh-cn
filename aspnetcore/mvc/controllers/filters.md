---
title: ASP.NET Core 中的筛选器
author: ardalis
description: 了解筛选器的工作原理以及如何在 ASP.NET Core 中使用它们。
ms.author: riande
ms.custom: mvc
ms.date: 09/28/2019
uid: mvc/controllers/filters
ms.openlocfilehash: ed48c2074360768b8d8c5af7057b353b00592394
ms.sourcegitcommit: 73a451e9a58ac7102f90b608d661d8c23dd9bbaf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037693"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="6b59b-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="6b59b-104">作者：[Kirk Larkin](https://github.com/serpent5)、[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="6b59b-104">By [Kirk Larkin](https://github.com/serpent5), [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="6b59b-105">通过使用 ASP.NET Core 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。 </span><span class="sxs-lookup"><span data-stu-id="6b59b-105">*Filters* in ASP.NET Core allow code to be run before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="6b59b-106">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="6b59b-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="6b59b-107">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="6b59b-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="6b59b-108">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="6b59b-108">Response caching (short-circuiting the request pipeline to return a cached response).</span></span>

<span data-ttu-id="6b59b-109">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="6b59b-109">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="6b59b-110">横切关注点的示例包括错误处理、缓存、配置、授权和日志记录。</span><span class="sxs-lookup"><span data-stu-id="6b59b-110">Examples of cross-cutting concerns include error handling, caching, configuration, authorization, and logging.</span></span>  <span data-ttu-id="6b59b-111">筛选器可以避免复制代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-111">Filters avoid duplicating code.</span></span> <span data-ttu-id="6b59b-112">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="6b59b-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="6b59b-113">本文档适用于 Razor Pages、API 控制器和具有视图的控制器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-113">This document applies to Razor Pages, API controllers, and controllers with views.</span></span>

<span data-ttu-id="6b59b-114">[查看或下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="6b59b-114">[View or download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample) ([how to download](xref:index#how-to-download-a-sample)).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="6b59b-115">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="6b59b-115">How filters work</span></span>

<span data-ttu-id="6b59b-116">筛选器在 ASP.NET Core 操作调用管道  （有时称为筛选器管道  ）内运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-116">Filters run within the *ASP.NET Core action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="6b59b-117">筛选器管道在 ASP.NET Core 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-117">The filter pipeline runs after ASP.NET Core selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 ASP.NET Core 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="6b59b-120">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="6b59b-120">Filter types</span></span>

<span data-ttu-id="6b59b-121">每种筛选器类型都在筛选器管道中的不同阶段执行：</span><span class="sxs-lookup"><span data-stu-id="6b59b-121">Each filter type is executed at a different stage in the filter pipeline:</span></span>

* <span data-ttu-id="6b59b-122">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对请求为用户授权。</span><span class="sxs-lookup"><span data-stu-id="6b59b-122">[Authorization filters](#authorization-filters) run first and are used to determine whether the user is authorized for the request.</span></span> <span data-ttu-id="6b59b-123">如果请求未获授权，授权筛选器可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="6b59b-123">Authorization filters short-circuit the pipeline if the request is unauthorized.</span></span>

* <span data-ttu-id="6b59b-124">[资源筛选器](#resource-filters)：</span><span class="sxs-lookup"><span data-stu-id="6b59b-124">[Resource filters](#resource-filters):</span></span>

  * <span data-ttu-id="6b59b-125">授权后运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-125">Run after authorization.</span></span>  
  * <span data-ttu-id="6b59b-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> 可以在筛选器管道的其余阶段之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-126"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuting*> can run code before the rest of the filter pipeline.</span></span> <span data-ttu-id="6b59b-127">例如，`OnResourceExecuting` 可以在模型绑定之前运行代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-127">For example, `OnResourceExecuting` can run code before model binding.</span></span>
  * <span data-ttu-id="6b59b-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> 可以在管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-128"><xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter.OnResourceExecuted*> can run code after the rest of the pipeline has completed.</span></span>

* <span data-ttu-id="6b59b-129">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-129">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="6b59b-130">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="6b59b-130">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="6b59b-131">不可在 Razor Pages 中使用操作筛选器  。</span><span class="sxs-lookup"><span data-stu-id="6b59b-131">Action filters are **not** supported in Razor Pages.</span></span>

* <span data-ttu-id="6b59b-132">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="6b59b-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="6b59b-133">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="6b59b-134">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="6b59b-135">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="6b59b-136">下图展示了筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="6b59b-136">The following diagram shows how filter types interact in the filter pipeline.</span></span>

![请求通过授权过滤器、资源过滤器、模型绑定、操作过滤器、操作执行和操作结果转换、异常过滤器、结果过滤器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="6b59b-139">实现</span><span class="sxs-lookup"><span data-stu-id="6b59b-139">Implementation</span></span>

<span data-ttu-id="6b59b-140">通过不同的接口定义，筛选器同时支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="6b59b-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span>

<span data-ttu-id="6b59b-141">同步筛选器可以在其管道阶段之前 (`On-Stage-Executing`) 和之后 (`On-Stage-Executed`) 运行代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-141">Synchronous filters can run code before (`On-Stage-Executing`) and after (`On-Stage-Executed`) their pipeline stage.</span></span> <span data-ttu-id="6b59b-142">例如，`OnActionExecuting` 在调用操作方法之前调用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-142">For example, `OnActionExecuting` is called before the action method is called.</span></span> <span data-ttu-id="6b59b-143">`OnActionExecuted` 在操作方法返回之后调用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-143">`OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6b59b-144">异步筛选器定义 `On-Stage-ExecutionAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="6b59b-144">Asynchronous filters define an `On-Stage-ExecutionAsync` method:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleAsyncActionFilter.cs?name=snippet)]

<span data-ttu-id="6b59b-145">在前面的代码中，`SampleAsyncActionFilter` 具有执行操作方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-145">In the preceding code, the `SampleAsyncActionFilter` has an <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> (`next`)  that executes the action method.</span></span>  <span data-ttu-id="6b59b-146">每个 `On-Stage-ExecutionAsync` 方法采用执行筛选器的管道阶段的 `FilterType-ExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-146">Each of the `On-Stage-ExecutionAsync` methods take a `FilterType-ExecutionDelegate` that executes the filter's pipeline stage.</span></span>

### <a name="multiple-filter-stages"></a><span data-ttu-id="6b59b-147">多个筛选器阶段</span><span class="sxs-lookup"><span data-stu-id="6b59b-147">Multiple filter stages</span></span>

<span data-ttu-id="6b59b-148">可以在单个类中实现多个筛选器阶段的接口。</span><span class="sxs-lookup"><span data-stu-id="6b59b-148">Interfaces for multiple filter stages can be implemented in a single class.</span></span> <span data-ttu-id="6b59b-149">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类实现 `IActionFilter`、`IResultFilter` 及其异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="6b59b-149">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

<span data-ttu-id="6b59b-150">筛选器接口的同步和异步版本任意实现一个，而不是同时实现   。</span><span class="sxs-lookup"><span data-stu-id="6b59b-150">Implement **either** the synchronous or the async version of a filter interface, **not** both.</span></span> <span data-ttu-id="6b59b-151">运行时会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="6b59b-151">The runtime checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="6b59b-152">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-152">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="6b59b-153">如果在一个类中同时实现异步和同步接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-153">If both asynchronous and synchronous interfaces are implemented in one class, only the async method is called.</span></span> <span data-ttu-id="6b59b-154">使用抽象类时（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>），将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-154">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="built-in-filter-attributes"></a><span data-ttu-id="6b59b-155">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="6b59b-155">Built-in filter attributes</span></span>

<span data-ttu-id="6b59b-156">ASP.NET Core 包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-156">ASP.NET Core includes built-in attribute-based filters that can be subclassed and customized.</span></span> <span data-ttu-id="6b59b-157">例如，以下结果筛选器会向响应添加标头：</span><span class="sxs-lookup"><span data-stu-id="6b59b-157">For example, the following result filter adds a header to the response:</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderAttribute.cs?name=snippet)]

<span data-ttu-id="6b59b-158">通过使用属性，筛选器可接收参数，如前面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="6b59b-158">Attributes allow filters to accept arguments, as shown in the preceding example.</span></span> <span data-ttu-id="6b59b-159">将 `AddHeaderAttribute` 添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="6b59b-159">Apply the `AddHeaderAttribute` to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<!-- `https://localhost:5001/Sample` -->

<span data-ttu-id="6b59b-160">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="6b59b-160">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="6b59b-161">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="6b59b-161">Filter attributes:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ExceptionFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.Filters.ResultFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.FormatFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="6b59b-162">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="6b59b-162">Filter scopes and order of execution</span></span>

<span data-ttu-id="6b59b-163">可以将筛选器添加到管道中的三个作用域  之一：</span><span class="sxs-lookup"><span data-stu-id="6b59b-163">A filter can be added to the pipeline at one of three *scopes*:</span></span>

* <span data-ttu-id="6b59b-164">在操作上使用属性。</span><span class="sxs-lookup"><span data-stu-id="6b59b-164">Using an attribute on an action.</span></span>
* <span data-ttu-id="6b59b-165">在控制器上使用属性。</span><span class="sxs-lookup"><span data-stu-id="6b59b-165">Using an attribute on a controller.</span></span>
* <span data-ttu-id="6b59b-166">所有控制器和操作的全局筛选器，如下面的代码所示：</span><span class="sxs-lookup"><span data-stu-id="6b59b-166">Globally for all controllers and actions as shown in the following code:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/StartupGF.cs?name=snippet_ConfigureServices)]

<span data-ttu-id="6b59b-167">前面的代码使用 [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) 集合全局添加三个筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-167">The preceding code adds three filters globally using the [MvcOptions.Filters](xref:Microsoft.AspNetCore.Mvc.MvcOptions.Filters) collection.</span></span>

### <a name="default-order-of-execution"></a><span data-ttu-id="6b59b-168">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="6b59b-168">Default order of execution</span></span>

<span data-ttu-id="6b59b-169">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="6b59b-169">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="6b59b-170">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-170">Global filters surround class filters, which in turn surround method filters.</span></span>

<span data-ttu-id="6b59b-171">在筛选器嵌套模式下，筛选器的 after  代码会按照与 before  代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-171">As a result of filter nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="6b59b-172">筛选器序列：</span><span class="sxs-lookup"><span data-stu-id="6b59b-172">The filter sequence:</span></span>

* <span data-ttu-id="6b59b-173">全局筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-173">The *before* code of global filters.</span></span>
  * <span data-ttu-id="6b59b-174">控制器筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-174">The *before* code of controller filters.</span></span>
    * <span data-ttu-id="6b59b-175">操作方法筛选器的 before  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-175">The *before* code of action method filters.</span></span>
    * <span data-ttu-id="6b59b-176">操作方法筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-176">The *after* code of action method filters.</span></span>
  * <span data-ttu-id="6b59b-177">控制器筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-177">The *after* code of controller filters.</span></span>
* <span data-ttu-id="6b59b-178">全局筛选器的 after  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-178">The *after* code of global filters.</span></span>
  
<span data-ttu-id="6b59b-179">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="6b59b-179">The following example that illustrates the order in which filter methods are called for synchronous action filters.</span></span>

| <span data-ttu-id="6b59b-180">序列</span><span class="sxs-lookup"><span data-stu-id="6b59b-180">Sequence</span></span> | <span data-ttu-id="6b59b-181">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="6b59b-181">Filter scope</span></span> | <span data-ttu-id="6b59b-182">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="6b59b-182">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="6b59b-183">1</span><span class="sxs-lookup"><span data-stu-id="6b59b-183">1</span></span> | <span data-ttu-id="6b59b-184">Global</span><span class="sxs-lookup"><span data-stu-id="6b59b-184">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6b59b-185">2</span><span class="sxs-lookup"><span data-stu-id="6b59b-185">2</span></span> | <span data-ttu-id="6b59b-186">控制器</span><span class="sxs-lookup"><span data-stu-id="6b59b-186">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6b59b-187">3</span><span class="sxs-lookup"><span data-stu-id="6b59b-187">3</span></span> | <span data-ttu-id="6b59b-188">方法</span><span class="sxs-lookup"><span data-stu-id="6b59b-188">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6b59b-189">4</span><span class="sxs-lookup"><span data-stu-id="6b59b-189">4</span></span> | <span data-ttu-id="6b59b-190">方法</span><span class="sxs-lookup"><span data-stu-id="6b59b-190">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6b59b-191">5</span><span class="sxs-lookup"><span data-stu-id="6b59b-191">5</span></span> | <span data-ttu-id="6b59b-192">控制器</span><span class="sxs-lookup"><span data-stu-id="6b59b-192">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="6b59b-193">6</span><span class="sxs-lookup"><span data-stu-id="6b59b-193">6</span></span> | <span data-ttu-id="6b59b-194">Global</span><span class="sxs-lookup"><span data-stu-id="6b59b-194">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="6b59b-195">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="6b59b-195">This sequence shows:</span></span>

* <span data-ttu-id="6b59b-196">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="6b59b-196">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="6b59b-197">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="6b59b-197">The controller filter is nested within the global filter.</span></span>

### <a name="controller-and-razor-page-level-filters"></a><span data-ttu-id="6b59b-198">控制器和 Razor 页面级筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-198">Controller and Razor Page level filters</span></span>

<span data-ttu-id="6b59b-199">继承自 <xref:Microsoft.AspNetCore.Mvc.Controller> 基类的每个控制器包括 [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*)、[Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*) 和 [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-199">Every controller that inherits from the <xref:Microsoft.AspNetCore.Mvc.Controller> base class includes [Controller.OnActionExecuting](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuting*),  [Controller.OnActionExecutionAsync](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecutionAsync*), and [Controller.OnActionExecuted](xref:Microsoft.AspNetCore.Mvc.Controller.OnActionExecuted*)
`OnActionExecuted` methods.</span></span> <span data-ttu-id="6b59b-200">这些方法：</span><span class="sxs-lookup"><span data-stu-id="6b59b-200">These methods:</span></span>

* <span data-ttu-id="6b59b-201">覆盖为给定操作运行的筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-201">Wrap the filters that run for a given action.</span></span>
* <span data-ttu-id="6b59b-202">`OnActionExecuting` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-202">`OnActionExecuting` is called before any of the action's filters.</span></span>
* <span data-ttu-id="6b59b-203">`OnActionExecuted` 在所有操作筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-203">`OnActionExecuted` is called after all of the action filters.</span></span>
* <span data-ttu-id="6b59b-204">`OnActionExecutionAsync` 在所有操作筛选器之前调用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-204">`OnActionExecutionAsync` is called before any of the action's filters.</span></span> <span data-ttu-id="6b59b-205">`next` 之后的筛选器中的代码在操作方法之后运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-205">Code in the filter after `next` runs after the action method.</span></span>

<span data-ttu-id="6b59b-206">例如，在下载示例中，启动时全局应用 `MySampleActionFilter`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-206">For example, in the download sample, `MySampleActionFilter` is applied globally in startup.</span></span>

<span data-ttu-id="6b59b-207">`TestController`：</span><span class="sxs-lookup"><span data-stu-id="6b59b-207">The `TestController`:</span></span>

* <span data-ttu-id="6b59b-208">将 `SampleActionFilterAttribute` (`[SampleActionFilter]`) 应用于 `FilterTest2` 操作：</span><span class="sxs-lookup"><span data-stu-id="6b59b-208">Applies the `SampleActionFilterAttribute` (`[SampleActionFilter]`) to the `FilterTest2` action:</span></span>
* <span data-ttu-id="6b59b-209">重写 `OnActionExecuting` 和 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-209">Overrides `OnActionExecuting` and `OnActionExecuted`.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/TestController.cs?name=snippet)]

<span data-ttu-id="6b59b-210">导航到 `https://localhost:5001/Test/FilterTest2` 运行以下代码：</span><span class="sxs-lookup"><span data-stu-id="6b59b-210">Navigating to `https://localhost:5001/Test/FilterTest2` runs the following code:</span></span>

* `TestController.OnActionExecuting`
  * `MySampleActionFilter.OnActionExecuting`
    * `SampleActionFilterAttribute.OnActionExecuting`
      * `TestController.FilterTest2`
    * `SampleActionFilterAttribute.OnActionExecuted`
  * `MySampleActionFilter.OnActionExecuted`
* `TestController.OnActionExecuted`

<span data-ttu-id="6b59b-211">对于 Razor Pages，请参阅[通过重写筛选器方法实现 Razor 页面筛选器](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-211">For Razor Pages, see [Implement Razor Page filters by overriding filter methods](xref:razor-pages/filter#implement-razor-page-filters-by-overriding-filter-methods).</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="6b59b-212">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="6b59b-212">Overriding the default order</span></span>

<span data-ttu-id="6b59b-213">可以通过实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter> 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="6b59b-213">The default sequence of execution can be overridden by implementing <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter>.</span></span> <span data-ttu-id="6b59b-214">`IOrderedFilter` 公开了 <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="6b59b-214">`IOrderedFilter` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IOrderedFilter.Order> property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="6b59b-215">具有较低的 `Order` 值的筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-215">A filter with a lower `Order` value:</span></span>

* <span data-ttu-id="6b59b-216">在具有较高的 `Order` 值的筛选器之前运行 before  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-216">Runs the *before* code before that of a filter with a higher value of `Order`.</span></span>
* <span data-ttu-id="6b59b-217">在具有较高的 `Order` 值的筛选器之后运行 after  代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-217">Runs the *after* code after that of a filter with a higher `Order` value.</span></span>

<span data-ttu-id="6b59b-218">可以使用构造函数参数设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="6b59b-218">The `Order` property can be set with a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="6b59b-219">请考虑前面示例中所示的 3 个相同操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-219">Consider the same 3 action filters shown in the preceding example.</span></span> <span data-ttu-id="6b59b-220">如果控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="6b59b-220">If the `Order` property of the controller and global filters is set to 1 and 2 respectively, the order of execution is reversed.</span></span>

| <span data-ttu-id="6b59b-221">序列</span><span class="sxs-lookup"><span data-stu-id="6b59b-221">Sequence</span></span> | <span data-ttu-id="6b59b-222">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="6b59b-222">Filter scope</span></span> | <span data-ttu-id="6b59b-223">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="6b59b-223">`Order` property</span></span> | <span data-ttu-id="6b59b-224">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="6b59b-224">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="6b59b-225">1</span><span class="sxs-lookup"><span data-stu-id="6b59b-225">1</span></span> | <span data-ttu-id="6b59b-226">方法</span><span class="sxs-lookup"><span data-stu-id="6b59b-226">Method</span></span> | <span data-ttu-id="6b59b-227">0</span><span class="sxs-lookup"><span data-stu-id="6b59b-227">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="6b59b-228">2</span><span class="sxs-lookup"><span data-stu-id="6b59b-228">2</span></span> | <span data-ttu-id="6b59b-229">控制器</span><span class="sxs-lookup"><span data-stu-id="6b59b-229">Controller</span></span> | <span data-ttu-id="6b59b-230">1</span><span class="sxs-lookup"><span data-stu-id="6b59b-230">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6b59b-231">3</span><span class="sxs-lookup"><span data-stu-id="6b59b-231">3</span></span> | <span data-ttu-id="6b59b-232">Global</span><span class="sxs-lookup"><span data-stu-id="6b59b-232">Global</span></span> | <span data-ttu-id="6b59b-233">2</span><span class="sxs-lookup"><span data-stu-id="6b59b-233">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="6b59b-234">4</span><span class="sxs-lookup"><span data-stu-id="6b59b-234">4</span></span> | <span data-ttu-id="6b59b-235">Global</span><span class="sxs-lookup"><span data-stu-id="6b59b-235">Global</span></span> | <span data-ttu-id="6b59b-236">2</span><span class="sxs-lookup"><span data-stu-id="6b59b-236">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6b59b-237">5</span><span class="sxs-lookup"><span data-stu-id="6b59b-237">5</span></span> | <span data-ttu-id="6b59b-238">控制器</span><span class="sxs-lookup"><span data-stu-id="6b59b-238">Controller</span></span> | <span data-ttu-id="6b59b-239">1</span><span class="sxs-lookup"><span data-stu-id="6b59b-239">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="6b59b-240">6</span><span class="sxs-lookup"><span data-stu-id="6b59b-240">6</span></span> | <span data-ttu-id="6b59b-241">方法</span><span class="sxs-lookup"><span data-stu-id="6b59b-241">Method</span></span> | <span data-ttu-id="6b59b-242">0</span><span class="sxs-lookup"><span data-stu-id="6b59b-242">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="6b59b-243">在确定筛选器的运行顺序时，`Order` 属性重写作用域。</span><span class="sxs-lookup"><span data-stu-id="6b59b-243">The `Order` property overrides scope when determining the order in which filters run.</span></span> <span data-ttu-id="6b59b-244">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="6b59b-244">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="6b59b-245">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="6b59b-245">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="6b59b-246">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="6b59b-246">For built-in filters, scope determines order unless `Order` is set to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="6b59b-247">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="6b59b-247">Cancellation and short-circuiting</span></span>

<span data-ttu-id="6b59b-248">通过设置提供给筛选器方法的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> 参数上的 <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> 属性，可以使筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="6b59b-248">The filter pipeline can be short-circuited by setting the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext.Result> property on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResourceExecutingContext> parameter provided to the filter method.</span></span> <span data-ttu-id="6b59b-249">例如，以下资源筛选器将阻止执行管道的其余阶段：</span><span class="sxs-lookup"><span data-stu-id="6b59b-249">For instance, the following Resource filter prevents the rest of the pipeline from executing:</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?name=snippet)]

<span data-ttu-id="6b59b-250">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="6b59b-250">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="6b59b-251">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="6b59b-251">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="6b59b-252">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-252">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="6b59b-253">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="6b59b-253">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="6b59b-254">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-254">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="6b59b-255">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="6b59b-255">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="6b59b-256">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="6b59b-256">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="6b59b-257">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="6b59b-257">Dependency injection</span></span>

<span data-ttu-id="6b59b-258">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-258">Filters can be added by type or by instance.</span></span> <span data-ttu-id="6b59b-259">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="6b59b-259">If an instance is added, that instance is used for every request.</span></span> <span data-ttu-id="6b59b-260">如果添加类型，则将激活该类型。</span><span class="sxs-lookup"><span data-stu-id="6b59b-260">If a type is added, it's type-activated.</span></span> <span data-ttu-id="6b59b-261">激活类型的筛选器意味着：</span><span class="sxs-lookup"><span data-stu-id="6b59b-261">A type-activated filter means:</span></span>

* <span data-ttu-id="6b59b-262">将为每个请求创建一个实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-262">An instance is created for each request.</span></span>
* <span data-ttu-id="6b59b-263">[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="6b59b-263">Any constructor dependencies are populated by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span>

<span data-ttu-id="6b59b-264">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](xref:fundamentals/dependency-injection) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="6b59b-264">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](xref:fundamentals/dependency-injection) (DI).</span></span> <span data-ttu-id="6b59b-265">无法由 DI 提供构造函数依赖项，因为：</span><span class="sxs-lookup"><span data-stu-id="6b59b-265">Constructor dependencies cannot be provided by DI because:</span></span>

* <span data-ttu-id="6b59b-266">属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="6b59b-266">Attributes must have their constructor parameters supplied where they're applied.</span></span> 
* <span data-ttu-id="6b59b-267">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="6b59b-267">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="6b59b-268">以下筛选器支持从 DI 提供的构造函数依赖项：</span><span class="sxs-lookup"><span data-stu-id="6b59b-268">The following filters support constructor dependencies provided from DI:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>
* <xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute>
* <span data-ttu-id="6b59b-269">在属性上实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-269"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implemented on the attribute.</span></span>

<span data-ttu-id="6b59b-270">可以将前面的筛选器应用于控制器或操作方法：</span><span class="sxs-lookup"><span data-stu-id="6b59b-270">The preceding filters can be applied to a controller or action method:</span></span>

<span data-ttu-id="6b59b-271">可以从 DI 获取记录器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-271">Loggers are available from DI.</span></span> <span data-ttu-id="6b59b-272">但是，避免创建和使用筛选器仅用于日志记录。</span><span class="sxs-lookup"><span data-stu-id="6b59b-272">However, avoid creating and using filters purely for logging purposes.</span></span> <span data-ttu-id="6b59b-273">[内置框架日志记录](xref:fundamentals/logging/index)通常提供日志记录所需的内容。</span><span class="sxs-lookup"><span data-stu-id="6b59b-273">The [built-in framework logging](xref:fundamentals/logging/index) typically provides what's needed for logging.</span></span> <span data-ttu-id="6b59b-274">添加到筛选器的日志记录：</span><span class="sxs-lookup"><span data-stu-id="6b59b-274">Logging added to filters:</span></span>

* <span data-ttu-id="6b59b-275">应重点关注业务域问题或特定于筛选器的行为。</span><span class="sxs-lookup"><span data-stu-id="6b59b-275">Should focus on business domain concerns or behavior specific to the filter.</span></span>
* <span data-ttu-id="6b59b-276">不应记录操作或其他框架事件  。</span><span class="sxs-lookup"><span data-stu-id="6b59b-276">Should **not** log actions or other framework events.</span></span> <span data-ttu-id="6b59b-277">内置筛选器记录操作和框架事件。</span><span class="sxs-lookup"><span data-stu-id="6b59b-277">The built in filters log actions and framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="6b59b-278">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6b59b-278">ServiceFilterAttribute</span></span>

<span data-ttu-id="6b59b-279">在 `ConfigureServices` 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="6b59b-279">Service filter implementation types are registered in `ConfigureServices`.</span></span> <span data-ttu-id="6b59b-280"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-280">A <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> retrieves an instance of the filter from DI.</span></span>

<span data-ttu-id="6b59b-281">以下代码显示 `AddHeaderResultServiceFilter`：</span><span class="sxs-lookup"><span data-stu-id="6b59b-281">The following code shows the `AddHeaderResultServiceFilter`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6b59b-282">在以下代码中，`AddHeaderResultServiceFilter` 将添加到 DI 容器中：</span><span class="sxs-lookup"><span data-stu-id="6b59b-282">In the following code, `AddHeaderResultServiceFilter` is added to the DI container:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=4)]

<span data-ttu-id="6b59b-283">在以下代码中，`ServiceFilter` 属性将从 DI 中检索 `AddHeaderResultServiceFilter` 筛选器的实例：</span><span class="sxs-lookup"><span data-stu-id="6b59b-283">In the following code, the `ServiceFilter` attribute retrieves an instance of the `AddHeaderResultServiceFilter` filter from DI:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="6b59b-284">使用 `ServiceFilterAttribute` 时，[ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="6b59b-284">When using `ServiceFilterAttribute`, setting [ServiceFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute.IsReusable):</span></span>

* <span data-ttu-id="6b59b-285">提供以下提示：筛选器实例可能在其创建的请求范围之外被重用。 </span><span class="sxs-lookup"><span data-stu-id="6b59b-285">Provides a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6b59b-286">ASP.NET Core 运行时不保证：</span><span class="sxs-lookup"><span data-stu-id="6b59b-286">The ASP.NET Core runtime doesn't guarantee:</span></span>

  * <span data-ttu-id="6b59b-287">将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-287">That a single instance of the filter will be created.</span></span>
  * <span data-ttu-id="6b59b-288">稍后不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-288">The filter will not be re-requested from the DI container at some later point.</span></span>

* <span data-ttu-id="6b59b-289">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-289">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

 <span data-ttu-id="6b59b-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-290"><xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6b59b-291">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-291">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6b59b-292">`CreateInstance` 从 DI 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="6b59b-292">`CreateInstance` loads the specified type from DI.</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="6b59b-293">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="6b59b-293">TypeFilterAttribute</span></span>

<span data-ttu-id="6b59b-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 与 <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute> 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="6b59b-294"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> is similar to <xref:Microsoft.AspNetCore.Mvc.ServiceFilterAttribute>, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="6b59b-295">它使用 <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName> 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="6b59b-295">It instantiates the type by using <xref:Microsoft.Extensions.DependencyInjection.ObjectFactory?displayProperty=fullName>.</span></span>

<span data-ttu-id="6b59b-296">因为不会直接从 DI 容器解析 `TypeFilterAttribute` 类型：</span><span class="sxs-lookup"><span data-stu-id="6b59b-296">Because `TypeFilterAttribute` types aren't resolved directly from the DI container:</span></span>

* <span data-ttu-id="6b59b-297">使用 `TypeFilterAttribute` 引用的类型不需要注册在 DI 容器中。</span><span class="sxs-lookup"><span data-stu-id="6b59b-297">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the DI container.</span></span>  <span data-ttu-id="6b59b-298">它们具备由 DI 容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="6b59b-298">They do have their dependencies fulfilled by the DI container.</span></span>
* <span data-ttu-id="6b59b-299">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="6b59b-299">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="6b59b-300">使用 `TypeFilterAttribute` 时，[TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable) 设置：</span><span class="sxs-lookup"><span data-stu-id="6b59b-300">When using `TypeFilterAttribute`, setting [TypeFilterAttribute.IsReusable](xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute.IsReusable):</span></span>
* <span data-ttu-id="6b59b-301">提供提示：筛选器实例可能在其创建的请求范围之外被重用。 </span><span class="sxs-lookup"><span data-stu-id="6b59b-301">Provides hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="6b59b-302">ASP.NET Core 运行时不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-302">The ASP.NET Core runtime provides no guarantees that a single instance of the filter will be created.</span></span>

* <span data-ttu-id="6b59b-303">不应与依赖于生命周期不同于单一实例的服务的筛选器一起使用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-303">Should not be used with a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="6b59b-304">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="6b59b-304">The following example shows how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

<!-- 
https://localhost:5001/home/hi?name=joe
VS debug window shows 
FiltersSample.Filters.LogConstantFilter:Information: Method 'Hi' called
-->

## <a name="authorization-filters"></a><span data-ttu-id="6b59b-305">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-305">Authorization filters</span></span>

<span data-ttu-id="6b59b-306">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-306">Authorization filters:</span></span>

* <span data-ttu-id="6b59b-307">是筛选器管道中运行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-307">Are the first filters run in the filter pipeline.</span></span>
* <span data-ttu-id="6b59b-308">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="6b59b-308">Control access to action methods.</span></span>
* <span data-ttu-id="6b59b-309">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-309">Have a before method, but no after method.</span></span>

<span data-ttu-id="6b59b-310">自定义授权筛选器需要自定义授权框架。</span><span class="sxs-lookup"><span data-stu-id="6b59b-310">Custom authorization filters require a custom authorization framework.</span></span> <span data-ttu-id="6b59b-311">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-311">Prefer configuring the authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="6b59b-312">内置授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-312">The built-in authorization filter:</span></span>

* <span data-ttu-id="6b59b-313">调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="6b59b-313">Calls the authorization system.</span></span>
* <span data-ttu-id="6b59b-314">不授权请求。</span><span class="sxs-lookup"><span data-stu-id="6b59b-314">Does not authorize requests.</span></span>

<span data-ttu-id="6b59b-315">不会在授权筛选器中引发异常  ：</span><span class="sxs-lookup"><span data-stu-id="6b59b-315">Do **not** throw exceptions within authorization filters:</span></span>

* <span data-ttu-id="6b59b-316">不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-316">The exception will not be handled.</span></span>
* <span data-ttu-id="6b59b-317">异常筛选器不会处理异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-317">Exception filters will not handle the exception.</span></span>

<span data-ttu-id="6b59b-318">在授权筛选器出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="6b59b-318">Consider issuing a challenge when an exception occurs in an authorization filter.</span></span>

<span data-ttu-id="6b59b-319">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-319">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="6b59b-320">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-320">Resource filters</span></span>

<span data-ttu-id="6b59b-321">资源筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-321">Resource filters:</span></span>

* <span data-ttu-id="6b59b-322">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="6b59b-322">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IResourceFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResourceFilter> interface.</span></span>
* <span data-ttu-id="6b59b-323">执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="6b59b-323">Execution wraps most of the filter pipeline.</span></span>
* <span data-ttu-id="6b59b-324">只有[授权筛选器](#authorization-filters)在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-324">Only [Authorization filters](#authorization-filters) run before resource filters.</span></span>

<span data-ttu-id="6b59b-325">如果要使大部分管道短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="6b59b-325">Resource filters are useful to short-circuit most of the pipeline.</span></span> <span data-ttu-id="6b59b-326">例如，如果缓存命中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="6b59b-326">For example, a caching filter can avoid the rest of the pipeline on a cache hit.</span></span>

<span data-ttu-id="6b59b-327">资源筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="6b59b-327">Resource filter examples:</span></span>

* <span data-ttu-id="6b59b-328">之前显示的[短路资源筛选器](#short-circuiting-resource-filter)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-328">[The short-circuiting resource filter](#short-circuiting-resource-filter) shown previously.</span></span>
* <span data-ttu-id="6b59b-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="6b59b-329">[DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/2.0.0-preview2/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

  * <span data-ttu-id="6b59b-330">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="6b59b-330">Prevents model binding from accessing the form data.</span></span>
  * <span data-ttu-id="6b59b-331">用于上传大型文件，以防止表单数据被读入内存。</span><span class="sxs-lookup"><span data-stu-id="6b59b-331">Used for large file uploads to prevent the form data from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="6b59b-332">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-332">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="6b59b-333">操作筛选器不  应用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="6b59b-333">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="6b59b-334">Razor Pages 支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-334">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="6b59b-335">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-335">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="6b59b-336">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-336">Action filters:</span></span>

* <span data-ttu-id="6b59b-337">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> 接口。</span><span class="sxs-lookup"><span data-stu-id="6b59b-337">Implement either the <xref:Microsoft.AspNetCore.Mvc.Filters.IActionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncActionFilter> interface.</span></span>
* <span data-ttu-id="6b59b-338">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-338">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="6b59b-339">以下代码显示示例操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-339">The following code shows a sample action filter:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MySampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="6b59b-340"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="6b59b-340">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="6b59b-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - 用于读取操作方法的输入。</span><span class="sxs-lookup"><span data-stu-id="6b59b-341"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.ActionArguments> - enables the inputs to an action method be read.</span></span>
* <span data-ttu-id="6b59b-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - 用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-342"><xref:Microsoft.AspNetCore.Mvc.Controller> - enables manipulating the controller instance.</span></span>
* <span data-ttu-id="6b59b-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - 设置 `Result` 会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="6b59b-343"><xref:System.Web.Mvc.ActionExecutingContext.Result> - setting `Result` short-circuits execution of the action method and subsequent action filters.</span></span>

<span data-ttu-id="6b59b-344">在操作方法中引发异常：</span><span class="sxs-lookup"><span data-stu-id="6b59b-344">Throwing an exception in an action method:</span></span>

* <span data-ttu-id="6b59b-345">防止运行后续筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-345">Prevents running of subsequent filters.</span></span>
* <span data-ttu-id="6b59b-346">与设置 `Result` 不同，结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="6b59b-346">Unlike setting `Result`, is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6b59b-347"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="6b59b-347">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="6b59b-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - 如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="6b59b-348"><xref:System.Web.Mvc.ActionExecutedContext.Canceled> - True if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6b59b-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - 如果操作或之前运行的操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="6b59b-349"><xref:System.Web.Mvc.ActionExecutedContext.Exception> - Non-null if the action or a previously run action filter threw an exception.</span></span> <span data-ttu-id="6b59b-350">将此属性设置为 null：</span><span class="sxs-lookup"><span data-stu-id="6b59b-350">Setting this property to null:</span></span>

  * <span data-ttu-id="6b59b-351">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-351">Effectively handles the exception.</span></span>
  * <span data-ttu-id="6b59b-352">执行 `Result`，从操作方法中将它返回。</span><span class="sxs-lookup"><span data-stu-id="6b59b-352">`Result` is executed as if it was returned from the action method.</span></span>

<span data-ttu-id="6b59b-353">对于 `IAsyncActionFilter`，一个向 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate> 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="6b59b-353">For an `IAsyncActionFilter`, a call to the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutionDelegate>:</span></span>

* <span data-ttu-id="6b59b-354">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-354">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="6b59b-355">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-355">Returns `ActionExecutedContext`.</span></span>

<span data-ttu-id="6b59b-356">若要设置短路，可将 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> 分配到某个结果实例，并且不调用 `next` (`ActionExecutionDelegate`)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-356">To short-circuit, assign <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext.Result?displayProperty=fullName> to a result instance and don't call `next` (the `ActionExecutionDelegate`).</span></span>

<span data-ttu-id="6b59b-357">该框架提供一个可子类化的抽象 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-357">The framework provides an abstract <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> that can be subclassed.</span></span>

<span data-ttu-id="6b59b-358">`OnActionExecuting` 操作筛选器可用于：</span><span class="sxs-lookup"><span data-stu-id="6b59b-358">The `OnActionExecuting` action filter can be used to:</span></span>

* <span data-ttu-id="6b59b-359">验证模型状态。</span><span class="sxs-lookup"><span data-stu-id="6b59b-359">Validate model state.</span></span>
* <span data-ttu-id="6b59b-360">如果状态无效，则返回错误。</span><span class="sxs-lookup"><span data-stu-id="6b59b-360">Return an error if the state is invalid.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet)]

<span data-ttu-id="6b59b-361">`OnActionExecuted` 方法在操作方法之后运行：</span><span class="sxs-lookup"><span data-stu-id="6b59b-361">The `OnActionExecuted` method runs after the action method:</span></span>

* <span data-ttu-id="6b59b-362">可通过 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="6b59b-362">And can see and manipulate the results of the action through the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Result> property.</span></span>
* <span data-ttu-id="6b59b-363">如果操作执行已被另一个筛选器设置短路，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="6b59b-363"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Canceled> is set to true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="6b59b-364">如果操作或后续操作筛选器引发了异常，则 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="6b59b-364"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext.Exception> is set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="6b59b-365">将 `Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="6b59b-365">Setting `Exception` to null:</span></span>

  * <span data-ttu-id="6b59b-366">有效地处理异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-366">Effectively handles an exception.</span></span>
  * <span data-ttu-id="6b59b-367">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="6b59b-367">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/ValidateModelAttribute.cs?name=snippet2&higlight=12-99)]

## <a name="exception-filters"></a><span data-ttu-id="6b59b-368">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-368">Exception filters</span></span>

<span data-ttu-id="6b59b-369">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-369">Exception filters:</span></span>

* <span data-ttu-id="6b59b-370">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-370">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter>.</span></span> 
* <span data-ttu-id="6b59b-371">可用于实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="6b59b-371">Can be used to implement common error handling policies.</span></span>

<span data-ttu-id="6b59b-372">下面的异常筛选器示例使用自定义错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="6b59b-372">The following sample exception filter uses a custom error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=16-19)]

<span data-ttu-id="6b59b-373">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-373">Exception filters:</span></span>

* <span data-ttu-id="6b59b-374">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="6b59b-374">Don't have before and after events.</span></span>
* <span data-ttu-id="6b59b-375">实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-375">Implement <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter.OnException*> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncExceptionFilter.OnExceptionAsync*>.</span></span>
* <span data-ttu-id="6b59b-376">处理 Razor 页面或控制器创建、[模型绑定](xref:mvc/models/model-binding)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-376">Handle unhandled exceptions that occur in Razor Page or controller creation, [model binding](xref:mvc/models/model-binding), action filters, or action methods.</span></span>
* <span data-ttu-id="6b59b-377">请不要捕获资源筛选器、结果筛选器或 MVC 结果执行中发生的异常  。</span><span class="sxs-lookup"><span data-stu-id="6b59b-377">Do **not** catch exceptions that occur in resource filters, result filters, or MVC result execution.</span></span>

<span data-ttu-id="6b59b-378">若要处理异常，请将 <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> 属性设置为 `true`，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="6b59b-378">To handle an exception, set the <xref:System.Web.Mvc.ExceptionContext.ExceptionHandled> property to `true` or write a response.</span></span> <span data-ttu-id="6b59b-379">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-379">This stops propagation of the exception.</span></span> <span data-ttu-id="6b59b-380">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="6b59b-380">An exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="6b59b-381">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="6b59b-381">Only an action filter can do that.</span></span>

<span data-ttu-id="6b59b-382">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-382">Exception filters:</span></span>

* <span data-ttu-id="6b59b-383">非常适合捕获发生在操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-383">Are good for trapping exceptions that occur within actions.</span></span>
* <span data-ttu-id="6b59b-384">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="6b59b-384">Are not as flexible as error handling middleware.</span></span>

<span data-ttu-id="6b59b-385">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-385">Prefer middleware for exception handling.</span></span> <span data-ttu-id="6b59b-386">基于所调用的操作方法，仅当错误处理不同时，才使用异常筛选器  。</span><span class="sxs-lookup"><span data-stu-id="6b59b-386">Use exception filters only where error handling *differs* based on which action method is called.</span></span> <span data-ttu-id="6b59b-387">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-387">For example, an app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="6b59b-388">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="6b59b-388">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

## <a name="result-filters"></a><span data-ttu-id="6b59b-389">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="6b59b-389">Result filters</span></span>

<span data-ttu-id="6b59b-390">结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-390">Result filters:</span></span>

* <span data-ttu-id="6b59b-391">实现接口：</span><span class="sxs-lookup"><span data-stu-id="6b59b-391">Implement an interface:</span></span>
  * <span data-ttu-id="6b59b-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span><span class="sxs-lookup"><span data-stu-id="6b59b-392"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter></span></span>
  * <span data-ttu-id="6b59b-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span><span class="sxs-lookup"><span data-stu-id="6b59b-393"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter></span></span>
* <span data-ttu-id="6b59b-394">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-394">Their execution surrounds the execution of action results.</span></span>

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="6b59b-395">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="6b59b-395">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="6b59b-396">以下代码显示一个添加 HTTP 标头的结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-396">The following code shows a result filter that adds an HTTP header:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="6b59b-397">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="6b59b-397">The kind of result being executed depends on the action.</span></span> <span data-ttu-id="6b59b-398">返回视图的操作会将所有 Razor 处理作为要执行的 <xref:Microsoft.AspNetCore.Mvc.ViewResult> 的一部分。</span><span class="sxs-lookup"><span data-stu-id="6b59b-398">An action returning a view would include all razor processing as part of the <xref:Microsoft.AspNetCore.Mvc.ViewResult> being executed.</span></span> <span data-ttu-id="6b59b-399">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="6b59b-399">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="6b59b-400">详细了解[操作结果](xref:mvc/controllers/actions)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-400">Learn more about [action results](xref:mvc/controllers/actions).</span></span>

<span data-ttu-id="6b59b-401">仅当操作或操作筛选器生成操作结果时，才会执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-401">Result filters are only executed when an action or action filter produces an action result.</span></span> <span data-ttu-id="6b59b-402">不会在以下情况下执行结果筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-402">Result filters are not executed when:</span></span>

* <span data-ttu-id="6b59b-403">授权筛选器或资源筛选器使管道短路。</span><span class="sxs-lookup"><span data-stu-id="6b59b-403">An authorization filter or resource filter short-circuits the pipeline.</span></span>
* <span data-ttu-id="6b59b-404">异常筛选器通过生成操作结果来处理异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-404">An exception filter handles an exception by producing an action result.</span></span>

<span data-ttu-id="6b59b-405"><xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> 方法可以将 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> 设置为 `true`，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="6b59b-405">The <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuting*?displayProperty=fullName> method can short-circuit execution of the action result and subsequent result filters by setting <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel?displayProperty=fullName> to `true`.</span></span> <span data-ttu-id="6b59b-406">设置短路时写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="6b59b-406">Write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="6b59b-407">如果在 `IResultFilter.OnResultExecuting` 中引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="6b59b-407">Throwing an exception in `IResultFilter.OnResultExecuting` will:</span></span>

* <span data-ttu-id="6b59b-408">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-408">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="6b59b-409">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="6b59b-409">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="6b59b-410">当 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> 方法运行时：</span><span class="sxs-lookup"><span data-stu-id="6b59b-410">When the <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter.OnResultExecuted*?displayProperty=fullName> method runs:</span></span>

* <span data-ttu-id="6b59b-411">响应可能已发送到客户端，且无法更改。</span><span class="sxs-lookup"><span data-stu-id="6b59b-411">The response has likely been sent to the client and cannot be changed.</span></span>
* <span data-ttu-id="6b59b-412">如果引发了异常，则不会发送响应正文。</span><span class="sxs-lookup"><span data-stu-id="6b59b-412">If an exception was thrown, the response body is not sent.</span></span>

<!-- Review preceding "If an exception was thrown: Original 
When the OnResultExecuted method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).

SHould that be , 
If an exception was thrown **IN THE RESULT FILTER**, the response body is not sent.

 -->

<span data-ttu-id="6b59b-413">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-413">`ResultExecutedContext.Canceled` is set to `true` if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="6b59b-414">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="6b59b-414">`ResultExecutedContext.Exception` is set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="6b59b-415">将 `Exception` 设置为 NULL 可有效地处理异常，并防止 ASP.NET Core 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="6b59b-415">Setting `Exception` to null effectively handles an exception and prevents the exception from being rethrown by ASP.NET Core later in the pipeline.</span></span> <span data-ttu-id="6b59b-416">处理结果筛选器中出现的异常时，没有可靠的方法来将数据写入响应。</span><span class="sxs-lookup"><span data-stu-id="6b59b-416">There is no reliable way to write data to a response when handling an exception in a result filter.</span></span> <span data-ttu-id="6b59b-417">如果在操作结果引发异常时标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="6b59b-417">If the headers have been flushed to the client when an action result throws an exception, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="6b59b-418">对于 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>，通过调用 <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="6b59b-418">For an <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncResultFilter>, a call to `await next` on the <xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutionDelegate> executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="6b59b-419">若要设置短路，请将 [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) 设置为 `true`，并且不调用 `ResultExecutionDelegate`：</span><span class="sxs-lookup"><span data-stu-id="6b59b-419">To short-circuit, set [ResultExecutingContext.Cancel](xref:Microsoft.AspNetCore.Mvc.Filters.ResultExecutingContext.Cancel) to `true` and don't call the `ResultExecutionDelegate`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/MyAsyncResponseFilter.cs?name=snippet)]

<span data-ttu-id="6b59b-420">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-420">The framework provides an abstract `ResultFilterAttribute` that can be subclassed.</span></span> <span data-ttu-id="6b59b-421">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="6b59b-421">The [AddHeaderAttribute](#add-header-attribute) class shown previously is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="6b59b-422">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="6b59b-422">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="6b59b-423"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对所有操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="6b59b-423">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for all action results.</span></span> <span data-ttu-id="6b59b-424">这包括由以下对象生成的操作结果：</span><span class="sxs-lookup"><span data-stu-id="6b59b-424">This includes action results produced by:</span></span>

* <span data-ttu-id="6b59b-425">设置短路的授权筛选器和资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-425">Authorization filters and resource filters that short-circuit.</span></span>
* <span data-ttu-id="6b59b-426">异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="6b59b-426">Exception filters.</span></span>

<span data-ttu-id="6b59b-427">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”  状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="6b59b-427">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/UnprocessableResultFilter.cs?name=snippet)]

### <a name="ifilterfactory"></a><span data-ttu-id="6b59b-428">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6b59b-428">IFilterFactory</span></span>

<span data-ttu-id="6b59b-429"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-429"><xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="6b59b-430">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-430">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="6b59b-431">当运行时准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="6b59b-431">When the runtime prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="6b59b-432">如果转换成功，则调用 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="6b59b-432">If that cast succeeds, the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method is called to create the `IFilterMetadata` instance that is invoked.</span></span> <span data-ttu-id="6b59b-433">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="6b59b-433">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="6b59b-434">可以使用自定义属性实现来实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="6b59b-434">`IFilterFactory` can be implemented using custom attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

<span data-ttu-id="6b59b-435">可以通过运行[下载示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)来测试前面的代码：</span><span class="sxs-lookup"><span data-stu-id="6b59b-435">The preceding code can be tested by running the [download sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample):</span></span>

* <span data-ttu-id="6b59b-436">调用 F12 开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="6b59b-436">Invoke the F12 developer tools.</span></span>
* <span data-ttu-id="6b59b-437">导航到 `https://localhost:5001/Sample/HeaderWithFactory`</span><span class="sxs-lookup"><span data-stu-id="6b59b-437">Navigate to `https://localhost:5001/Sample/HeaderWithFactory`</span></span>

<span data-ttu-id="6b59b-438">F12 开发人员工具显示示例代码添加的以下响应标头：</span><span class="sxs-lookup"><span data-stu-id="6b59b-438">The F12 developer tools display the following response headers added by the sample code:</span></span>

* <span data-ttu-id="6b59b-439">**author:** `Joe Smith`</span><span class="sxs-lookup"><span data-stu-id="6b59b-439">**author:** `Joe Smith`</span></span>
* <span data-ttu-id="6b59b-440">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span><span class="sxs-lookup"><span data-stu-id="6b59b-440">**globaladdheader:** `Result filter added to MvcOptions.Filters`</span></span>
* <span data-ttu-id="6b59b-441">**internal:** `My header`</span><span class="sxs-lookup"><span data-stu-id="6b59b-441">**internal:** `My header`</span></span>

<span data-ttu-id="6b59b-442">前面的代码创建 **internal:** `My header` 响应标头。</span><span class="sxs-lookup"><span data-stu-id="6b59b-442">The preceding code creates the **internal:** `My header` response header.</span></span>

### <a name="ifilterfactory-implemented-on-an-attribute"></a><span data-ttu-id="6b59b-443">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="6b59b-443">IFilterFactory implemented on an attribute</span></span>

<!-- Review 
This section needs to be rewritten.
What's a non-named attribute?
-->

<span data-ttu-id="6b59b-444">实现 `IFilterFactory` 的筛选器可用于以下筛选器：</span><span class="sxs-lookup"><span data-stu-id="6b59b-444">Filters that implement `IFilterFactory` are useful for filters that:</span></span>

* <span data-ttu-id="6b59b-445">不需要传递参数。</span><span class="sxs-lookup"><span data-stu-id="6b59b-445">Don't require passing parameters.</span></span>
* <span data-ttu-id="6b59b-446">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="6b59b-446">Have constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="6b59b-447"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> 可实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>。</span><span class="sxs-lookup"><span data-stu-id="6b59b-447"><xref:Microsoft.AspNetCore.Mvc.TypeFilterAttribute> implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory>.</span></span> <span data-ttu-id="6b59b-448">`IFilterFactory` 公开用于创建 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> 实例的 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> 方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-448">`IFilterFactory` exposes the <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterFactory.CreateInstance*> method for creating an <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata> instance.</span></span> <span data-ttu-id="6b59b-449">`CreateInstance` 从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="6b59b-449">`CreateInstance` loads the specified type from the services container (DI).</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="6b59b-450">以下代码显示应用 `[SampleActionFilter]` 的三种方法：</span><span class="sxs-lookup"><span data-stu-id="6b59b-450">The following code shows three approaches to applying the `[SampleActionFilter]`:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet&highlight=1)]

<span data-ttu-id="6b59b-451">在前面的代码中，使用 `[SampleActionFilter]` 修饰方法是应用 `SampleActionFilter` 的首选方法。</span><span class="sxs-lookup"><span data-stu-id="6b59b-451">In the preceding code, decorating the method with `[SampleActionFilter]` is the preferred approach to applying the `SampleActionFilter`.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="6b59b-452">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="6b59b-452">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="6b59b-453">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="6b59b-453">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="6b59b-454">但筛选器又不同于中间件，它们是 ASP.NET Core 运行时的一部分，这意味着它们有权访问 ASP.NET Core 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="6b59b-454">But filters differ from middleware in that they're part of the ASP.NET Core runtime, which means that they have access to ASP.NET Core context and constructs.</span></span>

<span data-ttu-id="6b59b-455">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="6b59b-455">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware to inject into the filter pipeline.</span></span> <span data-ttu-id="6b59b-456">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="6b59b-456">The following example uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="6b59b-457">使用 <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> 运行中间件：</span><span class="sxs-lookup"><span data-stu-id="6b59b-457">Use the <xref:Microsoft.AspNetCore.Mvc.MiddlewareFilterAttribute> to run the middleware:</span></span>

[!code-csharp[](./filters/sample/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="6b59b-458">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="6b59b-458">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="6b59b-459">后续操作</span><span class="sxs-lookup"><span data-stu-id="6b59b-459">Next actions</span></span>

* <span data-ttu-id="6b59b-460">请参阅 [Razor Pages 的筛选器方法](xref:razor-pages/filter)</span><span class="sxs-lookup"><span data-stu-id="6b59b-460">See [Filter methods for Razor Pages](xref:razor-pages/filter)</span></span>
* <span data-ttu-id="6b59b-461">若要尝试使用筛选器，请[下载、测试并修改 GitHub 示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="6b59b-461">To experiment with filters, [download, test, and modify the GitHub sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
