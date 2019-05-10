---
title: ASP.NET Core 中的筛选器
author: ardalis
description: 了解筛选器的工作原理以及如何在 ASP.NET Core MVC 中使用它们。
ms.author: riande
ms.custom: mvc
ms.date: 02/08/2019
uid: mvc/controllers/filters
ms.openlocfilehash: f357df0bbc51e881132e36ccb20f4ffdc3035032
ms.sourcegitcommit: 5b0eca8c21550f95de3bb21096bd4fd4d9098026
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/27/2019
ms.locfileid: "64883462"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="a2666-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="a2666-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="a2666-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="a2666-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="a2666-105">通过使用 ASP.NET Core MVC 中的筛选器，可在请求处理管道中的特定阶段之前或之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-105">*Filters* in ASP.NET Core MVC allow you to run code before or after specific stages in the request processing pipeline.</span></span>

<span data-ttu-id="a2666-106">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="a2666-106">Built-in filters handle tasks such as:</span></span>

* <span data-ttu-id="a2666-107">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="a2666-107">Authorization (preventing access to resources a user isn't authorized for).</span></span>
* <span data-ttu-id="a2666-108">确保所有请求都使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="a2666-108">Ensuring that all requests use HTTPS.</span></span>
* <span data-ttu-id="a2666-109">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="a2666-109">Response caching (short-circuiting the request pipeline to return a cached response).</span></span> 

<span data-ttu-id="a2666-110">可以创建自定义筛选器，用于处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="a2666-110">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="a2666-111">筛选器可以避免跨操作复制代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-111">Filters can avoid duplicating code across actions.</span></span> <span data-ttu-id="a2666-112">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="a2666-112">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="a2666-113">[查看或下载 GitHub 中的示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="a2666-113">[View or download sample from GitHub](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

## <a name="how-filters-work"></a><span data-ttu-id="a2666-114">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="a2666-114">How filters work</span></span>

<span data-ttu-id="a2666-115">筛选器在 *MVC 操作调用管道*（有时称为*筛选器管道*）内运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-115">Filters run within the *MVC action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="a2666-116">筛选器管道在 MVC 选择了要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-116">The filter pipeline runs after MVC selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 MVC 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="a2666-119">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="a2666-119">Filter types</span></span>

<span data-ttu-id="a2666-120">每种筛选器类型都在筛选器管道中的不同阶段执行。</span><span class="sxs-lookup"><span data-stu-id="a2666-120">Each filter type is executed at a different stage in the filter pipeline.</span></span>

* <span data-ttu-id="a2666-121">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对当前请求为当前用户授权。</span><span class="sxs-lookup"><span data-stu-id="a2666-121">[Authorization filters](#authorization-filters) run first and are used to determine whether the current user is authorized for the current request.</span></span> <span data-ttu-id="a2666-122">如果请求未获授权，它们可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-122">They can short-circuit the pipeline if a request is unauthorized.</span></span> 

* <span data-ttu-id="a2666-123">[资源筛选器](#resource-filters)是授权后最先处理请求的筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-123">[Resource filters](#resource-filters) are the first to handle a request after authorization.</span></span>  <span data-ttu-id="a2666-124">它们可以在筛选器管道的其余阶段运行之前以及管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-124">They can run code before the rest of the filter pipeline, and after the rest of the pipeline has completed.</span></span> <span data-ttu-id="a2666-125">出于性能方面的考虑，可以使用它们来实现缓存或以其他方式让筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-125">They're useful to implement caching or otherwise short-circuit the filter pipeline for performance reasons.</span></span> <span data-ttu-id="a2666-126">它们在模型绑定之前运行，所以可以影响模型绑定。</span><span class="sxs-lookup"><span data-stu-id="a2666-126">They run before model binding, so they can influence model binding.</span></span>

* <span data-ttu-id="a2666-127">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-127">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="a2666-128">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="a2666-128">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span> <span data-ttu-id="a2666-129">不可在 Razor Pages 中使用操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-129">Action filters are not supported in Razor Pages.</span></span>

* <span data-ttu-id="a2666-130">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="a2666-130">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="a2666-131">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-131">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="a2666-132">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-132">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="a2666-133">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="a2666-133">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="a2666-134">下图展示了这些筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="a2666-134">The following diagram shows how these filter types interact in the filter pipeline.</span></span>

![请求通过授权筛选器、资源筛选器、模型绑定、操作筛选器、操作执行和操作结果转换、异常筛选器、结果筛选器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="a2666-137">实现</span><span class="sxs-lookup"><span data-stu-id="a2666-137">Implementation</span></span>

<span data-ttu-id="a2666-138">通过不同的接口定义，筛选器同时支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="a2666-138">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span> 

<span data-ttu-id="a2666-139">可在其管道阶段之前和之后运行代码的同步筛选器定义 On*Stage*Executing 方法和 On*Stage*Executed 方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-139">Synchronous filters that can run code both before and after their pipeline stage define On*Stage*Executing and On*Stage*Executed methods.</span></span> <span data-ttu-id="a2666-140">例如，在调用操作方法之前调用 `OnActionExecuting`，在操作方法返回之后调用 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="a2666-140">For example, `OnActionExecuting` is called before the action method is called, and `OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet1)]

<span data-ttu-id="a2666-141">异步筛选器定义单一的 On*Stage*ExecutionAsync 方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-141">Asynchronous filters define a single On*Stage*ExecutionAsync method.</span></span> <span data-ttu-id="a2666-142">此方法采用 *FilterType*ExecutionDelegate 委托来执行筛选器的管道阶段。</span><span class="sxs-lookup"><span data-stu-id="a2666-142">This method takes a *FilterType*ExecutionDelegate delegate which executes the filter's pipeline stage.</span></span> <span data-ttu-id="a2666-143">例如，`ActionExecutionDelegate` 调用该操作方法或下一个操作筛选器，用户可以在调用它之前和之后执行代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-143">For example, `ActionExecutionDelegate` calls the action method or next action filter, and you can execute code before and after you call it.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

<span data-ttu-id="a2666-144">可以在单个类中为多个筛选器阶段实现接口。</span><span class="sxs-lookup"><span data-stu-id="a2666-144">You can implement interfaces for multiple filter stages in a single class.</span></span> <span data-ttu-id="a2666-145">例如，<xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> 类实现 `IActionFilter`、`IResultFilter` 及其异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="a2666-145">For example, the <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

> [!NOTE]
> <span data-ttu-id="a2666-146">筛选器接口的同步和异步版本**任意**实现一个，而不是同时实现。</span><span class="sxs-lookup"><span data-stu-id="a2666-146">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="a2666-147">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="a2666-147">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="a2666-148">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-148">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="a2666-149">如果在一个类中同时实现了这两种接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-149">If you were to implement both interfaces on one class, only the async method would be called.</span></span> <span data-ttu-id="a2666-150">使用抽象类时（如 <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute>），将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-150">When using abstract classes like <xref:Microsoft.AspNetCore.Mvc.Filters.ActionFilterAttribute> you would override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="ifilterfactory"></a><span data-ttu-id="a2666-151">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a2666-151">IFilterFactory</span></span>

<span data-ttu-id="a2666-152">[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) 实现 <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>。</span><span class="sxs-lookup"><span data-stu-id="a2666-152">[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) implements <xref:Microsoft.AspNetCore.Mvc.Filters.IFilterMetadata>.</span></span> <span data-ttu-id="a2666-153">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="a2666-153">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="a2666-154">当该框架准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="a2666-154">When the framework prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="a2666-155">如果强制转换成功，则调用 [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="a2666-155">If that cast succeeds, the [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) method is called to create the `IFilterMetadata` instance that will be invoked.</span></span> <span data-ttu-id="a2666-156">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="a2666-156">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="a2666-157">用户可以在自己的属性实现上实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="a2666-157">You can implement `IFilterFactory` on your own attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a><span data-ttu-id="a2666-158">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="a2666-158">Built-in filter attributes</span></span>

<span data-ttu-id="a2666-159">该框架包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-159">The framework includes built-in attribute-based filters that you can subclass and customize.</span></span> <span data-ttu-id="a2666-160">例如，以下结果筛选器会向响应添加标头。</span><span class="sxs-lookup"><span data-stu-id="a2666-160">For example, the following Result filter adds a header to the response.</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

<span data-ttu-id="a2666-161">通过使用属性，筛选器可接收参数，如上面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="a2666-161">Attributes allow filters to accept arguments, as shown in the example above.</span></span> <span data-ttu-id="a2666-162">可将此属性添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="a2666-162">You would add this attribute to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="a2666-163">`Index` 操作的结果如下所示：响应标头显示在右下角。</span><span class="sxs-lookup"><span data-stu-id="a2666-163">The result of the `Index` action is shown below - the response headers are displayed on the bottom right.</span></span>

![显示响应标头（包括 Author Steve Smith @ardalis）的 Microsoft Edge 开发人员工具](filters/_static/add-header.png)

<span data-ttu-id="a2666-165">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="a2666-165">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="a2666-166">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="a2666-166">Filter attributes:</span></span>

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

<span data-ttu-id="a2666-167">[本文稍后](#dependency-injection)会对 `TypeFilterAttribute` 和 `ServiceFilterAttribute` 进行介绍。</span><span class="sxs-lookup"><span data-stu-id="a2666-167">`TypeFilterAttribute` and `ServiceFilterAttribute` are explained [later in this article](#dependency-injection).</span></span>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="a2666-168">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="a2666-168">Filter scopes and order of execution</span></span>

<span data-ttu-id="a2666-169">可以将筛选器添加到管道中的三个*作用域*之一。</span><span class="sxs-lookup"><span data-stu-id="a2666-169">A filter can be added to the pipeline at one of three *scopes*.</span></span> <span data-ttu-id="a2666-170">可以使用属性将筛选器添加到特定的操作方法或控制器类。</span><span class="sxs-lookup"><span data-stu-id="a2666-170">You can add a filter to a particular action method or to a controller class by using an attribute.</span></span> <span data-ttu-id="a2666-171">或者，也可以注册所有控制器和操作的全局筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-171">Or you can register a filter globally for all controllers and actions.</span></span> <span data-ttu-id="a2666-172">通过将筛选器添加到 `ConfigureServices` 中的 `MvcOptions.Filters` 集合，可以将其添加为全局筛选器：</span><span class="sxs-lookup"><span data-stu-id="a2666-172">Filters are added globally by adding it to the `MvcOptions.Filters` collection in `ConfigureServices`:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a><span data-ttu-id="a2666-173">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="a2666-173">Default order of execution</span></span>

<span data-ttu-id="a2666-174">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="a2666-174">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="a2666-175">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-175">Global filters surround class filters, which in turn surround method filters.</span></span> <span data-ttu-id="a2666-176">这种模式有时称为“俄罗斯套娃”嵌套，因为增加的每个作用域都包装在前一个作用域中，就像[套娃](https://wikipedia.org/wiki/Matryoshka_doll)一样。</span><span class="sxs-lookup"><span data-stu-id="a2666-176">This is sometimes referred to as "Russian doll" nesting, as each increase in scope is wrapped around the previous scope, like a [nesting doll](https://wikipedia.org/wiki/Matryoshka_doll).</span></span> <span data-ttu-id="a2666-177">通常情况下，无需显式确定排序便可获得所需的重写行为。</span><span class="sxs-lookup"><span data-stu-id="a2666-177">You generally get the desired overriding behavior without having to explicitly determine ordering.</span></span>

<span data-ttu-id="a2666-178">在这种嵌套模式下，筛选器的 *after* 代码会按照与 *before* 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-178">As a result of this nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="a2666-179">其序列如下所示：</span><span class="sxs-lookup"><span data-stu-id="a2666-179">The sequence looks like this:</span></span>

* <span data-ttu-id="a2666-180">筛选器的 *before* 代码应用于全局</span><span class="sxs-lookup"><span data-stu-id="a2666-180">The *before* code of filters applied globally</span></span>
  * <span data-ttu-id="a2666-181">筛选器的 *before* 代码应用于控制器</span><span class="sxs-lookup"><span data-stu-id="a2666-181">The *before* code of filters applied to controllers</span></span>
    * <span data-ttu-id="a2666-182">筛选器的 *before* 代码应用于操作方法</span><span class="sxs-lookup"><span data-stu-id="a2666-182">The *before* code of filters applied to action methods</span></span>
    * <span data-ttu-id="a2666-183">筛选器的 *after* 代码应用于操作方法</span><span class="sxs-lookup"><span data-stu-id="a2666-183">The *after* code of filters applied to action methods</span></span>
  * <span data-ttu-id="a2666-184">筛选器的 *after* 代码应用于控制器</span><span class="sxs-lookup"><span data-stu-id="a2666-184">The *after* code of filters applied to controllers</span></span>
* <span data-ttu-id="a2666-185">筛选器的 *after* 代码应用于全局</span><span class="sxs-lookup"><span data-stu-id="a2666-185">The *after* code of filters applied globally</span></span>
  
<span data-ttu-id="a2666-186">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="a2666-186">Here's an example that illustrates the order in which filter methods are called for synchronous Action filters.</span></span>

| <span data-ttu-id="a2666-187">序列</span><span class="sxs-lookup"><span data-stu-id="a2666-187">Sequence</span></span> | <span data-ttu-id="a2666-188">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="a2666-188">Filter scope</span></span> | <span data-ttu-id="a2666-189">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="a2666-189">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="a2666-190">1</span><span class="sxs-lookup"><span data-stu-id="a2666-190">1</span></span> | <span data-ttu-id="a2666-191">Global</span><span class="sxs-lookup"><span data-stu-id="a2666-191">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a2666-192">2</span><span class="sxs-lookup"><span data-stu-id="a2666-192">2</span></span> | <span data-ttu-id="a2666-193">控制器</span><span class="sxs-lookup"><span data-stu-id="a2666-193">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a2666-194">3</span><span class="sxs-lookup"><span data-stu-id="a2666-194">3</span></span> | <span data-ttu-id="a2666-195">方法</span><span class="sxs-lookup"><span data-stu-id="a2666-195">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a2666-196">4</span><span class="sxs-lookup"><span data-stu-id="a2666-196">4</span></span> | <span data-ttu-id="a2666-197">方法</span><span class="sxs-lookup"><span data-stu-id="a2666-197">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a2666-198">5</span><span class="sxs-lookup"><span data-stu-id="a2666-198">5</span></span> | <span data-ttu-id="a2666-199">控制器</span><span class="sxs-lookup"><span data-stu-id="a2666-199">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="a2666-200">6</span><span class="sxs-lookup"><span data-stu-id="a2666-200">6</span></span> | <span data-ttu-id="a2666-201">Global</span><span class="sxs-lookup"><span data-stu-id="a2666-201">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="a2666-202">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="a2666-202">This sequence shows:</span></span>

* <span data-ttu-id="a2666-203">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="a2666-203">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="a2666-204">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="a2666-204">The controller filter is nested within the global filter.</span></span> 

<span data-ttu-id="a2666-205">换句话说，如果处于异步筛选器的 On*Stage*ExecutionAsync 方法内，则当代码位于堆栈上时，所有筛选器都在更严格的作用域中运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-205">To put it another way, if you're inside an async filter's On*Stage*ExecutionAsync method, all of the filters with a tighter scope run while your code is on the stack.</span></span>

> [!NOTE]
> <span data-ttu-id="a2666-206">继承自 `Controller` 基类的每个控制器都包括 `OnActionExecuting` 和 `OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-206">Every controller that inherits from the `Controller` base class includes `OnActionExecuting` and `OnActionExecuted` methods.</span></span> <span data-ttu-id="a2666-207">这些方法包装针对某项给定操作运行的筛选器：`OnActionExecuting` 在所有筛选器之前调用，`OnActionExecuted` 在所有筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="a2666-207">These methods wrap the filters that run for a given action:  `OnActionExecuting` is called before any of the filters, and `OnActionExecuted` is called after all of the filters.</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="a2666-208">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="a2666-208">Overriding the default order</span></span>

<span data-ttu-id="a2666-209">可以通过实现 `IOrderedFilter` 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="a2666-209">You can override the default sequence of execution by implementing `IOrderedFilter`.</span></span> <span data-ttu-id="a2666-210">此接口公开了一个 `Order` 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="a2666-210">This interface exposes an `Order` property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="a2666-211">具有较低 `Order` 值的筛选器会在具有较高 `Order` 值的筛选器之前执行其 *before* 代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-211">A filter with a lower `Order` value will have its *before* code executed before that of a filter with a higher value of `Order`.</span></span> <span data-ttu-id="a2666-212">具有较低 `Order` 值的筛选器会在具有较高 `Order` 值的筛选器之后执行其 *after* 代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-212">A filter with a lower `Order` value will have its *after* code executed after that of a filter with a higher `Order` value.</span></span> <span data-ttu-id="a2666-213">可使用构造函数参数来设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="a2666-213">You can set the `Order` property by using a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="a2666-214">如果具有上述示例中所示的 3 个相同的操作筛选器，但将控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="a2666-214">If you have the same 3 Action filters shown in the preceding example but set the `Order` property of the controller and global filters to 1 and 2 respectively, the order of execution would be reversed.</span></span>

| <span data-ttu-id="a2666-215">序列</span><span class="sxs-lookup"><span data-stu-id="a2666-215">Sequence</span></span> | <span data-ttu-id="a2666-216">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="a2666-216">Filter scope</span></span> | <span data-ttu-id="a2666-217">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="a2666-217">`Order` property</span></span> | <span data-ttu-id="a2666-218">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="a2666-218">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="a2666-219">1</span><span class="sxs-lookup"><span data-stu-id="a2666-219">1</span></span> | <span data-ttu-id="a2666-220">方法</span><span class="sxs-lookup"><span data-stu-id="a2666-220">Method</span></span> | <span data-ttu-id="a2666-221">0</span><span class="sxs-lookup"><span data-stu-id="a2666-221">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="a2666-222">2</span><span class="sxs-lookup"><span data-stu-id="a2666-222">2</span></span> | <span data-ttu-id="a2666-223">控制器</span><span class="sxs-lookup"><span data-stu-id="a2666-223">Controller</span></span> | <span data-ttu-id="a2666-224">1</span><span class="sxs-lookup"><span data-stu-id="a2666-224">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a2666-225">3</span><span class="sxs-lookup"><span data-stu-id="a2666-225">3</span></span> | <span data-ttu-id="a2666-226">Global</span><span class="sxs-lookup"><span data-stu-id="a2666-226">Global</span></span> | <span data-ttu-id="a2666-227">2</span><span class="sxs-lookup"><span data-stu-id="a2666-227">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="a2666-228">4</span><span class="sxs-lookup"><span data-stu-id="a2666-228">4</span></span> | <span data-ttu-id="a2666-229">Global</span><span class="sxs-lookup"><span data-stu-id="a2666-229">Global</span></span> | <span data-ttu-id="a2666-230">2</span><span class="sxs-lookup"><span data-stu-id="a2666-230">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a2666-231">5</span><span class="sxs-lookup"><span data-stu-id="a2666-231">5</span></span> | <span data-ttu-id="a2666-232">控制器</span><span class="sxs-lookup"><span data-stu-id="a2666-232">Controller</span></span> | <span data-ttu-id="a2666-233">1</span><span class="sxs-lookup"><span data-stu-id="a2666-233">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="a2666-234">6</span><span class="sxs-lookup"><span data-stu-id="a2666-234">6</span></span> | <span data-ttu-id="a2666-235">方法</span><span class="sxs-lookup"><span data-stu-id="a2666-235">Method</span></span> | <span data-ttu-id="a2666-236">0</span><span class="sxs-lookup"><span data-stu-id="a2666-236">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="a2666-237">在确定筛选器的运行顺序时，`Order` 属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="a2666-237">The `Order` property trumps scope when determining the order in which filters will run.</span></span> <span data-ttu-id="a2666-238">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="a2666-238">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="a2666-239">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="a2666-239">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="a2666-240">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="a2666-240">For built-in filters, scope determines order unless you set `Order` to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="a2666-241">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="a2666-241">Cancellation and short circuiting</span></span>

<span data-ttu-id="a2666-242">通过设置提供给筛选器方法的 `context` 参数上的 `Result` 属性，可以在筛选器管道的任意位置设置短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-242">You can short-circuit the filter pipeline at any point by setting the `Result` property on the `context` parameter provided to the filter method.</span></span> <span data-ttu-id="a2666-243">例如，以下资源筛选器将阻止执行管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="a2666-243">For instance, the following Resource filter prevents the rest of the pipeline from executing.</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

<span data-ttu-id="a2666-244">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="a2666-244">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="a2666-245">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="a2666-245">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="a2666-246">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-246">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="a2666-247">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="a2666-247">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="a2666-248">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-248">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="a2666-249">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="a2666-249">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="a2666-250">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="a2666-250">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="a2666-251">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="a2666-251">Dependency injection</span></span>

<span data-ttu-id="a2666-252">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-252">Filters can be added by type or by instance.</span></span> <span data-ttu-id="a2666-253">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="a2666-253">If you add an instance, that instance will be used for every request.</span></span> <span data-ttu-id="a2666-254">如果添加类型，则将激活该类型，这意味着将为每个请求创建一个实例，并且[依赖关系注入](../../fundamentals/dependency-injection.md) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="a2666-254">If you add a type, it will be type-activated, meaning an instance will be created for each request and any constructor dependencies will be populated by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="a2666-255">按类型添加筛选器等效于 `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`。</span><span class="sxs-lookup"><span data-stu-id="a2666-255">Adding a filter by type is equivalent to `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.</span></span>

<span data-ttu-id="a2666-256">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](../../fundamentals/dependency-injection.md) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="a2666-256">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="a2666-257">这是因为属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="a2666-257">This is because attributes must have their constructor parameters supplied where they're applied.</span></span> <span data-ttu-id="a2666-258">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="a2666-258">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="a2666-259">如果筛选器具有一些需要从 DI 访问的依赖项，有几种受支持的方法可用。</span><span class="sxs-lookup"><span data-stu-id="a2666-259">If your filters have dependencies that you need to access from DI, there are several supported approaches.</span></span> <span data-ttu-id="a2666-260">可以使用以下接口之一，将筛选器应用于类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="a2666-260">You can apply your filter to a class or action method using one of the following:</span></span>

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* <span data-ttu-id="a2666-261">在属性上实现的 `IFilterFactory`</span><span class="sxs-lookup"><span data-stu-id="a2666-261">`IFilterFactory` implemented on your attribute</span></span>

> [!NOTE]
> <span data-ttu-id="a2666-262">记录器就是一种可能需要从 DI 获取的依赖项。</span><span class="sxs-lookup"><span data-stu-id="a2666-262">One dependency you might want to get from DI is a logger.</span></span> <span data-ttu-id="a2666-263">但是，应避免单纯为进行日志记录而创建和使用筛选器，因为[内置的框架日志记录功能](xref:fundamentals/logging/index)可能已经提供用户所需。</span><span class="sxs-lookup"><span data-stu-id="a2666-263">However, avoid creating and using filters purely for logging purposes, since the [built-in framework logging features](xref:fundamentals/logging/index) may already provide what you need.</span></span> <span data-ttu-id="a2666-264">如果要将日志记录功能添加到筛选器，它应重点关注业务领域问题或特定于筛选器的行为，而非 MVC 操作或其他框架事件。</span><span class="sxs-lookup"><span data-stu-id="a2666-264">If you're going to add logging to your filters, it should focus on business domain concerns or behavior specific to your filter, rather than MVC actions or other framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="a2666-265">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a2666-265">ServiceFilterAttribute</span></span>

<span data-ttu-id="a2666-266">在 DI 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="a2666-266">Service filter implementation types are registered in DI.</span></span> <span data-ttu-id="a2666-267">`ServiceFilterAttribute` 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="a2666-267">A `ServiceFilterAttribute` retrieves an instance of the filter from DI.</span></span> <span data-ttu-id="a2666-268">将 `ServiceFilterAttribute` 添加到 `Startup.ConfigureServices` 中的容器中，并在 `[ServiceFilter]` 属性中引用它：</span><span class="sxs-lookup"><span data-stu-id="a2666-268">Add the `ServiceFilterAttribute` to the container in `Startup.ConfigureServices`, and reference it in a `[ServiceFilter]` attribute:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="a2666-269">使用 `ServiceFilterAttribute` 时，`IsReusable` 设置会提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="a2666-269">When using `ServiceFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a2666-270">该框架不保证在稍后的某个时刻将创建筛选器的单个实例，或不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-270">The framework provides no guarantees that a single instance of the filter will be created or the filter will not be re-requested from the DI container at some later point.</span></span> <span data-ttu-id="a2666-271">如果使用的筛选器依赖于具有除单一实例以外的生命周期的服务，请避免使用 `IsReusable`。</span><span class="sxs-lookup"><span data-stu-id="a2666-271">Avoid using `IsReusable` when using a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a2666-272">使用 `ServiceFilterAttribute` 时不注册筛选器类型会引发异常：</span><span class="sxs-lookup"><span data-stu-id="a2666-272">Using `ServiceFilterAttribute` without registering the filter type results in an exception:</span></span>

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

<span data-ttu-id="a2666-273">`ServiceFilterAttribute` 可实现 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="a2666-273">`ServiceFilterAttribute` implements `IFilterFactory`.</span></span> <span data-ttu-id="a2666-274">`IFilterFactory` 公开用于创建 `IFilterMetadata` 实例的 `CreateInstance` 方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-274">`IFilterFactory` exposes the `CreateInstance` method for creating an `IFilterMetadata` instance.</span></span> <span data-ttu-id="a2666-275">`CreateInstance` 方法从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="a2666-275">The `CreateInstance` method loads the specified type from the services container (DI).</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="a2666-276">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="a2666-276">TypeFilterAttribute</span></span>

<span data-ttu-id="a2666-277">`TypeFilterAttribute` 与 `ServiceFilterAttribute` 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="a2666-277">`TypeFilterAttribute` is similar to `ServiceFilterAttribute`, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="a2666-278">它使用 `Microsoft.Extensions.DependencyInjection.ObjectFactory` 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="a2666-278">It instantiates the type by using `Microsoft.Extensions.DependencyInjection.ObjectFactory`.</span></span>

<span data-ttu-id="a2666-279">由于存在这种差异，所以存在以下情况：</span><span class="sxs-lookup"><span data-stu-id="a2666-279">Because of this difference:</span></span>

* <span data-ttu-id="a2666-280">使用 `TypeFilterAttribute` 引用的类型不需要先注册在容器中。</span><span class="sxs-lookup"><span data-stu-id="a2666-280">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the container first.</span></span>  <span data-ttu-id="a2666-281">它们具备由容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="a2666-281">They do have their dependencies fulfilled by the container.</span></span> 
* <span data-ttu-id="a2666-282">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="a2666-282">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="a2666-283">使用 `TypeFilterAttribute` 时，`IsReusable` 设置会提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="a2666-283">When using `TypeFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="a2666-284">该框架不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="a2666-284">The framework provides no guarantees that a single instance of the filter will be created.</span></span> <span data-ttu-id="a2666-285">如果使用的筛选器依赖于具有除单一实例以外的生命周期的服务，请避免使用 `IsReusable`。</span><span class="sxs-lookup"><span data-stu-id="a2666-285">Avoid using `IsReusable` when using a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="a2666-286">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="a2666-286">The following example demonstrates how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

### <a name="ifilterfactory-implemented-on-your-attribute"></a><span data-ttu-id="a2666-287">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="a2666-287">IFilterFactory implemented on your attribute</span></span>

<span data-ttu-id="a2666-288">如果你的筛选器符合以下描述：</span><span class="sxs-lookup"><span data-stu-id="a2666-288">If you have a filter that:</span></span>

* <span data-ttu-id="a2666-289">不需要任何参数。</span><span class="sxs-lookup"><span data-stu-id="a2666-289">Doesn't require any arguments.</span></span>
* <span data-ttu-id="a2666-290">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="a2666-290">Has constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="a2666-291">在类和方法上可以不使用 `[TypeFilter(typeof(FilterType))]`改用自己命名的属性。</span><span class="sxs-lookup"><span data-stu-id="a2666-291">You can use your own named attribute on classes and methods instead of `[TypeFilter(typeof(FilterType))]`).</span></span> <span data-ttu-id="a2666-292">下面的筛选器展示了如何实现此操作：</span><span class="sxs-lookup"><span data-stu-id="a2666-292">The following filter shows how this can be implemented:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="a2666-293">可以使用 `[SampleActionFilter]` 语法将此筛选器应用于类或方法，而不必使用 `[TypeFilter]` 或 `[ServiceFilter]`。</span><span class="sxs-lookup"><span data-stu-id="a2666-293">This filter can be applied to classes or methods using the `[SampleActionFilter]` syntax, instead of having to use `[TypeFilter]` or `[ServiceFilter]`.</span></span>

## <a name="authorization-filters"></a><span data-ttu-id="a2666-294">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="a2666-294">Authorization filters</span></span>

<span data-ttu-id="a2666-295">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="a2666-295">*Authorization filters*:</span></span>

* <span data-ttu-id="a2666-296">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="a2666-296">Control access to action methods.</span></span>
* <span data-ttu-id="a2666-297">是筛选器管道中要执行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-297">Are the first filters to be executed within the filter pipeline.</span></span> 
* <span data-ttu-id="a2666-298">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-298">Have a before method, but no after method.</span></span> 

<span data-ttu-id="a2666-299">用户只有在编写自己的授权框架时，才应编写自定义授权筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-299">You should only write a custom authorization filter if you are writing your own authorization framework.</span></span> <span data-ttu-id="a2666-300">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-300">Prefer configuring your authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="a2666-301">内置筛选器实现只负责调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="a2666-301">The built-in filter implementation is just responsible for calling the authorization system.</span></span>

<span data-ttu-id="a2666-302">切勿在授权筛选器内引发异常，因为没有任何能处理该异常的组件（异常筛选器不会进行处理）。</span><span class="sxs-lookup"><span data-stu-id="a2666-302">You shouldn't throw exceptions within authorization filters, since nothing will handle the exception (exception filters won't handle them).</span></span> <span data-ttu-id="a2666-303">在出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="a2666-303">Consider issuing a challenge when an exception occurs.</span></span>

<span data-ttu-id="a2666-304">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="a2666-304">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="a2666-305">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="a2666-305">Resource filters</span></span>

* <span data-ttu-id="a2666-306">实现 `IResourceFilter` 或 `IAsyncResourceFilter` 接口，</span><span class="sxs-lookup"><span data-stu-id="a2666-306">Implement either the `IResourceFilter` or `IAsyncResourceFilter` interface,</span></span>
* <span data-ttu-id="a2666-307">它们的执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="a2666-307">Their execution wraps most of the filter pipeline.</span></span> 
* <span data-ttu-id="a2666-308">只有[授权筛选器](#authorization-filters)在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="a2666-308">Only [Authorization filters](#authorization-filters) run before Resource filters.</span></span>

<span data-ttu-id="a2666-309">如果需要使某个请求正在执行的大部分工作短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="a2666-309">Resource filters are useful to short-circuit most of the work a request is doing.</span></span> <span data-ttu-id="a2666-310">例如，如果响应在缓存中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="a2666-310">For example, a caching filter can avoid the rest of the pipeline if the response is in the cache.</span></span>

<span data-ttu-id="a2666-311">前面所示的[短路资源筛选器](#short-circuiting-resource-filter)便是一种资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-311">The [short circuiting resource filter](#short-circuiting-resource-filter) shown earlier is one example of a resource filter.</span></span> <span data-ttu-id="a2666-312">另一个示例是 [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="a2666-312">Another example is [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

* <span data-ttu-id="a2666-313">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="a2666-313">It prevents model binding from accessing the form data.</span></span> 
* <span data-ttu-id="a2666-314">如果要上传大型文件，同时想防止表单被读入内存，那么此筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="a2666-314">It's useful for large file uploads and want to prevent the form from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="a2666-315">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="a2666-315">Action filters</span></span>

> [!IMPORTANT]
> <span data-ttu-id="a2666-316">操作筛选器不应用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="a2666-316">Action filters do **not** apply to Razor Pages.</span></span> <span data-ttu-id="a2666-317">Razor Pages 支持 <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter>。</span><span class="sxs-lookup"><span data-stu-id="a2666-317">Razor Pages supports <xref:Microsoft.AspNetCore.Mvc.Filters.IPageFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncPageFilter> .</span></span> <span data-ttu-id="a2666-318">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="a2666-318">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

<span data-ttu-id="a2666-319">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="a2666-319">*Action filters*:</span></span>

* <span data-ttu-id="a2666-320">实现 `IActionFilter` 或 `IAsyncActionFilter` 接口。</span><span class="sxs-lookup"><span data-stu-id="a2666-320">Implement either the `IActionFilter` or `IAsyncActionFilter` interface.</span></span>
* <span data-ttu-id="a2666-321">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="a2666-321">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="a2666-322">下面是一个操作筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="a2666-322">Here's a sample action filter:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="a2666-323"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="a2666-323">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutingContext> provides the following properties:</span></span>

* <span data-ttu-id="a2666-324">`ActionArguments`：用于处理对操作的输入。</span><span class="sxs-lookup"><span data-stu-id="a2666-324">`ActionArguments` - lets you manipulate the inputs to the action.</span></span>
* <span data-ttu-id="a2666-325">`Controller`：用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="a2666-325">`Controller` - lets you manipulate the controller instance.</span></span> 
* <span data-ttu-id="a2666-326">`Result`：设置此属性会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-326">`Result` - setting this short-circuits execution of the action method and subsequent action filters.</span></span> <span data-ttu-id="a2666-327">引发异常也会阻止操作方法和后续筛选器的执行，但会被视为失败，而不是一个成功的结果。</span><span class="sxs-lookup"><span data-stu-id="a2666-327">Throwing an exception also prevents execution of the action method and subsequent filters, but is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a2666-328"><xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> 提供 `Controller` 和 `Result` 以及以下属性：</span><span class="sxs-lookup"><span data-stu-id="a2666-328">The <xref:Microsoft.AspNetCore.Mvc.Filters.ActionExecutedContext> provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="a2666-329">`Canceled`：如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="a2666-329">`Canceled` - will be true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="a2666-330">`Exception`：如果操作或后续操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="a2666-330">`Exception` - will be non-null if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a2666-331">将此属性设置为 NULL 可有效地“处理”异常，并且将执行 `Result`，就像它是从操作方法正常返回的一样。</span><span class="sxs-lookup"><span data-stu-id="a2666-331">Setting this property to null effectively 'handles' an exception, and `Result` will be executed as if it were returned from the action method normally.</span></span>

<span data-ttu-id="a2666-332">对于 `IAsyncActionFilter`，一个向 `ActionExecutionDelegate` 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="a2666-332">For an `IAsyncActionFilter`, a call to the `ActionExecutionDelegate`:</span></span>

* <span data-ttu-id="a2666-333">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-333">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="a2666-334">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="a2666-334">returns `ActionExecutedContext`.</span></span> 

<span data-ttu-id="a2666-335">若要设置短路，可将 `ActionExecutingContext.Result` 分配到某个结果实例，并且不调用 `ActionExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="a2666-335">To short-circuit, assign `ActionExecutingContext.Result` to some result instance and don't call the `ActionExecutionDelegate`.</span></span>

<span data-ttu-id="a2666-336">该框架提供一个可子类化的抽象 `ActionFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="a2666-336">The framework provides an abstract `ActionFilterAttribute` that you can subclass.</span></span> 

<span data-ttu-id="a2666-337">操作筛选器可用于验证模型状态，并在状态为无效时返回任何错误：</span><span class="sxs-lookup"><span data-stu-id="a2666-337">You can use an action filter to validate model state and return any errors if the state is invalid:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

<span data-ttu-id="a2666-338">`OnActionExecuted` 方法在操作方法之后运行，可通过 `ActionExecutedContext.Result` 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="a2666-338">The `OnActionExecuted` method runs after the action method and can see and manipulate the results of the action through the `ActionExecutedContext.Result` property.</span></span> <span data-ttu-id="a2666-339">如果操作执行已被另一个筛选器设置短路，则 `ActionExecutedContext.Canceled` 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="a2666-339">`ActionExecutedContext.Canceled` will be set to true if the action execution was short-circuited by another filter.</span></span> <span data-ttu-id="a2666-340">如果操作或后续操作筛选器引发了异常，则 `ActionExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="a2666-340">`ActionExecutedContext.Exception` will be set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="a2666-341">将 `ActionExecutedContext.Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="a2666-341">Setting `ActionExecutedContext.Exception` to null:</span></span>

* <span data-ttu-id="a2666-342">有效地“处理”异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-342">Effectively 'handles' an exception.</span></span>
* <span data-ttu-id="a2666-343">执行 `ActionExecutedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="a2666-343">`ActionExecutedContext.Result` is executed as if it were returned normally from the action method.</span></span>

## <a name="exception-filters"></a><span data-ttu-id="a2666-344">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="a2666-344">Exception filters</span></span>

<span data-ttu-id="a2666-345">*异常筛选器*可实现 `IExceptionFilter` 或 `IAsyncExceptionFilter` 接口。</span><span class="sxs-lookup"><span data-stu-id="a2666-345">*Exception filters* implement either the `IExceptionFilter` or `IAsyncExceptionFilter` interface.</span></span> <span data-ttu-id="a2666-346">它们可用于为应用实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="a2666-346">They can be used to implement common error handling policies for an app.</span></span> 

<span data-ttu-id="a2666-347">下面的异常筛选器示例使用自定义开发人员错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="a2666-347">The following sample exception filter uses a custom developer error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

<span data-ttu-id="a2666-348">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="a2666-348">Exception filters:</span></span>

* <span data-ttu-id="a2666-349">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="a2666-349">Don't have before and after events.</span></span> 
* <span data-ttu-id="a2666-350">实现 `OnException` 或 `OnExceptionAsync`。</span><span class="sxs-lookup"><span data-stu-id="a2666-350">Implement `OnException` or `OnExceptionAsync`.</span></span> 
* <span data-ttu-id="a2666-351">处理控制器创建、[模型绑定](../models/model-binding.md)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-351">Handle unhandled exceptions that occur in controller creation, [model binding](../models/model-binding.md), action filters, or action methods.</span></span> 
* <span data-ttu-id="a2666-352">请不要捕获资源筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-352">Do not catch exceptions that occur in Resource filters, Result filters, or MVC Result execution.</span></span>

<span data-ttu-id="a2666-353">若要处理异常，请将 `ExceptionContext.ExceptionHandled` 属性设置为 true，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="a2666-353">To handle an exception, set the `ExceptionContext.ExceptionHandled` property to true or write a response.</span></span> <span data-ttu-id="a2666-354">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-354">This stops propagation of the exception.</span></span> <span data-ttu-id="a2666-355">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="a2666-355">An Exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="a2666-356">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="a2666-356">Only an Action filter can do that.</span></span>

> [!NOTE]
> <span data-ttu-id="a2666-357">在 ASP.NET Core 1.1 中，如果将 `ExceptionHandled` 设置为 true 并编写响应，则不会发送响应。</span><span class="sxs-lookup"><span data-stu-id="a2666-357">In ASP.NET Core 1.1, the response isn't sent if you set `ExceptionHandled` to true **and** write a response.</span></span> <span data-ttu-id="a2666-358">在这种情况下，ASP.NET Core 1.0 不发送响应，ASP.NET Core 1.1.2 则恢复为 1.0 的行为。</span><span class="sxs-lookup"><span data-stu-id="a2666-358">In that scenario, ASP.NET Core 1.0 does send the response, and ASP.NET Core 1.1.2 will return to the 1.0 behavior.</span></span> <span data-ttu-id="a2666-359">有关详细信息，请参阅 GitHub 存储库中的[问题编号 5594](https://github.com/aspnet/Mvc/issues/5594)。</span><span class="sxs-lookup"><span data-stu-id="a2666-359">For more information, see [issue #5594](https://github.com/aspnet/Mvc/issues/5594) in the GitHub repository.</span></span> 

<span data-ttu-id="a2666-360">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="a2666-360">Exception filters:</span></span>

* <span data-ttu-id="a2666-361">非常适合捕获发生在 MVC 操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-361">Are good for trapping exceptions that occur within MVC actions.</span></span>
* <span data-ttu-id="a2666-362">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="a2666-362">Are not as flexible as error handling middleware.</span></span> 

<span data-ttu-id="a2666-363">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-363">Prefer middleware for exception handling.</span></span> <span data-ttu-id="a2666-364">仅在需要根据所选 MVC 操作以不同方式执行错误处理时，才使用异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-364">Use exception filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span> <span data-ttu-id="a2666-365">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="a2666-365">For example, your app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="a2666-366">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="a2666-366">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

<span data-ttu-id="a2666-367">`ExceptionFilterAttribute` 可以子类化。</span><span class="sxs-lookup"><span data-stu-id="a2666-367">The `ExceptionFilterAttribute` can be subclassed.</span></span> 

## <a name="result-filters"></a><span data-ttu-id="a2666-368">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="a2666-368">Result filters</span></span>

* <span data-ttu-id="a2666-369">实现接口：</span><span class="sxs-lookup"><span data-stu-id="a2666-369">Implement an interface:</span></span>
  * <span data-ttu-id="a2666-370">`IResultFilter` 或 `IAsyncResultFilter`。</span><span class="sxs-lookup"><span data-stu-id="a2666-370">`IResultFilter` or `IAsyncResultFilter`.</span></span>
  * <span data-ttu-id="a2666-371">`IAlwaysRunResultFilter` 或 `IAsyncAlwaysRunResultFilter`</span><span class="sxs-lookup"><span data-stu-id="a2666-371">`IAlwaysRunResultFilter` or `IAsyncAlwaysRunResultFilter`</span></span>
* <span data-ttu-id="a2666-372">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="a2666-372">Their execution surrounds the execution of action results.</span></span> 

### <a name="iresultfilter-and-iasyncresultfilter"></a><span data-ttu-id="a2666-373">IResultFilter 和 IAsyncResultFilter</span><span class="sxs-lookup"><span data-stu-id="a2666-373">IResultFilter and IAsyncResultFilter</span></span>

<span data-ttu-id="a2666-374">下面是一个添加 HTTP 标头的结果筛选器示例。</span><span class="sxs-lookup"><span data-stu-id="a2666-374">Here's an example of a Result filter that adds an HTTP header.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="a2666-375">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="a2666-375">The kind of result being executed depends on the action in question.</span></span> <span data-ttu-id="a2666-376">返回视图的 MVC 操作会将所有 Razor 处理作为要执行的 `ViewResult` 的一部分。</span><span class="sxs-lookup"><span data-stu-id="a2666-376">An MVC action returning a view would include all razor processing as part of the `ViewResult` being executed.</span></span> <span data-ttu-id="a2666-377">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="a2666-377">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="a2666-378">详细了解[操作结果](actions.md)</span><span class="sxs-lookup"><span data-stu-id="a2666-378">Learn more about [action results](actions.md)</span></span>

<span data-ttu-id="a2666-379">当操作或操作筛选器生成操作结果时，仅针对成功的结果执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-379">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="a2666-380">当异常筛选器处理异常时，不执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="a2666-380">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="a2666-381">`OnResultExecuting` 方法可以将 `ResultExecutingContext.Cancel` 设置为 true，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-381">The `OnResultExecuting` method can short-circuit execution of the action result and subsequent result filters by setting `ResultExecutingContext.Cancel` to true.</span></span> <span data-ttu-id="a2666-382">设置短路时，通常应写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="a2666-382">You should generally write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="a2666-383">如果引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="a2666-383">Throwing an exception will:</span></span>

* <span data-ttu-id="a2666-384">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="a2666-384">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="a2666-385">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="a2666-385">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="a2666-386">当 `OnResultExecuted` 方法运行时，响应可能已发送到客户端，而且不能再更改（除非引发了异常）。</span><span class="sxs-lookup"><span data-stu-id="a2666-386">When the `OnResultExecuted` method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).</span></span> <span data-ttu-id="a2666-387">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="a2666-387">`ResultExecutedContext.Canceled` will be set to true if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="a2666-388">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="a2666-388">`ResultExecutedContext.Exception` will be set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="a2666-389">将 `Exception` 设置为 NULL 可有效地“处理”异常，并防止 MVC 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="a2666-389">Setting `Exception` to null effectively 'handles' an exception and prevents the exception from being rethrown by MVC later in the pipeline.</span></span> <span data-ttu-id="a2666-390">在处理结果筛选器中的异常时，可能无法向响应写入任何数据。</span><span class="sxs-lookup"><span data-stu-id="a2666-390">When you're handling an exception in a result filter, you might not be able to write any data to the response.</span></span> <span data-ttu-id="a2666-391">如果操作结果在其执行过程中引发异常，并且标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="a2666-391">If the action result throws partway through its execution, and the headers have already been flushed to the client, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="a2666-392">对于 `IAsyncResultFilter`，通过调用 `ResultExecutionDelegate` 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="a2666-392">For an `IAsyncResultFilter` a call to `await next` on the `ResultExecutionDelegate` executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="a2666-393">若要设置短路，可将 `ResultExecutingContext.Cancel` 设置为 true，并且不调用 `ResultExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="a2666-393">To short-circuit, set `ResultExecutingContext.Cancel` to true and don't call the `ResultExecutionDelegate`.</span></span>

<span data-ttu-id="a2666-394">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="a2666-394">The framework provides an abstract `ResultFilterAttribute` that you can subclass.</span></span> <span data-ttu-id="a2666-395">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="a2666-395">The [AddHeaderAttribute](#add-header-attribute) class shown earlier is an example of a result filter attribute.</span></span>

### <a name="ialwaysrunresultfilter-and-iasyncalwaysrunresultfilter"></a><span data-ttu-id="a2666-396">IAlwaysRunResultFilter 和 IAsyncAlwaysRunResultFilter</span><span class="sxs-lookup"><span data-stu-id="a2666-396">IAlwaysRunResultFilter and IAsyncAlwaysRunResultFilter</span></span>

<span data-ttu-id="a2666-397"><xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> 和 <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> 接口声明了一个针对操作结果运行的 <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> 实现。</span><span class="sxs-lookup"><span data-stu-id="a2666-397">The <xref:Microsoft.AspNetCore.Mvc.Filters.IAlwaysRunResultFilter> and <xref:Microsoft.AspNetCore.Mvc.Filters.IAsyncAlwaysRunResultFilter> interfaces declare an <xref:Microsoft.AspNetCore.Mvc.Filters.IResultFilter> implementation that runs for action results.</span></span> <span data-ttu-id="a2666-398">除非应用 <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> 或 <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> 并使响应短路，否则会将筛选器应用于操作结果。</span><span class="sxs-lookup"><span data-stu-id="a2666-398">The filter is applied to an action result unless an <xref:Microsoft.AspNetCore.Mvc.Filters.IExceptionFilter> or <xref:Microsoft.AspNetCore.Mvc.Filters.IAuthorizationFilter> applies and short-circuits the response.</span></span>

<span data-ttu-id="a2666-399">换句话说，这些“始终运行”的筛选器始终运行，除非异常或授权筛选器使它们短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-399">In other words, these "always run" filters, always run, except when an exception or authorization filter short-circuits them.</span></span> <span data-ttu-id="a2666-400">除 `IExceptionFilter` 和 `IAuthorizationFilter` 之外的筛选器不会使它们短路。</span><span class="sxs-lookup"><span data-stu-id="a2666-400">Filters other than `IExceptionFilter` and `IAuthorizationFilter` don't short circuit them.</span></span>

<span data-ttu-id="a2666-401">例如，以下筛选器始终运行并在内容协商失败时设置具有“422 无法处理的实体”状态代码的操作结果 (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>)：</span><span class="sxs-lookup"><span data-stu-id="a2666-401">For example, the following filter always runs and sets an action result (<xref:Microsoft.AspNetCore.Mvc.ObjectResult>) with a *422 Unprocessable Entity* status code when content negotiation fails:</span></span>

```csharp
public class UnprocessableResultFilter : Attribute, IAlwaysRunResultFilter
{
    public void OnResultExecuting(ResultExecutingContext context)
    {
        if (context.Result is StatusCodeResult statusCodeResult &&
            statusCodeResult.StatusCode == 415)
        {
            context.Result = new ObjectResult("Can't process this!")
            {
                StatusCode = 422,
            };
        }
    }

    public void OnResultExecuted(ResultExecutedContext context)
    {
    }
}
```

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="a2666-402">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="a2666-402">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="a2666-403">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="a2666-403">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="a2666-404">但筛选器又不同于中间件，它们是 MVC 的一部分，这意味着它们有权访问 MVC 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="a2666-404">But filters differ from middleware in that they're part of MVC, which means that they have access to MVC context and constructs.</span></span>

<span data-ttu-id="a2666-405">在 ASP.NET Core 1.1 中，可以在筛选器管道中使用中间件。</span><span class="sxs-lookup"><span data-stu-id="a2666-405">In ASP.NET Core 1.1, you can use middleware in the filter pipeline.</span></span> <span data-ttu-id="a2666-406">如果有一个中间件组件，该组件需要访问 MVC 路由数据，或者只能针对特定控制器或操作运行，则可能需要这样做。</span><span class="sxs-lookup"><span data-stu-id="a2666-406">You might want to do that if you have a middleware component that needs access to MVC route data, or one that should run only for certain controllers or actions.</span></span>

<span data-ttu-id="a2666-407">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="a2666-407">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware that you want to inject into the filter pipeline.</span></span> <span data-ttu-id="a2666-408">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="a2666-408">Here's an example that uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="a2666-409">然后，可以使用 `MiddlewareFilterAttribute` 为所选控制器或操作或者在全局范围内运行中间件：</span><span class="sxs-lookup"><span data-stu-id="a2666-409">You can then use the `MiddlewareFilterAttribute` to run the middleware for a selected controller or action or globally:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="a2666-410">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="a2666-410">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="a2666-411">后续操作</span><span class="sxs-lookup"><span data-stu-id="a2666-411">Next actions</span></span>

* <span data-ttu-id="a2666-412">请参阅 [Razor Pages 的筛选器方法](xref:razor-pages/filter)</span><span class="sxs-lookup"><span data-stu-id="a2666-412">See [Filter methods for Razor Pages](xref:razor-pages/filter)</span></span>
* <span data-ttu-id="a2666-413">若要尝试使用筛选器，请[下载、测试并修改 Github 示例](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="a2666-413">To experiment with filters, [download, test and modify the Github sample](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
