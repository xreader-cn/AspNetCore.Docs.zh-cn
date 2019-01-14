---
title: ASP.NET Core 中的筛选器
author: ardalis
description: 了解筛选器的工作原理以及如何在 ASP.NET Core MVC 中使用它们。
ms.author: riande
ms.custom: mvc
ms.date: 10/15/2018
uid: mvc/controllers/filters
ms.openlocfilehash: d4fe49a9225b9980a956ef9c773ad631beb557ae
ms.sourcegitcommit: cec77d5ad8a0cedb1ecbec32834111492afd0cd2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/10/2019
ms.locfileid: "54207455"
---
# <a name="filters-in-aspnet-core"></a><span data-ttu-id="98e2b-103">ASP.NET Core 中的筛选器</span><span class="sxs-lookup"><span data-stu-id="98e2b-103">Filters in ASP.NET Core</span></span>

<span data-ttu-id="98e2b-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)、[Tom Dykstra](https://github.com/tdykstra/) 和 [Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="98e2b-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Tom Dykstra](https://github.com/tdykstra/), and [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="98e2b-105">ASP.NET Core MVC 中的筛选器允许在请求处理管道中的特定阶段之前或之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-105">*Filters* in ASP.NET Core MVC allow you to run code before or after specific stages in the request processing pipeline.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="98e2b-106">本主题不适用于 Razor 页面。</span><span class="sxs-lookup"><span data-stu-id="98e2b-106">This topic does **not** apply to Razor Pages.</span></span> <span data-ttu-id="98e2b-107">ASP.NET Core 2.1 及更高版本支持适用于 Razor 页面的 [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) 和 [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-107">ASP.NET Core 2.1 and later supports [IPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.ipagefilter?view=aspnetcore-2.0) and [IAsyncPageFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncpagefilter?view=aspnetcore-2.0) for Razor Pages.</span></span> <span data-ttu-id="98e2b-108">有关详细信息，请参阅 [Razor 页面的筛选方法](xref:razor-pages/filter)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-108">For more information, see [Filter methods for Razor Pages](xref:razor-pages/filter).</span></span>

 <span data-ttu-id="98e2b-109">内置筛选器处理任务，例如：</span><span class="sxs-lookup"><span data-stu-id="98e2b-109">Built-in filters handle tasks such as:</span></span>

 * <span data-ttu-id="98e2b-110">授权（防止用户访问未获授权的资源）。</span><span class="sxs-lookup"><span data-stu-id="98e2b-110">Authorization (preventing access to resources a user isn't authorized for).</span></span>
 * <span data-ttu-id="98e2b-111">确保所有请求都使用 HTTPS。</span><span class="sxs-lookup"><span data-stu-id="98e2b-111">Ensuring that all requests use HTTPS.</span></span>
 * <span data-ttu-id="98e2b-112">响应缓存（对请求管道进行短路出路，以便返回缓存的响应）。</span><span class="sxs-lookup"><span data-stu-id="98e2b-112">Response caching (short-circuiting the request pipeline to return a cached response).</span></span> 

<span data-ttu-id="98e2b-113">可以创建自定义筛选器来处理横切关注点。</span><span class="sxs-lookup"><span data-stu-id="98e2b-113">Custom filters can be created to handle cross-cutting concerns.</span></span> <span data-ttu-id="98e2b-114">筛选器可以避免跨操作复制代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-114">Filters can avoid duplicating code across actions.</span></span> <span data-ttu-id="98e2b-115">例如，错误处理异常筛选器可以合并错误处理。</span><span class="sxs-lookup"><span data-stu-id="98e2b-115">For example, an error handling exception filter could consolidate error handling.</span></span>

<span data-ttu-id="98e2b-116">[查看或下载 GitHub 中的示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-116">[View or download sample from GitHub](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>

## <a name="how-do-filters-work"></a><span data-ttu-id="98e2b-117">筛选器的工作原理</span><span class="sxs-lookup"><span data-stu-id="98e2b-117">How do filters work?</span></span>

<span data-ttu-id="98e2b-118">筛选器在 *MVC 操作调用管道*（有时称为*筛选器管道*）内运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-118">Filters run within the *MVC action invocation pipeline*, sometimes referred to as the *filter pipeline*.</span></span>  <span data-ttu-id="98e2b-119">筛选器管道在 MVC 选择要执行的操作之后运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-119">The filter pipeline runs after MVC selects the action to execute.</span></span>

![请求通过其他中间件、路由中间件、操作选择和 MVC 操作调用管道进行处理。](filters/_static/filter-pipeline-1.png)

### <a name="filter-types"></a><span data-ttu-id="98e2b-122">筛选器类型</span><span class="sxs-lookup"><span data-stu-id="98e2b-122">Filter types</span></span>

<span data-ttu-id="98e2b-123">每种筛选器类型都在筛选器管道中的不同阶段执行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-123">Each filter type is executed at a different stage in the filter pipeline.</span></span>

* <span data-ttu-id="98e2b-124">[授权筛选器](#authorization-filters)最先运行，用于确定是否已针对当前请求为当前用户授权。</span><span class="sxs-lookup"><span data-stu-id="98e2b-124">[Authorization filters](#authorization-filters) run first and are used to determine whether the current user is authorized for the current request.</span></span> <span data-ttu-id="98e2b-125">如果请求未获授权，它们可以让管道短路。</span><span class="sxs-lookup"><span data-stu-id="98e2b-125">They can short-circuit the pipeline if a request is unauthorized.</span></span> 

* <span data-ttu-id="98e2b-126">[资源筛选器](#resource-filters)是授权后最先处理请求的筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-126">[Resource filters](#resource-filters) are the first to handle a request after authorization.</span></span>  <span data-ttu-id="98e2b-127">它们可以在筛选器管道的其余阶段运行之前以及管道的其余阶段完成之后运行代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-127">They can run code before the rest of the filter pipeline, and after the rest of the pipeline has completed.</span></span> <span data-ttu-id="98e2b-128">出于性能方面的考虑，可以使用它们来实现缓存或以其他方式让筛选器管道短路。</span><span class="sxs-lookup"><span data-stu-id="98e2b-128">They're useful to implement caching or otherwise short-circuit the filter pipeline for performance reasons.</span></span> <span data-ttu-id="98e2b-129">它们在模型绑定之前运行，所以可以影响模型绑定。</span><span class="sxs-lookup"><span data-stu-id="98e2b-129">They run before model binding, so they can influence model binding.</span></span>

* <span data-ttu-id="98e2b-130">[操作筛选器](#action-filters)可以在调用单个操作方法之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-130">[Action filters](#action-filters) can run code immediately before and after an individual action method is called.</span></span> <span data-ttu-id="98e2b-131">它们可用于处理传入某个操作的参数以及从该操作返回的结果。</span><span class="sxs-lookup"><span data-stu-id="98e2b-131">They can be used to manipulate the arguments passed into an action and the result returned from the action.</span></span>

* <span data-ttu-id="98e2b-132">[异常筛选器](#exception-filters)用于在向响应正文写入任何内容之前，对未经处理的异常应用全局策略。</span><span class="sxs-lookup"><span data-stu-id="98e2b-132">[Exception filters](#exception-filters) are used to apply global policies to unhandled exceptions that occur before anything has been written to the response body.</span></span>

* <span data-ttu-id="98e2b-133">[结果筛选器](#result-filters)可以在执行单个操作结果之前和之后立即运行代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-133">[Result filters](#result-filters) can run code immediately before and after the execution of individual action results.</span></span> <span data-ttu-id="98e2b-134">仅当操作方法成功执行时，它们才会运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-134">They run only when the action method has executed successfully.</span></span> <span data-ttu-id="98e2b-135">对于必须围绕视图或格式化程序的执行的逻辑，它们很有用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-135">They are useful for logic that must surround view or formatter execution.</span></span>

<span data-ttu-id="98e2b-136">下图展示了这些筛选器类型在筛选器管道中的交互方式。</span><span class="sxs-lookup"><span data-stu-id="98e2b-136">The following diagram shows how these filter types interact in the filter pipeline.</span></span>

![请求通过授权筛选器、资源筛选器、模型绑定、操作筛选器、操作执行和操作结果转换、异常筛选器、结果筛选器和结果执行进行处理。](filters/_static/filter-pipeline-2.png)

## <a name="implementation"></a><span data-ttu-id="98e2b-139">实现</span><span class="sxs-lookup"><span data-stu-id="98e2b-139">Implementation</span></span>

<span data-ttu-id="98e2b-140">筛选器通过不同的接口定义支持同步和异步实现。</span><span class="sxs-lookup"><span data-stu-id="98e2b-140">Filters support both synchronous and asynchronous implementations through different interface definitions.</span></span> 

<span data-ttu-id="98e2b-141">可在其管道阶段之前和之后运行代码的同步筛选器定义 On*Stage*Executing 方法和 On*Stage*Executed 方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-141">Synchronous filters that can run code both before and after their pipeline stage define On*Stage*Executing and On*Stage*Executed methods.</span></span> <span data-ttu-id="98e2b-142">例如，在调用操作方法之前调用 `OnActionExecuting`，在操作方法返回之后调用 `OnActionExecuted`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-142">For example, `OnActionExecuting` is called before the action method is called, and `OnActionExecuted` is called after the action method returns.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet1)]

<span data-ttu-id="98e2b-143">异步筛选器定义单一的 On*Stage*ExecutionAsync 方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-143">Asynchronous filters define a single On*Stage*ExecutionAsync method.</span></span> <span data-ttu-id="98e2b-144">此方法采用 *FilterType*ExecutionDelegate 委托来执行筛选器的管道阶段。</span><span class="sxs-lookup"><span data-stu-id="98e2b-144">This method takes a *FilterType*ExecutionDelegate delegate which executes the filter's pipeline stage.</span></span> <span data-ttu-id="98e2b-145">例如，`ActionExecutionDelegate` 调用该操作方法或下一个操作筛选器，用户可以在调用它之前和之后执行代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-145">For example, `ActionExecutionDelegate` calls the action method or next action filter, and you can execute code before and after you call it.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleAsyncActionFilter.cs?highlight=6,8-10,13)]

<span data-ttu-id="98e2b-146">可以在单个类中为多个筛选器阶段实现接口。</span><span class="sxs-lookup"><span data-stu-id="98e2b-146">You can implement interfaces for multiple filter stages in a single class.</span></span> <span data-ttu-id="98e2b-147">例如，[ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) 类实现 `IActionFilter` 和 `IResultFilter`，以及它们的异步等效接口。</span><span class="sxs-lookup"><span data-stu-id="98e2b-147">For example, the [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) class implements `IActionFilter`, `IResultFilter`, and their async equivalents.</span></span>

> [!NOTE]
> <span data-ttu-id="98e2b-148">筛选器接口的同步和异步版本**任意**实现一个，而不是同时实现。</span><span class="sxs-lookup"><span data-stu-id="98e2b-148">Implement **either** the synchronous or the async version of a filter interface, not both.</span></span> <span data-ttu-id="98e2b-149">该框架会先查看筛选器是否实现了异步接口，如果是，则调用该接口。</span><span class="sxs-lookup"><span data-stu-id="98e2b-149">The framework checks first to see if the filter implements the async interface, and if so, it calls that.</span></span> <span data-ttu-id="98e2b-150">如果不是，则调用同步接口的方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-150">If not, it calls the synchronous interface's method(s).</span></span> <span data-ttu-id="98e2b-151">如果在一个类中同时实现了这两种接口，则仅调用异步方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-151">If you were to implement both interfaces on one class, only the async method would be called.</span></span> <span data-ttu-id="98e2b-152">使用抽象类时，比如 [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0)，将为每种筛选器类型仅重写同步方法或仅重写异步方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-152">When using abstract classes like [ActionFilterAttribute](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionfilterattribute?view=aspnetcore-2.0) you would override only the synchronous methods or the async method for each filter type.</span></span>

### <a name="ifilterfactory"></a><span data-ttu-id="98e2b-153">IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="98e2b-153">IFilterFactory</span></span>

<span data-ttu-id="98e2b-154">[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) 实现 [IFilterMetadata](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifiltermetadata)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-154">[IFilterFactory](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory) implements [IFilterMetadata](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifiltermetadata).</span></span> <span data-ttu-id="98e2b-155">因此，`IFilterFactory` 实例可在筛选器管道中的任意位置用作 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="98e2b-155">Therefore, an `IFilterFactory` instance can be used as an `IFilterMetadata` instance anywhere in the filter pipeline.</span></span> <span data-ttu-id="98e2b-156">当该框架准备调用筛选器时，它会尝试将其转换为 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-156">When the framework prepares to invoke the filter, it attempts to cast it to an `IFilterFactory`.</span></span> <span data-ttu-id="98e2b-157">如果强制转换成功，则调用 [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) 方法来创建将调用的 `IFilterMetadata` 实例。</span><span class="sxs-lookup"><span data-stu-id="98e2b-157">If that cast succeeds, the [CreateInstance](/dotnet/api/microsoft.aspnetcore.mvc.filters.ifilterfactory.createinstance) method is called to create the `IFilterMetadata` instance that will be invoked.</span></span> <span data-ttu-id="98e2b-158">这提供了一种很灵活的设计，因为无需在应用启动时显式设置精确的筛选器管道。</span><span class="sxs-lookup"><span data-stu-id="98e2b-158">This provides a flexible design, since the precise filter pipeline doesn't need to be set explicitly when the app starts.</span></span>

<span data-ttu-id="98e2b-159">用户可以在自己的属性实现上实现 `IFilterFactory` 作为另一种创建筛选器的方法：</span><span class="sxs-lookup"><span data-stu-id="98e2b-159">You can implement `IFilterFactory` on your own attribute implementations as another approach to creating filters:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderWithFactoryAttribute.cs?name=snippet_IFilterFactory&highlight=1,4,5,6,7)]

### <a name="built-in-filter-attributes"></a><span data-ttu-id="98e2b-160">内置筛选器属性</span><span class="sxs-lookup"><span data-stu-id="98e2b-160">Built-in filter attributes</span></span>

<span data-ttu-id="98e2b-161">该框架包含许多可子类化和自定义的基于属性的内置筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-161">The framework includes built-in attribute-based filters that you can subclass and customize.</span></span> <span data-ttu-id="98e2b-162">例如，以下结果筛选器会向响应添加标头。</span><span class="sxs-lookup"><span data-stu-id="98e2b-162">For example, the following Result filter adds a header to the response.</span></span>

<a name="add-header-attribute"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/AddHeaderAttribute.cs?highlight=5,16)]

<span data-ttu-id="98e2b-163">属性允许筛选器采用参数，如上面的示例所示。</span><span class="sxs-lookup"><span data-stu-id="98e2b-163">Attributes allow filters to accept arguments, as shown in the example above.</span></span> <span data-ttu-id="98e2b-164">可将此属性添加到控制器或操作方法，并指定 HTTP 标头的名称和值：</span><span class="sxs-lookup"><span data-stu-id="98e2b-164">You would add this attribute to a controller or action method and specify the name and value of the HTTP header:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1)]

<span data-ttu-id="98e2b-165">`Index` 操作的结果如下所示：响应标头显示在右下角。</span><span class="sxs-lookup"><span data-stu-id="98e2b-165">The result of the `Index` action is shown below - the response headers are displayed on the bottom right.</span></span>

![显示响应标头（包括 Author Steve Smith @ardalis）的 Microsoft Edge 开发人员工具](filters/_static/add-header.png)

<span data-ttu-id="98e2b-167">多种筛选器接口具有相应属性，这些属性可用作自定义实现的基类。</span><span class="sxs-lookup"><span data-stu-id="98e2b-167">Several of the filter interfaces have corresponding attributes that can be used as base classes for custom implementations.</span></span>

<span data-ttu-id="98e2b-168">筛选器属性：</span><span class="sxs-lookup"><span data-stu-id="98e2b-168">Filter attributes:</span></span>

* `ActionFilterAttribute`
* `ExceptionFilterAttribute`
* `ResultFilterAttribute`
* `FormatFilterAttribute`
* `ServiceFilterAttribute`
* `TypeFilterAttribute`

<span data-ttu-id="98e2b-169">[本文稍后](#dependency-injection)会对 `TypeFilterAttribute` 和 `ServiceFilterAttribute` 进行介绍。</span><span class="sxs-lookup"><span data-stu-id="98e2b-169">`TypeFilterAttribute` and `ServiceFilterAttribute` are explained [later in this article](#dependency-injection).</span></span>

## <a name="filter-scopes-and-order-of-execution"></a><span data-ttu-id="98e2b-170">筛选器作用域和执行顺序</span><span class="sxs-lookup"><span data-stu-id="98e2b-170">Filter scopes and order of execution</span></span>

<span data-ttu-id="98e2b-171">可以将筛选器添加到管道中的三个*作用域*之一。</span><span class="sxs-lookup"><span data-stu-id="98e2b-171">A filter can be added to the pipeline at one of three *scopes*.</span></span> <span data-ttu-id="98e2b-172">可以使用属性将筛选器添加到特定的操作方法或控制器类。</span><span class="sxs-lookup"><span data-stu-id="98e2b-172">You can add a filter to a particular action method or to a controller class by using an attribute.</span></span> <span data-ttu-id="98e2b-173">或者，也可以注册所有控制器和操作的全局筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-173">Or you can register a filter globally for all controllers and actions.</span></span> <span data-ttu-id="98e2b-174">通过将筛选器添加到 `ConfigureServices` 中的 `MvcOptions.Filters` 集合，可以将其添加为全局筛选器：</span><span class="sxs-lookup"><span data-stu-id="98e2b-174">Filters are added globally by adding it to the `MvcOptions.Filters` collection in `ConfigureServices`:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=5-8)]

### <a name="default-order-of-execution"></a><span data-ttu-id="98e2b-175">默认执行顺序</span><span class="sxs-lookup"><span data-stu-id="98e2b-175">Default order of execution</span></span>

<span data-ttu-id="98e2b-176">当管道的某个特定阶段有多个筛选器时，作用域可确定筛选器执行的默认顺序。</span><span class="sxs-lookup"><span data-stu-id="98e2b-176">When there are multiple filters for a particular stage of the pipeline, scope determines the default order of filter execution.</span></span>  <span data-ttu-id="98e2b-177">全局筛选器涵盖类筛选器，类筛选器又涵盖方法筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-177">Global filters surround class filters, which in turn surround method filters.</span></span> <span data-ttu-id="98e2b-178">这种模式有时称为“俄罗斯套娃”嵌套，因为增加的每个作用域都包装在前一个作用域中，就像[套娃](https://wikipedia.org/wiki/Matryoshka_doll)一样。</span><span class="sxs-lookup"><span data-stu-id="98e2b-178">This is sometimes referred to as "Russian doll" nesting, as each increase in scope is wrapped around the previous scope, like a [nesting doll](https://wikipedia.org/wiki/Matryoshka_doll).</span></span> <span data-ttu-id="98e2b-179">通常情况下，无需显式确定排序便可获得所需的重写行为。</span><span class="sxs-lookup"><span data-stu-id="98e2b-179">You generally get the desired overriding behavior without having to explicitly determine ordering.</span></span>

<span data-ttu-id="98e2b-180">在这种嵌套模式下，筛选器的 *after* 代码会按照与 *before* 代码相反的顺序运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-180">As a result of this nesting, the *after* code of filters runs in the reverse order of the *before* code.</span></span> <span data-ttu-id="98e2b-181">其序列如下所示：</span><span class="sxs-lookup"><span data-stu-id="98e2b-181">The sequence looks like this:</span></span>

* <span data-ttu-id="98e2b-182">筛选器的 *before* 代码应用于全局</span><span class="sxs-lookup"><span data-stu-id="98e2b-182">The *before* code of filters applied globally</span></span>
  * <span data-ttu-id="98e2b-183">筛选器的 *before* 代码应用于控制器</span><span class="sxs-lookup"><span data-stu-id="98e2b-183">The *before* code of filters applied to controllers</span></span>
    * <span data-ttu-id="98e2b-184">筛选器的 *before* 代码应用于操作方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-184">The *before* code of filters applied to action methods</span></span>
    * <span data-ttu-id="98e2b-185">筛选器的 *after* 代码应用于操作方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-185">The *after* code of filters applied to action methods</span></span>
  * <span data-ttu-id="98e2b-186">筛选器的 *after* 代码应用于控制器</span><span class="sxs-lookup"><span data-stu-id="98e2b-186">The *after* code of filters applied to controllers</span></span>
* <span data-ttu-id="98e2b-187">筛选器的 *after* 代码应用于全局</span><span class="sxs-lookup"><span data-stu-id="98e2b-187">The *after* code of filters applied globally</span></span>
  
<span data-ttu-id="98e2b-188">下面的示例阐释了为同步操作筛选器调用筛选器方法的顺序。</span><span class="sxs-lookup"><span data-stu-id="98e2b-188">Here's an example that illustrates the order in which filter methods are called for synchronous Action filters.</span></span>

| <span data-ttu-id="98e2b-189">序列</span><span class="sxs-lookup"><span data-stu-id="98e2b-189">Sequence</span></span> | <span data-ttu-id="98e2b-190">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="98e2b-190">Filter scope</span></span> | <span data-ttu-id="98e2b-191">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-191">Filter method</span></span> |
|:--------:|:------------:|:-------------:|
| <span data-ttu-id="98e2b-192">1</span><span class="sxs-lookup"><span data-stu-id="98e2b-192">1</span></span> | <span data-ttu-id="98e2b-193">Global</span><span class="sxs-lookup"><span data-stu-id="98e2b-193">Global</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98e2b-194">2</span><span class="sxs-lookup"><span data-stu-id="98e2b-194">2</span></span> | <span data-ttu-id="98e2b-195">控制器</span><span class="sxs-lookup"><span data-stu-id="98e2b-195">Controller</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98e2b-196">3</span><span class="sxs-lookup"><span data-stu-id="98e2b-196">3</span></span> | <span data-ttu-id="98e2b-197">方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-197">Method</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98e2b-198">4</span><span class="sxs-lookup"><span data-stu-id="98e2b-198">4</span></span> | <span data-ttu-id="98e2b-199">方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-199">Method</span></span> | `OnActionExecuted` |
| <span data-ttu-id="98e2b-200">5</span><span class="sxs-lookup"><span data-stu-id="98e2b-200">5</span></span> | <span data-ttu-id="98e2b-201">控制器</span><span class="sxs-lookup"><span data-stu-id="98e2b-201">Controller</span></span> | `OnActionExecuted` |
| <span data-ttu-id="98e2b-202">6</span><span class="sxs-lookup"><span data-stu-id="98e2b-202">6</span></span> | <span data-ttu-id="98e2b-203">Global</span><span class="sxs-lookup"><span data-stu-id="98e2b-203">Global</span></span> | `OnActionExecuted` |

<span data-ttu-id="98e2b-204">此序列显示：</span><span class="sxs-lookup"><span data-stu-id="98e2b-204">This sequence shows:</span></span>

* <span data-ttu-id="98e2b-205">方法筛选器已嵌套在控制器筛选器中。</span><span class="sxs-lookup"><span data-stu-id="98e2b-205">The method filter is nested within the controller filter.</span></span>
* <span data-ttu-id="98e2b-206">控制器筛选器已嵌套在全局筛选器中。</span><span class="sxs-lookup"><span data-stu-id="98e2b-206">The controller filter is nested within the global filter.</span></span> 

<span data-ttu-id="98e2b-207">换句话说，如果处于异步筛选器的 On*Stage*ExecutionAsync 方法内，则当代码位于堆栈上时，所有筛选器都在更严格的作用域中运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-207">To put it another way, if you're inside an async filter's On*Stage*ExecutionAsync method, all of the filters with a tighter scope run while your code is on the stack.</span></span>

> [!NOTE]
> <span data-ttu-id="98e2b-208">继承自 `Controller` 基类的每个控制器都包括 `OnActionExecuting` 和 `OnActionExecuted` 方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-208">Every controller that inherits from the `Controller` base class includes `OnActionExecuting` and `OnActionExecuted` methods.</span></span> <span data-ttu-id="98e2b-209">这些方法包装针对某项给定操作运行的筛选器：`OnActionExecuting` 在所有筛选器之前调用，`OnActionExecuted` 在所有筛选器之后调用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-209">These methods wrap the filters that run for a given action:  `OnActionExecuting` is called before any of the filters, and `OnActionExecuted` is called after all of the filters.</span></span>

### <a name="overriding-the-default-order"></a><span data-ttu-id="98e2b-210">重写默认顺序</span><span class="sxs-lookup"><span data-stu-id="98e2b-210">Overriding the default order</span></span>

<span data-ttu-id="98e2b-211">可以通过实现 `IOrderedFilter` 来重写默认执行序列。</span><span class="sxs-lookup"><span data-stu-id="98e2b-211">You can override the default sequence of execution by implementing `IOrderedFilter`.</span></span> <span data-ttu-id="98e2b-212">此接口公开了一个 `Order` 属性来确定执行顺序，该属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="98e2b-212">This interface exposes an `Order` property that takes precedence over scope to determine the order of execution.</span></span> <span data-ttu-id="98e2b-213">具有较低 `Order` 值的筛选器会在具有较高 `Order` 值的筛选器之前执行其 *before* 代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-213">A filter with a lower `Order` value will have its *before* code executed before that of a filter with a higher value of `Order`.</span></span> <span data-ttu-id="98e2b-214">具有较低 `Order` 值的筛选器会在具有较高 `Order` 值的筛选器之后执行其 *after* 代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-214">A filter with a lower `Order` value will have its *after* code executed after that of a filter with a higher `Order` value.</span></span> <span data-ttu-id="98e2b-215">可使用构造函数参数来设置 `Order` 属性：</span><span class="sxs-lookup"><span data-stu-id="98e2b-215">You can set the `Order` property by using a constructor parameter:</span></span>

```csharp
[MyFilter(Name = "Controller Level Attribute", Order=1)]
```

<span data-ttu-id="98e2b-216">如果具有上述示例中所示的 3 个相同的操作筛选器，但将控制器和全局筛选器的 `Order` 属性分别设置为 1 和 2，则会反转执行顺序。</span><span class="sxs-lookup"><span data-stu-id="98e2b-216">If you have the same 3 Action filters shown in the preceding example but set the `Order` property of the controller and global filters to 1 and 2 respectively, the order of execution would be reversed.</span></span>

| <span data-ttu-id="98e2b-217">序列</span><span class="sxs-lookup"><span data-stu-id="98e2b-217">Sequence</span></span> | <span data-ttu-id="98e2b-218">筛选器作用域</span><span class="sxs-lookup"><span data-stu-id="98e2b-218">Filter scope</span></span> | <span data-ttu-id="98e2b-219">`Order` 属性</span><span class="sxs-lookup"><span data-stu-id="98e2b-219">`Order` property</span></span> | <span data-ttu-id="98e2b-220">筛选器方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-220">Filter method</span></span> |
|:--------:|:------------:|:-----------------:|:-------------:|
| <span data-ttu-id="98e2b-221">1</span><span class="sxs-lookup"><span data-stu-id="98e2b-221">1</span></span> | <span data-ttu-id="98e2b-222">方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-222">Method</span></span> | <span data-ttu-id="98e2b-223">0</span><span class="sxs-lookup"><span data-stu-id="98e2b-223">0</span></span> | `OnActionExecuting` |
| <span data-ttu-id="98e2b-224">2</span><span class="sxs-lookup"><span data-stu-id="98e2b-224">2</span></span> | <span data-ttu-id="98e2b-225">控制器</span><span class="sxs-lookup"><span data-stu-id="98e2b-225">Controller</span></span> | <span data-ttu-id="98e2b-226">1</span><span class="sxs-lookup"><span data-stu-id="98e2b-226">1</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="98e2b-227">3</span><span class="sxs-lookup"><span data-stu-id="98e2b-227">3</span></span> | <span data-ttu-id="98e2b-228">Global</span><span class="sxs-lookup"><span data-stu-id="98e2b-228">Global</span></span> | <span data-ttu-id="98e2b-229">2</span><span class="sxs-lookup"><span data-stu-id="98e2b-229">2</span></span>  | `OnActionExecuting` |
| <span data-ttu-id="98e2b-230">4</span><span class="sxs-lookup"><span data-stu-id="98e2b-230">4</span></span> | <span data-ttu-id="98e2b-231">Global</span><span class="sxs-lookup"><span data-stu-id="98e2b-231">Global</span></span> | <span data-ttu-id="98e2b-232">2</span><span class="sxs-lookup"><span data-stu-id="98e2b-232">2</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="98e2b-233">5</span><span class="sxs-lookup"><span data-stu-id="98e2b-233">5</span></span> | <span data-ttu-id="98e2b-234">控制器</span><span class="sxs-lookup"><span data-stu-id="98e2b-234">Controller</span></span> | <span data-ttu-id="98e2b-235">1</span><span class="sxs-lookup"><span data-stu-id="98e2b-235">1</span></span>  | `OnActionExecuted` |
| <span data-ttu-id="98e2b-236">6</span><span class="sxs-lookup"><span data-stu-id="98e2b-236">6</span></span> | <span data-ttu-id="98e2b-237">方法</span><span class="sxs-lookup"><span data-stu-id="98e2b-237">Method</span></span> | <span data-ttu-id="98e2b-238">0</span><span class="sxs-lookup"><span data-stu-id="98e2b-238">0</span></span>  | `OnActionExecuted` |

<span data-ttu-id="98e2b-239">在确定筛选器的运行顺序时，`Order` 属性优先于作用域。</span><span class="sxs-lookup"><span data-stu-id="98e2b-239">The `Order` property trumps scope when determining the order in which filters will run.</span></span> <span data-ttu-id="98e2b-240">先按顺序对筛选器排序，然后使用作用域消除并列问题。</span><span class="sxs-lookup"><span data-stu-id="98e2b-240">Filters are sorted first by order, then scope is used to break ties.</span></span> <span data-ttu-id="98e2b-241">所有内置筛选器实现 `IOrderedFilter` 并将默认 `Order` 值设为 0。</span><span class="sxs-lookup"><span data-stu-id="98e2b-241">All of the built-in filters implement `IOrderedFilter` and set the default `Order` value to 0.</span></span> <span data-ttu-id="98e2b-242">对于内置筛选器，作用域会确定顺序，除非将 `Order` 设为非零值。</span><span class="sxs-lookup"><span data-stu-id="98e2b-242">For built-in filters, scope determines order unless you set `Order` to a non-zero value.</span></span>

## <a name="cancellation-and-short-circuiting"></a><span data-ttu-id="98e2b-243">取消和设置短路</span><span class="sxs-lookup"><span data-stu-id="98e2b-243">Cancellation and short circuiting</span></span>

<span data-ttu-id="98e2b-244">通过设置提供给筛选器方法的 `context` 参数上的 `Result` 属性，可以在筛选器管道的任意位置设置短路。</span><span class="sxs-lookup"><span data-stu-id="98e2b-244">You can short-circuit the filter pipeline at any point by setting the `Result` property on the `context` parameter provided to the filter method.</span></span> <span data-ttu-id="98e2b-245">例如，以下资源筛选器将阻止执行管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="98e2b-245">For instance, the following Resource filter prevents the rest of the pipeline from executing.</span></span>

<a name="short-circuiting-resource-filter"></a>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ShortCircuitingResourceFilterAttribute.cs?highlight=12,13,14,15)]

<span data-ttu-id="98e2b-246">在下面的代码中，`ShortCircuitingResourceFilter` 和 `AddHeader` 筛选器都以 `SomeResource` 操作方法为目标。</span><span class="sxs-lookup"><span data-stu-id="98e2b-246">In the following code, both the `ShortCircuitingResourceFilter` and the `AddHeader` filter target the `SomeResource` action method.</span></span> <span data-ttu-id="98e2b-247">`ShortCircuitingResourceFilter`：</span><span class="sxs-lookup"><span data-stu-id="98e2b-247">The `ShortCircuitingResourceFilter`:</span></span>

* <span data-ttu-id="98e2b-248">先运行，因为它是资源筛选器且 `AddHeader` 是操作筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-248">Runs first, because it's a Resource Filter and `AddHeader` is an Action Filter.</span></span>
* <span data-ttu-id="98e2b-249">对管道的其余部分进行短路处理。</span><span class="sxs-lookup"><span data-stu-id="98e2b-249">Short-circuits the rest of the pipeline.</span></span>

<span data-ttu-id="98e2b-250">这样 `AddHeader` 筛选器就不会为 `SomeResource` 操作运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-250">Therefore the `AddHeader` filter never runs for the `SomeResource` action.</span></span> <span data-ttu-id="98e2b-251">如果这两个筛选器都应用于操作方法级别，只要 `ShortCircuitingResourceFilter` 先运行，此行为就不会变。</span><span class="sxs-lookup"><span data-stu-id="98e2b-251">This behavior would be the same if both filters were applied at the action method level, provided the `ShortCircuitingResourceFilter` ran first.</span></span> <span data-ttu-id="98e2b-252">先运行 `ShortCircuitingResourceFilter`（考虑到它的筛选器类型），或显式使用 `Order` 属性。</span><span class="sxs-lookup"><span data-stu-id="98e2b-252">The `ShortCircuitingResourceFilter` runs first because of its filter type, or by explicit use of `Order` property.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/SampleController.cs?name=snippet_AddHeader&highlight=1,9)]

## <a name="dependency-injection"></a><span data-ttu-id="98e2b-253">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="98e2b-253">Dependency injection</span></span>

<span data-ttu-id="98e2b-254">可按类型或实例添加筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-254">Filters can be added by type or by instance.</span></span> <span data-ttu-id="98e2b-255">如果添加实例，该实例将用于每个请求。</span><span class="sxs-lookup"><span data-stu-id="98e2b-255">If you add an instance, that instance will be used for every request.</span></span> <span data-ttu-id="98e2b-256">如果添加类型，则将激活该类型，这意味着将为每个请求创建一个实例，并且[依赖关系注入](../../fundamentals/dependency-injection.md) (DI) 将填充所有构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="98e2b-256">If you add a type, it will be type-activated, meaning an instance will be created for each request and any constructor dependencies will be populated by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="98e2b-257">按类型添加筛选器等效于 `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-257">Adding a filter by type is equivalent to `filters.Add(new TypeFilterAttribute(typeof(MyFilter)))`.</span></span>

<span data-ttu-id="98e2b-258">如果将筛选器作为属性实现并直接添加到控制器类或操作方法中，则该筛选器不能由[依赖关系注入](../../fundamentals/dependency-injection.md) (DI) 提供构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="98e2b-258">Filters that are implemented as attributes and added directly to controller classes or action methods cannot have constructor dependencies provided by [dependency injection](../../fundamentals/dependency-injection.md) (DI).</span></span> <span data-ttu-id="98e2b-259">这是因为属性在应用时必须提供自己的构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="98e2b-259">This is because attributes must have their constructor parameters supplied where they're applied.</span></span> <span data-ttu-id="98e2b-260">这是属性工作原理上的限制。</span><span class="sxs-lookup"><span data-stu-id="98e2b-260">This is a limitation of how attributes work.</span></span>

<span data-ttu-id="98e2b-261">如果筛选器具有一些需要从 DI 访问的依赖项，有几种受支持的方法可用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-261">If your filters have dependencies that you need to access from DI, there are several supported approaches.</span></span> <span data-ttu-id="98e2b-262">可以使用以下接口之一，将筛选器应用于类或操作方法：</span><span class="sxs-lookup"><span data-stu-id="98e2b-262">You can apply your filter to a class or action method using one of the following:</span></span>

* `ServiceFilterAttribute`
* `TypeFilterAttribute`
* <span data-ttu-id="98e2b-263">在属性上实现的 `IFilterFactory`</span><span class="sxs-lookup"><span data-stu-id="98e2b-263">`IFilterFactory` implemented on your attribute</span></span>

> [!NOTE]
> <span data-ttu-id="98e2b-264">记录器就是一种可能需要从 DI 获取的依赖项。</span><span class="sxs-lookup"><span data-stu-id="98e2b-264">One dependency you might want to get from DI is a logger.</span></span> <span data-ttu-id="98e2b-265">但是，应避免单纯为进行日志记录而创建和使用筛选器，因为[内置的框架日志记录功能](xref:fundamentals/logging/index)可能已经提供用户所需。</span><span class="sxs-lookup"><span data-stu-id="98e2b-265">However, avoid creating and using filters purely for logging purposes, since the [built-in framework logging features](xref:fundamentals/logging/index) may already provide what you need.</span></span> <span data-ttu-id="98e2b-266">如果要将日志记录功能添加到筛选器，它应重点关注业务领域问题或特定于筛选器的行为，而非 MVC 操作或其他框架事件。</span><span class="sxs-lookup"><span data-stu-id="98e2b-266">If you're going to add logging to your filters, it should focus on business domain concerns or behavior specific to your filter, rather than MVC actions or other framework events.</span></span>

### <a name="servicefilterattribute"></a><span data-ttu-id="98e2b-267">ServiceFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="98e2b-267">ServiceFilterAttribute</span></span>

<span data-ttu-id="98e2b-268">在 DI 中注册服务筛选器实现类型。</span><span class="sxs-lookup"><span data-stu-id="98e2b-268">Service filter implementation types are registered in DI.</span></span> <span data-ttu-id="98e2b-269">`ServiceFilterAttribute` 可从 DI 检索筛选器实例。</span><span class="sxs-lookup"><span data-stu-id="98e2b-269">A `ServiceFilterAttribute` retrieves an instance of the filter from DI.</span></span> <span data-ttu-id="98e2b-270">将 `ServiceFilterAttribute` 添加到 `Startup.ConfigureServices` 中的容器中，并在 `[ServiceFilter]` 属性中引用它：</span><span class="sxs-lookup"><span data-stu-id="98e2b-270">Add the `ServiceFilterAttribute` to the container in `Startup.ConfigureServices`, and reference it in a `[ServiceFilter]` attribute:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Startup.cs?name=snippet_ConfigureServices&highlight=11)]

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_ServiceFilter&highlight=1)]

<span data-ttu-id="98e2b-271">使用 `ServiceFilterAttribute` 时，`IsReusable` 设置会提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-271">When using `ServiceFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="98e2b-272">该框架不保证在稍后的某个时刻将创建筛选器的单个实例，或不会从 DI 容器重新请求筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-272">The framework provides no guarantees that a single instance of the filter will be created or the filter will not be re-requested from the DI container at some later point.</span></span> <span data-ttu-id="98e2b-273">如果使用的筛选器依赖于具有除单一实例以外的生命周期的服务，请避免使用 `IsReusable`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-273">Avoid using `IsReusable` when using a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="98e2b-274">使用 `ServiceFilterAttribute` 时不注册筛选器类型会引发异常：</span><span class="sxs-lookup"><span data-stu-id="98e2b-274">Using `ServiceFilterAttribute` without registering the filter type results in an exception:</span></span>

```
System.InvalidOperationException: No service for type
'FiltersSample.Filters.AddHeaderFilterWithDI' has been registered.
```

<span data-ttu-id="98e2b-275">`ServiceFilterAttribute` 可实现 `IFilterFactory`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-275">`ServiceFilterAttribute` implements `IFilterFactory`.</span></span> <span data-ttu-id="98e2b-276">`IFilterFactory` 公开用于创建 `IFilterMetadata` 实例的 `CreateInstance` 方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-276">`IFilterFactory` exposes the `CreateInstance` method for creating an `IFilterMetadata` instance.</span></span> <span data-ttu-id="98e2b-277">`CreateInstance` 方法从服务容器 (DI) 中加载指定的类型。</span><span class="sxs-lookup"><span data-stu-id="98e2b-277">The `CreateInstance` method loads the specified type from the services container (DI).</span></span>

### <a name="typefilterattribute"></a><span data-ttu-id="98e2b-278">TypeFilterAttribute</span><span class="sxs-lookup"><span data-stu-id="98e2b-278">TypeFilterAttribute</span></span>

<span data-ttu-id="98e2b-279">`TypeFilterAttribute` 与 `ServiceFilterAttribute` 类似，但不会直接从 DI 容器解析其类型。</span><span class="sxs-lookup"><span data-stu-id="98e2b-279">`TypeFilterAttribute` is similar to `ServiceFilterAttribute`, but its type isn't resolved directly from the DI container.</span></span> <span data-ttu-id="98e2b-280">它使用 `Microsoft.Extensions.DependencyInjection.ObjectFactory` 对类型进行实例化。</span><span class="sxs-lookup"><span data-stu-id="98e2b-280">It instantiates the type by using `Microsoft.Extensions.DependencyInjection.ObjectFactory`.</span></span>

<span data-ttu-id="98e2b-281">由于存在这种差异，所以存在以下情况：</span><span class="sxs-lookup"><span data-stu-id="98e2b-281">Because of this difference:</span></span>

* <span data-ttu-id="98e2b-282">使用 `TypeFilterAttribute` 引用的类型不需要先注册在容器中。</span><span class="sxs-lookup"><span data-stu-id="98e2b-282">Types that are referenced using the `TypeFilterAttribute` don't need to be registered with the container first.</span></span>  <span data-ttu-id="98e2b-283">它们具备由容器实现的依赖项。</span><span class="sxs-lookup"><span data-stu-id="98e2b-283">They do have their dependencies fulfilled by the container.</span></span> 
* <span data-ttu-id="98e2b-284">`TypeFilterAttribute` 可以选择为类型接受构造函数参数。</span><span class="sxs-lookup"><span data-stu-id="98e2b-284">`TypeFilterAttribute` can optionally accept constructor arguments for the type.</span></span>

<span data-ttu-id="98e2b-285">使用 `TypeFilterAttribute` 时，`IsReusable` 设置会提示：筛选器实例可能在其创建的请求范围之外被重用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-285">When using `TypeFilterAttribute`, setting `IsReusable` is a hint that the filter instance *may* be reused outside of the request scope it was created within.</span></span> <span data-ttu-id="98e2b-286">该框架不保证将创建筛选器的单一实例。</span><span class="sxs-lookup"><span data-stu-id="98e2b-286">The framework provides no guarantees that a single instance of the filter will be created.</span></span> <span data-ttu-id="98e2b-287">如果使用的筛选器依赖于具有除单一实例以外的生命周期的服务，请避免使用 `IsReusable`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-287">Avoid using `IsReusable` when using a filter that depends on services with a lifetime other than singleton.</span></span>

<span data-ttu-id="98e2b-288">下面的示例演示如何使用 `TypeFilterAttribute` 将参数传递到类型：</span><span class="sxs-lookup"><span data-stu-id="98e2b-288">The following example demonstrates how to pass arguments to a type using `TypeFilterAttribute`:</span></span>

[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_TypeFilter&highlight=1,2)]
[!code-csharp[](../../mvc/controllers/filters/sample/src/FiltersSample/Filters/LogConstantFilter.cs?name=snippet_TypeFilter_Implementation&highlight=6)]

### <a name="ifilterfactory-implemented-on-your-attribute"></a><span data-ttu-id="98e2b-289">在属性上实现 IFilterFactory</span><span class="sxs-lookup"><span data-stu-id="98e2b-289">IFilterFactory implemented on your attribute</span></span>

<span data-ttu-id="98e2b-290">如果你的筛选器符合以下描述：</span><span class="sxs-lookup"><span data-stu-id="98e2b-290">If you have a filter that:</span></span>

* <span data-ttu-id="98e2b-291">不需要任何参数。</span><span class="sxs-lookup"><span data-stu-id="98e2b-291">Doesn't require any arguments.</span></span>
* <span data-ttu-id="98e2b-292">具备需要由 DI 填充的构造函数依赖项。</span><span class="sxs-lookup"><span data-stu-id="98e2b-292">Has constructor dependencies that need to be filled by DI.</span></span>

<span data-ttu-id="98e2b-293">在类和方法上可以不使用 `[TypeFilter(typeof(FilterType))]`改用自己命名的属性。</span><span class="sxs-lookup"><span data-stu-id="98e2b-293">You can use your own named attribute on classes and methods instead of `[TypeFilter(typeof(FilterType))]`).</span></span> <span data-ttu-id="98e2b-294">下面的筛选器展示了如何实现此操作：</span><span class="sxs-lookup"><span data-stu-id="98e2b-294">The following filter shows how this can be implemented:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilterAttribute.cs?name=snippet_TypeFilterAttribute&highlight=1,3,7)]

<span data-ttu-id="98e2b-295">可以使用 `[SampleActionFilter]` 语法将此筛选器应用于类或方法，而不必使用 `[TypeFilter]` 或 `[ServiceFilter]`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-295">This filter can be applied to classes or methods using the `[SampleActionFilter]` syntax, instead of having to use `[TypeFilter]` or `[ServiceFilter]`.</span></span>

## <a name="authorization-filters"></a><span data-ttu-id="98e2b-296">授权筛选器</span><span class="sxs-lookup"><span data-stu-id="98e2b-296">Authorization filters</span></span>

<span data-ttu-id="98e2b-297">授权筛选器：</span><span class="sxs-lookup"><span data-stu-id="98e2b-297">*Authorization filters*:</span></span>

* <span data-ttu-id="98e2b-298">控制对操作方法的访问。</span><span class="sxs-lookup"><span data-stu-id="98e2b-298">Control access to action methods.</span></span>
* <span data-ttu-id="98e2b-299">是筛选器管道中要执行的第一个筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-299">Are the first filters to be executed within the filter pipeline.</span></span> 
* <span data-ttu-id="98e2b-300">具有在它之前的执行的方法，但没有之后执行的方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-300">Have a before method, but no after method.</span></span> 

<span data-ttu-id="98e2b-301">用户只有在编写自己的授权框架时，才应编写自定义授权筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-301">You should only write a custom authorization filter if you are writing your own authorization framework.</span></span> <span data-ttu-id="98e2b-302">建议配置授权策略或编写自定义授权策略，而不是编写自定义筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-302">Prefer configuring your authorization policies or writing a custom authorization policy over writing a custom filter.</span></span> <span data-ttu-id="98e2b-303">内置筛选器实现只负责调用授权系统。</span><span class="sxs-lookup"><span data-stu-id="98e2b-303">The built-in filter implementation is just responsible for calling the authorization system.</span></span>

<span data-ttu-id="98e2b-304">切勿在授权筛选器内引发异常，因为没有任何能处理该异常的组件（异常筛选器不会进行处理）。</span><span class="sxs-lookup"><span data-stu-id="98e2b-304">You shouldn't throw exceptions within authorization filters, since nothing will handle the exception (exception filters won't handle them).</span></span> <span data-ttu-id="98e2b-305">在出现异常时请小心应对。</span><span class="sxs-lookup"><span data-stu-id="98e2b-305">Consider issuing a challenge when an exception occurs.</span></span>

<span data-ttu-id="98e2b-306">详细了解[授权](xref:security/authorization/introduction)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-306">Learn more about [Authorization](xref:security/authorization/introduction).</span></span>

## <a name="resource-filters"></a><span data-ttu-id="98e2b-307">资源筛选器</span><span class="sxs-lookup"><span data-stu-id="98e2b-307">Resource filters</span></span>

* <span data-ttu-id="98e2b-308">实现 `IResourceFilter` 或 `IAsyncResourceFilter` 接口，</span><span class="sxs-lookup"><span data-stu-id="98e2b-308">Implement either the `IResourceFilter` or `IAsyncResourceFilter` interface,</span></span>
* <span data-ttu-id="98e2b-309">它们的执行会覆盖筛选器管道的绝大部分。</span><span class="sxs-lookup"><span data-stu-id="98e2b-309">Their execution wraps most of the filter pipeline.</span></span> 
* <span data-ttu-id="98e2b-310">只有[授权筛选器](#authorization-filters)在资源筛选器之前运行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-310">Only [Authorization filters](#authorization-filters) run before Resource filters.</span></span>

<span data-ttu-id="98e2b-311">如果需要使某个请求正在执行的大部分工作短路，资源筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-311">Resource filters are useful to short-circuit most of the work a request is doing.</span></span> <span data-ttu-id="98e2b-312">例如，如果响应在缓存中，则缓存筛选器可以绕开管道的其余阶段。</span><span class="sxs-lookup"><span data-stu-id="98e2b-312">For example, a caching filter can avoid the rest of the pipeline if the response is in the cache.</span></span>

<span data-ttu-id="98e2b-313">前面所示的[短路资源筛选器](#short-circuiting-resource-filter)便是一种资源筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-313">The [short circuiting resource filter](#short-circuiting-resource-filter) shown earlier is one example of a resource filter.</span></span> <span data-ttu-id="98e2b-314">另一个示例是 [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs)：</span><span class="sxs-lookup"><span data-stu-id="98e2b-314">Another example is [DisableFormValueModelBindingAttribute](https://github.com/aspnet/Entropy/blob/rel/1.1.1/samples/Mvc.FileUpload/Filters/DisableFormValueModelBindingAttribute.cs):</span></span>

* <span data-ttu-id="98e2b-315">可以防止模型绑定访问表单数据。</span><span class="sxs-lookup"><span data-stu-id="98e2b-315">It prevents model binding from accessing the form data.</span></span> 
* <span data-ttu-id="98e2b-316">如果要上传大型文件，同时想防止表单被读入内存，那么此筛选器会很有用。</span><span class="sxs-lookup"><span data-stu-id="98e2b-316">It's useful for large file uploads and want to prevent the form from being read into memory.</span></span>

## <a name="action-filters"></a><span data-ttu-id="98e2b-317">操作筛选器</span><span class="sxs-lookup"><span data-stu-id="98e2b-317">Action filters</span></span>

<span data-ttu-id="98e2b-318">操作筛选器：</span><span class="sxs-lookup"><span data-stu-id="98e2b-318">*Action filters*:</span></span>

* <span data-ttu-id="98e2b-319">实现 `IActionFilter` 或 `IAsyncActionFilter` 接口。</span><span class="sxs-lookup"><span data-stu-id="98e2b-319">Implement either the `IActionFilter` or `IAsyncActionFilter` interface.</span></span>
* <span data-ttu-id="98e2b-320">它们的执行围绕着操作方法的执行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-320">Their execution surrounds the execution of action methods.</span></span>

<span data-ttu-id="98e2b-321">下面是一个操作筛选器示例：</span><span class="sxs-lookup"><span data-stu-id="98e2b-321">Here's a sample action filter:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/SampleActionFilter.cs?name=snippet_ActionFilter)]

<span data-ttu-id="98e2b-322">[ActionExecutingContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) 提供以下属性：</span><span class="sxs-lookup"><span data-stu-id="98e2b-322">The [ActionExecutingContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutingcontext) provides the following properties:</span></span>

* <span data-ttu-id="98e2b-323">`ActionArguments`：用于处理对操作的输入。</span><span class="sxs-lookup"><span data-stu-id="98e2b-323">`ActionArguments` - lets you manipulate the inputs to the action.</span></span>
* <span data-ttu-id="98e2b-324">`Controller`：用于处理控制器实例。</span><span class="sxs-lookup"><span data-stu-id="98e2b-324">`Controller` - lets you manipulate the controller instance.</span></span> 
* <span data-ttu-id="98e2b-325">`Result`：设置此属性会使操作方法和后续操作筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="98e2b-325">`Result` - setting this short-circuits execution of the action method and subsequent action filters.</span></span> <span data-ttu-id="98e2b-326">引发异常也会阻止操作方法和后续筛选器的执行，但会被视为失败，而不是一个成功的结果。</span><span class="sxs-lookup"><span data-stu-id="98e2b-326">Throwing an exception also prevents execution of the action method and subsequent filters, but is treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="98e2b-327">[ActionExecutedContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) 提供 `Controller` 和 `Result` 以及下列属性：</span><span class="sxs-lookup"><span data-stu-id="98e2b-327">The [ActionExecutedContext](/dotnet/api/microsoft.aspnetcore.mvc.filters.actionexecutedcontext) provides `Controller` and `Result` plus the following properties:</span></span>

* <span data-ttu-id="98e2b-328">`Canceled`：如果操作执行已被另一个筛选器设置短路，则为 true。</span><span class="sxs-lookup"><span data-stu-id="98e2b-328">`Canceled` - will be true if the action execution was short-circuited by another filter.</span></span>
* <span data-ttu-id="98e2b-329">`Exception`：如果操作或后续操作筛选器引发了异常，则为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="98e2b-329">`Exception` - will be non-null if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="98e2b-330">将此属性设置为 NULL 可有效地“处理”异常，并且将执行 `Result`，就像它是从操作方法正常返回的一样。</span><span class="sxs-lookup"><span data-stu-id="98e2b-330">Setting this property to null effectively 'handles' an exception, and `Result` will be executed as if it were returned from the action method normally.</span></span>

<span data-ttu-id="98e2b-331">对于 `IAsyncActionFilter`，一个向 `ActionExecutionDelegate` 的调用可以达到以下目的：</span><span class="sxs-lookup"><span data-stu-id="98e2b-331">For an `IAsyncActionFilter`, a call to the `ActionExecutionDelegate`:</span></span>

* <span data-ttu-id="98e2b-332">执行所有后续操作筛选器和操作方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-332">Executes any subsequent action filters and the action method.</span></span>
* <span data-ttu-id="98e2b-333">返回 `ActionExecutedContext`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-333">returns `ActionExecutedContext`.</span></span> 

<span data-ttu-id="98e2b-334">若要设置短路，可将 `ActionExecutingContext.Result` 分配到某个结果实例，并且不调用 `ActionExecutionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-334">To short-circuit, assign `ActionExecutingContext.Result` to some result instance and don't call the `ActionExecutionDelegate`.</span></span>

<span data-ttu-id="98e2b-335">该框架提供一个可子类化的抽象 `ActionFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-335">The framework provides an abstract `ActionFilterAttribute` that you can subclass.</span></span> 

<span data-ttu-id="98e2b-336">操作筛选器可用于验证模型状态，并在状态为无效时返回任何错误：</span><span class="sxs-lookup"><span data-stu-id="98e2b-336">You can use an action filter to validate model state and return any errors if the state is invalid:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/ValidateModelAttribute.cs)]

<span data-ttu-id="98e2b-337">`OnActionExecuted` 方法在操作方法之后运行，可通过 `ActionExecutedContext.Result` 属性查看和处理操作结果。</span><span class="sxs-lookup"><span data-stu-id="98e2b-337">The `OnActionExecuted` method runs after the action method and can see and manipulate the results of the action through the `ActionExecutedContext.Result` property.</span></span> <span data-ttu-id="98e2b-338">如果操作执行已被另一个筛选器设置短路，则 `ActionExecutedContext.Canceled` 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="98e2b-338">`ActionExecutedContext.Canceled` will be set to true if the action execution was short-circuited by another filter.</span></span> <span data-ttu-id="98e2b-339">如果操作或后续操作筛选器引发了异常，则 `ActionExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="98e2b-339">`ActionExecutedContext.Exception` will be set to a non-null value if the action or a subsequent action filter threw an exception.</span></span> <span data-ttu-id="98e2b-340">将 `ActionExecutedContext.Exception` 设置为 null：</span><span class="sxs-lookup"><span data-stu-id="98e2b-340">Setting `ActionExecutedContext.Exception` to null:</span></span>

* <span data-ttu-id="98e2b-341">有效地“处理”异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-341">Effectively 'handles' an exception.</span></span>
* <span data-ttu-id="98e2b-342">执行 `ActionExectedContext.Result`，从操作方法中将它正常返回。</span><span class="sxs-lookup"><span data-stu-id="98e2b-342">`ActionExectedContext.Result` is executed as if it were returned normally from the action method.</span></span>

## <a name="exception-filters"></a><span data-ttu-id="98e2b-343">异常筛选器</span><span class="sxs-lookup"><span data-stu-id="98e2b-343">Exception filters</span></span>

<span data-ttu-id="98e2b-344">*异常筛选器*可实现 `IExceptionFilter` 或 `IAsyncExceptionFilter` 接口。</span><span class="sxs-lookup"><span data-stu-id="98e2b-344">*Exception filters* implement either the `IExceptionFilter` or `IAsyncExceptionFilter` interface.</span></span> <span data-ttu-id="98e2b-345">它们可用于为应用实现常见的错误处理策略。</span><span class="sxs-lookup"><span data-stu-id="98e2b-345">They can be used to implement common error handling policies for an app.</span></span> 

<span data-ttu-id="98e2b-346">下面的异常筛选器示例使用自定义开发人员错误视图，显示在开发应用时发生的异常的相关详细信息：</span><span class="sxs-lookup"><span data-stu-id="98e2b-346">The following sample exception filter uses a custom developer error view to display details about exceptions that occur when the app is in development:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/CustomExceptionFilterAttribute.cs?name=snippet_ExceptionFilter&highlight=1,14)]

<span data-ttu-id="98e2b-347">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="98e2b-347">Exception filters:</span></span>

* <span data-ttu-id="98e2b-348">没有之前和之后的事件。</span><span class="sxs-lookup"><span data-stu-id="98e2b-348">Don't have before and after events.</span></span> 
* <span data-ttu-id="98e2b-349">实现 `OnException` 或 `OnExceptionAsync`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-349">Implement `OnException` or `OnExceptionAsync`.</span></span> 
* <span data-ttu-id="98e2b-350">处理控制器创建、[模型绑定](../models/model-binding.md)、操作筛选器或操作方法中发生的未经处理的异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-350">Handle unhandled exceptions that occur in controller creation, [model binding](../models/model-binding.md), action filters, or action methods.</span></span> 
* <span data-ttu-id="98e2b-351">请不要捕获资源筛选器、结果筛选器或 MVC 结果执行中发生的异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-351">Do not catch exceptions that occur in Resource filters, Result filters, or MVC Result execution.</span></span>

<span data-ttu-id="98e2b-352">若要处理异常，请将 `ExceptionContext.ExceptionHandled` 属性设置为 true，或编写响应。</span><span class="sxs-lookup"><span data-stu-id="98e2b-352">To handle an exception, set the `ExceptionContext.ExceptionHandled` property to true or write a response.</span></span> <span data-ttu-id="98e2b-353">这将停止传播异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-353">This stops propagation of the exception.</span></span> <span data-ttu-id="98e2b-354">异常筛选器无法将异常转变为“成功”。</span><span class="sxs-lookup"><span data-stu-id="98e2b-354">An Exception filter can't turn an exception into a "success".</span></span> <span data-ttu-id="98e2b-355">只有操作筛选器才能执行该转变。</span><span class="sxs-lookup"><span data-stu-id="98e2b-355">Only an Action filter can do that.</span></span>

> [!NOTE]
> <span data-ttu-id="98e2b-356">在 ASP.NET Core 1.1 中，如果将 `ExceptionHandled` 设置为 true 并编写响应，则不会发送响应。</span><span class="sxs-lookup"><span data-stu-id="98e2b-356">In ASP.NET Core 1.1, the response isn't sent if you set `ExceptionHandled` to true **and** write a response.</span></span> <span data-ttu-id="98e2b-357">在这种情况下，ASP.NET Core 1.0 不发送响应，ASP.NET Core 1.1.2 则恢复为 1.0 的行为。</span><span class="sxs-lookup"><span data-stu-id="98e2b-357">In that scenario, ASP.NET Core 1.0 does send the response, and ASP.NET Core 1.1.2 will return to the 1.0 behavior.</span></span> <span data-ttu-id="98e2b-358">有关详细信息，请参阅 GitHub 存储库中的[问题编号 5594](https://github.com/aspnet/Mvc/issues/5594)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-358">For more information, see [issue #5594](https://github.com/aspnet/Mvc/issues/5594) in the GitHub repository.</span></span> 

<span data-ttu-id="98e2b-359">异常筛选器：</span><span class="sxs-lookup"><span data-stu-id="98e2b-359">Exception filters:</span></span>

* <span data-ttu-id="98e2b-360">非常适合捕获发生在 MVC 操作中的异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-360">Are good for trapping exceptions that occur within MVC actions.</span></span>
* <span data-ttu-id="98e2b-361">并不像错误处理中间件那么灵活。</span><span class="sxs-lookup"><span data-stu-id="98e2b-361">Are not as flexible as error handling middleware.</span></span> 

<span data-ttu-id="98e2b-362">建议使用中间件处理异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-362">Prefer middleware for exception handling.</span></span> <span data-ttu-id="98e2b-363">仅在需要根据所选 MVC 操作以不同方式执行错误处理时，才使用异常筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-363">Use exception filters only where you need to do error handling *differently* based on which MVC action was chosen.</span></span> <span data-ttu-id="98e2b-364">例如，应用可能具有用于 API 终结点和视图/HTML 的操作方法。</span><span class="sxs-lookup"><span data-stu-id="98e2b-364">For example, your app might have action methods for both API endpoints and for views/HTML.</span></span> <span data-ttu-id="98e2b-365">API 终结点可能返回 JSON 形式的错误信息，而基于视图的操作可能返回 HTML 形式的错误页。</span><span class="sxs-lookup"><span data-stu-id="98e2b-365">The API endpoints could return error information as JSON, while the view-based actions could return an error page as HTML.</span></span>

<span data-ttu-id="98e2b-366">`ExceptionFilterAttribute` 可以子类化。</span><span class="sxs-lookup"><span data-stu-id="98e2b-366">The `ExceptionFilterAttribute` can be subclassed.</span></span> 

## <a name="result-filters"></a><span data-ttu-id="98e2b-367">结果筛选器</span><span class="sxs-lookup"><span data-stu-id="98e2b-367">Result filters</span></span>

* <span data-ttu-id="98e2b-368">实现 `IResultFilter` 或 `IAsyncResultFilter` 接口。</span><span class="sxs-lookup"><span data-stu-id="98e2b-368">Implement either the `IResultFilter` or `IAsyncResultFilter` interface.</span></span>
* <span data-ttu-id="98e2b-369">它们的执行围绕着操作结果的执行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-369">Their execution surrounds the execution of action results.</span></span> 

<span data-ttu-id="98e2b-370">下面是一个添加 HTTP 标头的结果筛选器示例。</span><span class="sxs-lookup"><span data-stu-id="98e2b-370">Here's an example of a Result filter that adds an HTTP header.</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LoggingAddHeaderFilter.cs?name=snippet_ResultFilter)]

<span data-ttu-id="98e2b-371">要执行的结果类型取决于所执行的操作。</span><span class="sxs-lookup"><span data-stu-id="98e2b-371">The kind of result being executed depends on the action in question.</span></span> <span data-ttu-id="98e2b-372">返回视图的 MVC 操作会将所有 Razor 处理作为要执行的 `ViewResult` 的一部分。</span><span class="sxs-lookup"><span data-stu-id="98e2b-372">An MVC action returning a view would include all razor processing as part of the `ViewResult` being executed.</span></span> <span data-ttu-id="98e2b-373">API 方法可能会将某些序列化操作作为结果执行的一部分。</span><span class="sxs-lookup"><span data-stu-id="98e2b-373">An API method might perform some serialization as part of the execution of the result.</span></span> <span data-ttu-id="98e2b-374">详细了解[操作结果](actions.md)</span><span class="sxs-lookup"><span data-stu-id="98e2b-374">Learn more about [action results](actions.md)</span></span>

<span data-ttu-id="98e2b-375">当操作或操作筛选器生成操作结果时，仅针对成功的结果执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-375">Result filters are only executed for successful results - when the action or action filters produce an action result.</span></span> <span data-ttu-id="98e2b-376">当异常筛选器处理异常时，不执行结果筛选器。</span><span class="sxs-lookup"><span data-stu-id="98e2b-376">Result filters are not executed when exception filters handle an exception.</span></span>

<span data-ttu-id="98e2b-377">`OnResultExecuting` 方法可以将 `ResultExecutingContext.Cancel` 设置为 true，使操作结果和后续结果筛选器的执行短路。</span><span class="sxs-lookup"><span data-stu-id="98e2b-377">The `OnResultExecuting` method can short-circuit execution of the action result and subsequent result filters by setting `ResultExecutingContext.Cancel` to true.</span></span> <span data-ttu-id="98e2b-378">设置短路时，通常应写入响应对象，以免生成空响应。</span><span class="sxs-lookup"><span data-stu-id="98e2b-378">You should generally write to the response object when short-circuiting to avoid generating an empty response.</span></span> <span data-ttu-id="98e2b-379">如果引发异常，则会导致：</span><span class="sxs-lookup"><span data-stu-id="98e2b-379">Throwing an exception will:</span></span>

* <span data-ttu-id="98e2b-380">阻止操作结果和后续筛选器的执行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-380">Prevent execution of the action result and subsequent filters.</span></span>
* <span data-ttu-id="98e2b-381">结果被视为失败而不是成功。</span><span class="sxs-lookup"><span data-stu-id="98e2b-381">Be treated as a failure instead of a successful result.</span></span>

<span data-ttu-id="98e2b-382">当 `OnResultExecuted` 方法运行时，响应可能已发送到客户端，而且不能再更改（除非引发了异常）。</span><span class="sxs-lookup"><span data-stu-id="98e2b-382">When the `OnResultExecuted` method runs, the response has likely been sent to the client and cannot be changed further (unless an exception was thrown).</span></span> <span data-ttu-id="98e2b-383">如果操作结果执行已被另一个筛选器设置短路，则 `ResultExecutedContext.Canceled` 设置为 true。</span><span class="sxs-lookup"><span data-stu-id="98e2b-383">`ResultExecutedContext.Canceled` will be set to true if the action result execution was short-circuited by another filter.</span></span>

<span data-ttu-id="98e2b-384">如果操作结果或后续结果筛选器引发了异常，则 `ResultExecutedContext.Exception` 设置为非 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="98e2b-384">`ResultExecutedContext.Exception` will be set to a non-null value if the action result or a subsequent result filter threw an exception.</span></span> <span data-ttu-id="98e2b-385">将 `Exception` 设置为 NULL 可有效地“处理”异常，并防止 MVC 在管道的后续阶段重新引发该异常。</span><span class="sxs-lookup"><span data-stu-id="98e2b-385">Setting `Exception` to null effectively 'handles' an exception and prevents the exception from being rethrown by MVC later in the pipeline.</span></span> <span data-ttu-id="98e2b-386">在处理结果筛选器中的异常时，可能无法向响应写入任何数据。</span><span class="sxs-lookup"><span data-stu-id="98e2b-386">When you're handling an exception in a result filter, you might not be able to write any data to the response.</span></span> <span data-ttu-id="98e2b-387">如果操作结果在其执行过程中引发异常，并且标头已刷新到客户端，则没有任何可靠的机制可用于发送失败代码。</span><span class="sxs-lookup"><span data-stu-id="98e2b-387">If the action result throws partway through its execution, and the headers have already been flushed to the client, there's no reliable mechanism to send a failure code.</span></span>

<span data-ttu-id="98e2b-388">对于 `IAsyncResultFilter`，通过调用 `ResultExecutionDelegate` 上的 `await next` 可执行所有后续结果筛选器和操作结果。</span><span class="sxs-lookup"><span data-stu-id="98e2b-388">For an `IAsyncResultFilter` a call to `await next` on the `ResultExecutionDelegate` executes any subsequent result filters and the action result.</span></span> <span data-ttu-id="98e2b-389">若要设置短路，可将 `ResultExecutingContext.Cancel` 设置为 true，并且不调用 `ResultExectionDelegate`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-389">To short-circuit, set `ResultExecutingContext.Cancel` to true and don't call the `ResultExectionDelegate`.</span></span>

<span data-ttu-id="98e2b-390">该框架提供一个可子类化的抽象 `ResultFilterAttribute`。</span><span class="sxs-lookup"><span data-stu-id="98e2b-390">The framework provides an abstract `ResultFilterAttribute` that you can subclass.</span></span> <span data-ttu-id="98e2b-391">前面所示的 [AddHeaderAttribute](#add-header-attribute) 类是一种结果筛选器属性。</span><span class="sxs-lookup"><span data-stu-id="98e2b-391">The [AddHeaderAttribute](#add-header-attribute) class shown earlier is an example of a result filter attribute.</span></span>

## <a name="using-middleware-in-the-filter-pipeline"></a><span data-ttu-id="98e2b-392">在筛选器管道中使用中间件</span><span class="sxs-lookup"><span data-stu-id="98e2b-392">Using middleware in the filter pipeline</span></span>

<span data-ttu-id="98e2b-393">资源筛选器的工作方式与[中间件](xref:fundamentals/middleware/index)类似，即涵盖管道中的所有后续执行。</span><span class="sxs-lookup"><span data-stu-id="98e2b-393">Resource filters work like [middleware](xref:fundamentals/middleware/index) in that they surround the execution of everything that comes later in the pipeline.</span></span> <span data-ttu-id="98e2b-394">但筛选器又不同于中间件，它们是 MVC 的一部分，这意味着它们有权访问 MVC 上下文和构造。</span><span class="sxs-lookup"><span data-stu-id="98e2b-394">But filters differ from middleware in that they're part of MVC, which means that they have access to MVC context and constructs.</span></span>

<span data-ttu-id="98e2b-395">在 ASP.NET Core 1.1 中，可以在筛选器管道中使用中间件。</span><span class="sxs-lookup"><span data-stu-id="98e2b-395">In ASP.NET Core 1.1, you can use middleware in the filter pipeline.</span></span> <span data-ttu-id="98e2b-396">如果有一个中间件组件，该组件需要访问 MVC 路由数据，或者只能针对特定控制器或操作运行，则可能需要这样做。</span><span class="sxs-lookup"><span data-stu-id="98e2b-396">You might want to do that if you have a middleware component that needs access to MVC route data, or one that should run only for certain controllers or actions.</span></span>

<span data-ttu-id="98e2b-397">若要将中间件用作筛选器，可创建一个具有 `Configure` 方法的类型，该方法可指定要注入到筛选器管道的中间件。</span><span class="sxs-lookup"><span data-stu-id="98e2b-397">To use middleware as a filter, create a type with a `Configure` method that specifies the middleware that you want to inject into the filter pipeline.</span></span> <span data-ttu-id="98e2b-398">下面的示例使用本地化中间件为请求建立当前区域性：</span><span class="sxs-lookup"><span data-stu-id="98e2b-398">Here's an example that uses the localization middleware to establish the current culture for a request:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Filters/LocalizationPipeline.cs?name=snippet_MiddlewareFilter&highlight=3,21)]

<span data-ttu-id="98e2b-399">然后，可以使用 `MiddlewareFilterAttribute` 为所选控制器或操作或者在全局范围内运行中间件：</span><span class="sxs-lookup"><span data-stu-id="98e2b-399">You can then use the `MiddlewareFilterAttribute` to run the middleware for a selected controller or action or globally:</span></span>

[!code-csharp[](./filters/sample/src/FiltersSample/Controllers/HomeController.cs?name=snippet_MiddlewareFilter&highlight=2)]

<span data-ttu-id="98e2b-400">中间件筛选器与资源筛选器在筛选器管道的相同阶段运行，即，在模型绑定之前以及管道的其余阶段之后。</span><span class="sxs-lookup"><span data-stu-id="98e2b-400">Middleware filters run at the same stage of the filter pipeline as Resource filters, before model binding and after the rest of the pipeline.</span></span>

## <a name="next-actions"></a><span data-ttu-id="98e2b-401">后续操作</span><span class="sxs-lookup"><span data-stu-id="98e2b-401">Next actions</span></span>

<span data-ttu-id="98e2b-402">若要尝试使用筛选器，请[下载、测试并修改该示例](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample)。</span><span class="sxs-lookup"><span data-stu-id="98e2b-402">To experiment with filters, [download, test and modify the sample](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/filters/sample).</span></span>
