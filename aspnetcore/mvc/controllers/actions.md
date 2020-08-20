---
title: 在 ASP.NET Core MVC 中使用控制器处理请求
author: ardalis
description: ''
ms.author: riande
ms.date: 12/05/2019
no-loc:
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
uid: mvc/controllers/actions
ms.openlocfilehash: 9542a7c0fd16c00f46ee69c5873878a7c70ef626
ms.sourcegitcommit: 65add17f74a29a647d812b04517e46cbc78258f9
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/19/2020
ms.locfileid: "88630323"
---
# <a name="handle-requests-with-controllers-in-aspnet-core-mvc"></a><span data-ttu-id="6b412-102">在 ASP.NET Core MVC 中使用控制器处理请求</span><span class="sxs-lookup"><span data-stu-id="6b412-102">Handle requests with controllers in ASP.NET Core MVC</span></span>

<span data-ttu-id="6b412-103">作者：[Steve Smith](https://ardalis.com/) 和 [Scott Addie](https://github.com/scottaddie)</span><span class="sxs-lookup"><span data-stu-id="6b412-103">By [Steve Smith](https://ardalis.com/) and [Scott Addie](https://github.com/scottaddie)</span></span>

<span data-ttu-id="6b412-104">控制器、操作和操作结果是开发人员如何使用 ASP.NET Core MVC 生成应用的一个基本组成部分。</span><span class="sxs-lookup"><span data-stu-id="6b412-104">Controllers, actions, and action results are a fundamental part of how developers build apps using ASP.NET Core MVC.</span></span>

## <a name="what-is-a-controller"></a><span data-ttu-id="6b412-105">什么是控制器？</span><span class="sxs-lookup"><span data-stu-id="6b412-105">What is a Controller?</span></span>

<span data-ttu-id="6b412-106">控制器用于对一组操作进行定义和分组。</span><span class="sxs-lookup"><span data-stu-id="6b412-106">A controller is used to define and group a set of actions.</span></span> <span data-ttu-id="6b412-107">操作（或操作方法\*\*）是控制器上一种用来处理请求的方法。</span><span class="sxs-lookup"><span data-stu-id="6b412-107">An action (or *action method*) is a method on a controller which handles requests.</span></span> <span data-ttu-id="6b412-108">控制器按逻辑将类似的操作集合到一起。</span><span class="sxs-lookup"><span data-stu-id="6b412-108">Controllers logically group similar actions together.</span></span> <span data-ttu-id="6b412-109">通过这种操作的聚合，可以共同应用路由、缓存和授权等通用规则集。</span><span class="sxs-lookup"><span data-stu-id="6b412-109">This aggregation of actions allows common sets of rules, such as routing, caching, and authorization, to be applied collectively.</span></span> <span data-ttu-id="6b412-110">请求通过[路由](xref:mvc/controllers/routing)映射到操作。</span><span class="sxs-lookup"><span data-stu-id="6b412-110">Requests are mapped to actions through [routing](xref:mvc/controllers/routing).</span></span>

<span data-ttu-id="6b412-111">根据惯例，控制器类：</span><span class="sxs-lookup"><span data-stu-id="6b412-111">By convention, controller classes:</span></span>

* <span data-ttu-id="6b412-112">驻留在项目的根级 *控制器* 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="6b412-112">Reside in the project's root-level *Controllers* folder.</span></span>
* <span data-ttu-id="6b412-113">继承自 `Microsoft.AspNetCore.Mvc.Controller`。</span><span class="sxs-lookup"><span data-stu-id="6b412-113">Inherit from `Microsoft.AspNetCore.Mvc.Controller`.</span></span>

<span data-ttu-id="6b412-114">控制器是一个可实例化的类，其中下列条件至少某一个为 true：</span><span class="sxs-lookup"><span data-stu-id="6b412-114">A controller is an instantiable class in which at least one of the following conditions is true:</span></span>

* <span data-ttu-id="6b412-115">类名称带有 `Controller` 后缀。</span><span class="sxs-lookup"><span data-stu-id="6b412-115">The class name is suffixed with `Controller`.</span></span>
* <span data-ttu-id="6b412-116">该类继承自带有 `Controller` 后缀的类。</span><span class="sxs-lookup"><span data-stu-id="6b412-116">The class inherits from a class whose name is suffixed with `Controller`.</span></span>
* <span data-ttu-id="6b412-117">`[Controller]` 特性应用于该类。</span><span class="sxs-lookup"><span data-stu-id="6b412-117">The `[Controller]` attribute is applied to the class.</span></span>

<span data-ttu-id="6b412-118">控制器类不可含有关联的 `[NonController]` 属性。</span><span class="sxs-lookup"><span data-stu-id="6b412-118">A controller class must not have an associated `[NonController]` attribute.</span></span>

<span data-ttu-id="6b412-119">控制器应遵循 [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）。</span><span class="sxs-lookup"><span data-stu-id="6b412-119">Controllers should follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span> <span data-ttu-id="6b412-120">以下几种方法可以实现此原则。</span><span class="sxs-lookup"><span data-stu-id="6b412-120">There are a couple of approaches to implementing this principle.</span></span> <span data-ttu-id="6b412-121">如果多个控制器操作需要相同的服务，请考虑使用[构造函数注入](xref:mvc/controllers/dependency-injection#constructor-injection)来请求这些依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6b412-121">If multiple controller actions require the same service, consider using [constructor injection](xref:mvc/controllers/dependency-injection#constructor-injection) to request those dependencies.</span></span> <span data-ttu-id="6b412-122">如果该服务仅需要一个操作方法，请考虑使用[操作注入](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices)来请求依赖关系。</span><span class="sxs-lookup"><span data-stu-id="6b412-122">If the service is needed by only a single action method, consider using [Action Injection](xref:mvc/controllers/dependency-injection#action-injection-with-fromservices) to request the dependency.</span></span>

<span data-ttu-id="6b412-123">在“模型-视图-控制器”模式中，控制器负责请求的初始处理和模型的实例化操作\*\*\*\*\*\*\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b412-123">Within the **M**odel-**V**iew-**C**ontroller pattern, a controller is responsible for the initial processing of the request and instantiation of the model.</span></span> <span data-ttu-id="6b412-124">通常情况下，应在模型中执行业务决策。</span><span class="sxs-lookup"><span data-stu-id="6b412-124">Generally, business decisions should be performed within the model.</span></span>

<span data-ttu-id="6b412-125">控制器获取模型处理的结果（如果有），并返回正确的视图及其关联的视图数据或 API 调用的结果。</span><span class="sxs-lookup"><span data-stu-id="6b412-125">The controller takes the result of the model's processing (if any) and returns either the proper view and its associated view data or the result of the API call.</span></span> <span data-ttu-id="6b412-126">请参阅 [ASP.NET Core MVC 概述](xref:mvc/overview)以及 [ASP.NET Core MVC 和 Visual Studio 入门](xref:tutorials/first-mvc-app/start-mvc)了解详细信息。</span><span class="sxs-lookup"><span data-stu-id="6b412-126">Learn more at [Overview of ASP.NET Core MVC](xref:mvc/overview) and [Get started with ASP.NET Core MVC and Visual Studio](xref:tutorials/first-mvc-app/start-mvc).</span></span>

<span data-ttu-id="6b412-127">控制器是一个 UI 级别\*\* 的抽象。</span><span class="sxs-lookup"><span data-stu-id="6b412-127">The controller is a *UI-level* abstraction.</span></span> <span data-ttu-id="6b412-128">它的职责是确保请求的数据有效，并选择应当返回的视图（或 API 的结果）。</span><span class="sxs-lookup"><span data-stu-id="6b412-128">Its responsibilities are to ensure request data is valid and to choose which view (or result for an API) should be returned.</span></span> <span data-ttu-id="6b412-129">在构造良好的应用程序中，它不会直接包括数据访问或业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="6b412-129">In well-factored apps, it doesn't directly include data access or business logic.</span></span> <span data-ttu-id="6b412-130">相反，控制器会委托给处理这些责任的服务。</span><span class="sxs-lookup"><span data-stu-id="6b412-130">Instead, the controller delegates to services handling these responsibilities.</span></span>

## <a name="defining-actions"></a><span data-ttu-id="6b412-131">定义操作</span><span class="sxs-lookup"><span data-stu-id="6b412-131">Defining Actions</span></span>

<span data-ttu-id="6b412-132">控制器上的公共方法（除了那些带有 `[NonAction]` 属性的方法）均为操作。</span><span class="sxs-lookup"><span data-stu-id="6b412-132">Public methods on a controller, except those with the `[NonAction]` attribute, are actions.</span></span> <span data-ttu-id="6b412-133">操作上的参数会绑定到请求数据，并使用[模型绑定](xref:mvc/models/model-binding)进行验证。</span><span class="sxs-lookup"><span data-stu-id="6b412-133">Parameters on actions are bound to request data and are validated using [model binding](xref:mvc/models/model-binding).</span></span> <span data-ttu-id="6b412-134">所有模型绑定的内容都会执行模型验证。</span><span class="sxs-lookup"><span data-stu-id="6b412-134">Model validation occurs for everything that's model-bound.</span></span> <span data-ttu-id="6b412-135">`ModelState.IsValid` 属性值指示模型绑定和验证是否成功。</span><span class="sxs-lookup"><span data-stu-id="6b412-135">The `ModelState.IsValid` property value indicates whether model binding and validation succeeded.</span></span>

<span data-ttu-id="6b412-136">操作方法应包含用于将请求映射到某个业务关注点的逻辑。</span><span class="sxs-lookup"><span data-stu-id="6b412-136">Action methods should contain logic for mapping a request to a business concern.</span></span> <span data-ttu-id="6b412-137">业务关注点通常应当表示为控制器通过[依赖关系注入](xref:mvc/controllers/dependency-injection)来访问的服务。</span><span class="sxs-lookup"><span data-stu-id="6b412-137">Business concerns should typically be represented as services that the controller accesses through [dependency injection](xref:mvc/controllers/dependency-injection).</span></span> <span data-ttu-id="6b412-138">然后，操作将业务操作的结果映射到应用程序状态。</span><span class="sxs-lookup"><span data-stu-id="6b412-138">Actions then map the result of the business action to an application state.</span></span>

<span data-ttu-id="6b412-139">操作可以返回任何内容，但是经常返回生成响应的 `IActionResult`（或异步方法的 `Task<IActionResult>`）的实例。</span><span class="sxs-lookup"><span data-stu-id="6b412-139">Actions can return anything, but frequently return an instance of `IActionResult` (or `Task<IActionResult>` for async methods) that produces a response.</span></span> <span data-ttu-id="6b412-140">操作方法负责选择响应的类型\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b412-140">The action method is responsible for choosing *what kind of response*.</span></span> <span data-ttu-id="6b412-141">操作结果会做出响应\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b412-141">The action result *does the responding*.</span></span>

### <a name="controller-helper-methods"></a><span data-ttu-id="6b412-142">控制器帮助程序方法</span><span class="sxs-lookup"><span data-stu-id="6b412-142">Controller Helper Methods</span></span>

<span data-ttu-id="6b412-143">控制器通常继承自[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)（尽管没有要求）。</span><span class="sxs-lookup"><span data-stu-id="6b412-143">Controllers usually inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), although this isn't required.</span></span> <span data-ttu-id="6b412-144">派生自 `Controller` 会提供对三个帮助程序方法类别的访问：</span><span class="sxs-lookup"><span data-stu-id="6b412-144">Deriving from `Controller` provides access to three categories of helper methods:</span></span>

#### <a name="1-methods-resulting-in-an-empty-response-body"></a><span data-ttu-id="6b412-145">1. 导致空响应正文的方法</span><span class="sxs-lookup"><span data-stu-id="6b412-145">1. Methods resulting in an empty response body</span></span>

<span data-ttu-id="6b412-146">没有包含 `Content-Type` HTTP 响应标头，因为响应正文缺少要描述的内容。</span><span class="sxs-lookup"><span data-stu-id="6b412-146">No `Content-Type` HTTP response header is included, since the response body lacks content to describe.</span></span>

<span data-ttu-id="6b412-147">该类别中有两种结果类型：重定向和 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="6b412-147">There are two result types within this category: Redirect and HTTP Status Code.</span></span>

* <span data-ttu-id="6b412-148">**HTTP 状态代码**</span><span class="sxs-lookup"><span data-stu-id="6b412-148">**HTTP Status Code**</span></span>

    <span data-ttu-id="6b412-149">此类型返回 HTTP 状态代码。</span><span class="sxs-lookup"><span data-stu-id="6b412-149">This type returns an HTTP status code.</span></span> <span data-ttu-id="6b412-150">此类型的几种帮助程序方法是 `BadRequest`、`NotFound` 和 `Ok`。</span><span class="sxs-lookup"><span data-stu-id="6b412-150">A couple of helper methods of this type are `BadRequest`, `NotFound`, and `Ok`.</span></span> <span data-ttu-id="6b412-151">例如，`return BadRequest();` 执行时生成 400 状态代码。</span><span class="sxs-lookup"><span data-stu-id="6b412-151">For example, `return BadRequest();` produces a 400 status code when executed.</span></span> <span data-ttu-id="6b412-152">重载 `BadRequest``NotFound` 和 `Ok` 等方法时，它们不再符合 HTTP 状态代码响应方的资格，因为正在进行内容协商。</span><span class="sxs-lookup"><span data-stu-id="6b412-152">When methods such as `BadRequest`, `NotFound`, and `Ok` are overloaded, they no longer qualify as HTTP Status Code responders, since content negotiation is taking place.</span></span>

* <span data-ttu-id="6b412-153">**重定向**</span><span class="sxs-lookup"><span data-stu-id="6b412-153">**Redirect**</span></span>

    <span data-ttu-id="6b412-154">此类型（使用 `Redirect``LocalRedirect``RedirectToAction` 或 `RedirectToRoute`）返回一个到操作或目标的重定向。</span><span class="sxs-lookup"><span data-stu-id="6b412-154">This type returns a redirect to an action or destination (using `Redirect`, `LocalRedirect`, `RedirectToAction`, or `RedirectToRoute`).</span></span> <span data-ttu-id="6b412-155">例如，`return RedirectToAction("Complete", new {id = 123});` 重定向到 `Complete`，传递一个匿名对象。</span><span class="sxs-lookup"><span data-stu-id="6b412-155">For example, `return RedirectToAction("Complete", new {id = 123});` redirects to `Complete`, passing an anonymous object.</span></span>

    <span data-ttu-id="6b412-156">重定向结果类型与 HTTP 状态代码类型的不同之处主要在于 `Location` HTTP 响应标头的添加。</span><span class="sxs-lookup"><span data-stu-id="6b412-156">The Redirect result type differs from the HTTP Status Code type primarily in the addition of a `Location` HTTP response header.</span></span>

#### <a name="2-methods-resulting-in-a-non-empty-response-body-with-a-predefined-content-type"></a><span data-ttu-id="6b412-157">2. 导致具有预定义内容类型的非空响应正文的方法</span><span class="sxs-lookup"><span data-stu-id="6b412-157">2. Methods resulting in a non-empty response body with a predefined content type</span></span>

<span data-ttu-id="6b412-158">此类别中的大多数帮助程序方法都包含一个 `ContentType` 属性，通过它可以设置 `Content-Type` 响应标头来描述响应正文。</span><span class="sxs-lookup"><span data-stu-id="6b412-158">Most helper methods in this category include a `ContentType` property, allowing you to set the `Content-Type` response header to describe the response body.</span></span>

<span data-ttu-id="6b412-159">此类别中有两种结果类型：[视图](xref:mvc/views/overview)和[已格式化的响应](xref:web-api/advanced/formatting)。</span><span class="sxs-lookup"><span data-stu-id="6b412-159">There are two result types within this category: [View](xref:mvc/views/overview) and [Formatted Response](xref:web-api/advanced/formatting).</span></span>

* <span data-ttu-id="6b412-160">**视图**</span><span class="sxs-lookup"><span data-stu-id="6b412-160">**View**</span></span>

    <span data-ttu-id="6b412-161">此类型返回一个使用模型呈现 HTML 的视图。</span><span class="sxs-lookup"><span data-stu-id="6b412-161">This type returns a view which uses a model to render HTML.</span></span> <span data-ttu-id="6b412-162">例如，`return View(customer);` 将模型传递给视图以进行数据绑定。</span><span class="sxs-lookup"><span data-stu-id="6b412-162">For example, `return View(customer);` passes a model to the view for data-binding.</span></span>

* <span data-ttu-id="6b412-163">**已格式化的响应**</span><span class="sxs-lookup"><span data-stu-id="6b412-163">**Formatted Response**</span></span>

    <span data-ttu-id="6b412-164">此类型返回 JSON 或类似的数据交换格式，从而以特定方式表示某个对象。</span><span class="sxs-lookup"><span data-stu-id="6b412-164">This type returns JSON or a similar data exchange format to represent an object in a specific manner.</span></span> <span data-ttu-id="6b412-165">例如，`return Json(customer);` 将提供的对象串行化为 JSON 格式。</span><span class="sxs-lookup"><span data-stu-id="6b412-165">For example, `return Json(customer);` serializes the provided object into JSON format.</span></span>
    
    <span data-ttu-id="6b412-166">此类型的其他常见方法包括 `File` 和 `PhysicalFile`。</span><span class="sxs-lookup"><span data-stu-id="6b412-166">Other common methods of this type include `File` and `PhysicalFile`.</span></span> <span data-ttu-id="6b412-167">例如，`return PhysicalFile(customerFilePath, "text/xml");` 返回 [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult)。</span><span class="sxs-lookup"><span data-stu-id="6b412-167">For example, `return PhysicalFile(customerFilePath, "text/xml");` returns [PhysicalFileResult](/dotnet/api/microsoft.aspnetcore.mvc.physicalfileresult).</span></span>

#### <a name="3-methods-resulting-in-a-non-empty-response-body-formatted-in-a-content-type-negotiated-with-the-client"></a><span data-ttu-id="6b412-168">3. 导致在与客户端协商的内容类型中设置非空响应正文的方法</span><span class="sxs-lookup"><span data-stu-id="6b412-168">3. Methods resulting in a non-empty response body formatted in a content type negotiated with the client</span></span>

<span data-ttu-id="6b412-169">此类别更为熟知的说法是“内容协商”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b412-169">This category is better known as **Content Negotiation**.</span></span> <span data-ttu-id="6b412-170">每当操作返回 [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) 类型或除 [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) 实现之外的任何实现时，会应用[内容协商](xref:web-api/advanced/formatting#content-negotiation)。</span><span class="sxs-lookup"><span data-stu-id="6b412-170">[Content negotiation](xref:web-api/advanced/formatting#content-negotiation) applies whenever an action returns an [ObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.objectresult) type or something other than an [IActionResult](/dotnet/api/microsoft.aspnetcore.mvc.iactionresult) implementation.</span></span> <span data-ttu-id="6b412-171">返回非 `IActionResult` 实现（例如 `object`）的操作也会返回已格式化的响应。</span><span class="sxs-lookup"><span data-stu-id="6b412-171">An action that returns a non-`IActionResult` implementation (for example, `object`) also returns a Formatted Response.</span></span>

<span data-ttu-id="6b412-172">此类型的一些帮助程序方法包括 `BadRequest`、`CreatedAtRoute` 和 `Ok`。</span><span class="sxs-lookup"><span data-stu-id="6b412-172">Some helper methods of this type include `BadRequest`, `CreatedAtRoute`, and `Ok`.</span></span> <span data-ttu-id="6b412-173">这些方法的示例分别为 `return BadRequest(modelState);`、`return CreatedAtRoute("routename", values, newobject);` 和 `return Ok(value);`。</span><span class="sxs-lookup"><span data-stu-id="6b412-173">Examples of these methods include `return BadRequest(modelState);`, `return CreatedAtRoute("routename", values, newobject);`, and `return Ok(value);`, respectively.</span></span> <span data-ttu-id="6b412-174">请注意，`BadRequest` 和 `Ok` 仅在传递了值的时候才执行内容协商；在没有传递值的情况下，它们充当 HTTP 状态码结果类型。</span><span class="sxs-lookup"><span data-stu-id="6b412-174">Note that `BadRequest` and `Ok` perform content negotiation only when passed a value; without being passed a value, they instead serve as HTTP Status Code result types.</span></span> <span data-ttu-id="6b412-175">另一方面，`CreatedAtRoute` 方法始终执行内容协商，因为它的重载均要求传递一个值。</span><span class="sxs-lookup"><span data-stu-id="6b412-175">The `CreatedAtRoute` method, on the other hand, always performs content negotiation since its overloads all require that a value be passed.</span></span>

### <a name="cross-cutting-concerns"></a><span data-ttu-id="6b412-176">横切关注点</span><span class="sxs-lookup"><span data-stu-id="6b412-176">Cross-Cutting Concerns</span></span>

<span data-ttu-id="6b412-177">应用程序通常会共享其部分工作流程。</span><span class="sxs-lookup"><span data-stu-id="6b412-177">Applications typically share parts of their workflow.</span></span> <span data-ttu-id="6b412-178">示例包括需要身份验证才能访问购物车的应用，或者在某些页面上缓存数据的应用。</span><span class="sxs-lookup"><span data-stu-id="6b412-178">Examples include an app that requires authentication to access the shopping cart, or an app that caches data on some pages.</span></span> <span data-ttu-id="6b412-179">要在某个操作方法之前或之后执行逻辑，请使用筛选器\*\*。</span><span class="sxs-lookup"><span data-stu-id="6b412-179">To perform logic before or after an action method, use a *filter*.</span></span> <span data-ttu-id="6b412-180">对横切关注点使用[筛选器](xref:mvc/controllers/filters)可以减少重复。</span><span class="sxs-lookup"><span data-stu-id="6b412-180">Using [Filters](xref:mvc/controllers/filters) on cross-cutting concerns can reduce duplication.</span></span>

<span data-ttu-id="6b412-181">可在控制器或操作级别上应用大多数筛选器属性（例如 `[Authorize]`），具体取决于所需的粒度级别。</span><span class="sxs-lookup"><span data-stu-id="6b412-181">Most filter attributes, such as `[Authorize]`, can be applied at the controller or action level depending upon the desired level of granularity.</span></span>

<span data-ttu-id="6b412-182">错误处理和响应缓存通常是横切关注点：</span><span class="sxs-lookup"><span data-stu-id="6b412-182">Error handling and response caching are often cross-cutting concerns:</span></span>
* [<span data-ttu-id="6b412-183">处理错误</span><span class="sxs-lookup"><span data-stu-id="6b412-183">Handle errors</span></span>](xref:mvc/controllers/filters#exception-filters)
* [<span data-ttu-id="6b412-184">响应缓存</span><span class="sxs-lookup"><span data-stu-id="6b412-184">Response Caching</span></span>](xref:performance/caching/response)

<span data-ttu-id="6b412-185">使用筛选器或自定义[中间件](xref:fundamentals/middleware/index)可处理许多横切关注点。</span><span class="sxs-lookup"><span data-stu-id="6b412-185">Many cross-cutting concerns can be handled using filters or custom [middleware](xref:fundamentals/middleware/index).</span></span>
