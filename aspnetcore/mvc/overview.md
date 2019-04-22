---
title: ASP.NET Core MVC 概述
author: ardalis
description: 了解 ASP.NET Core MVC 这一丰富框架如何使用“模型-视图-控制器”设计模式构建 Web 应用和 API。
ms.author: riande
ms.date: 01/08/2018
uid: mvc/overview
ms.openlocfilehash: eb636616ab952f506993ae5f4692e5e966bb20f4
ms.sourcegitcommit: 78339e9891c8676db01a6e81e9cb0cdaa280162f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/17/2019
ms.locfileid: "59516295"
---
# <a name="overview-of-aspnet-core-mvc"></a><span data-ttu-id="bf7d2-103">ASP.NET Core MVC 概述</span><span class="sxs-lookup"><span data-stu-id="bf7d2-103">Overview of ASP.NET Core MVC</span></span>

<span data-ttu-id="bf7d2-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="bf7d2-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="bf7d2-105">ASP.NET Core MVC 是使用“模型-视图-控制器”设计模式构建 Web 应用和 API 的丰富框架。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-105">ASP.NET Core MVC is a rich framework for building web apps and APIs using the Model-View-Controller design pattern.</span></span>

## <a name="what-is-the-mvc-pattern"></a><span data-ttu-id="bf7d2-106">什么是 MVC 模式？</span><span class="sxs-lookup"><span data-stu-id="bf7d2-106">What is the MVC pattern?</span></span>

<span data-ttu-id="bf7d2-107">模型-视图-控制器 (MVC) 架构模式将应用程序分为三个主要组成部分：模型、视图和控制器。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-107">The Model-View-Controller (MVC) architectural pattern separates an application into three main groups of components: Models, Views, and Controllers.</span></span> <span data-ttu-id="bf7d2-108">此模式有助于实现[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-108">This pattern helps to achieve [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns).</span></span> <span data-ttu-id="bf7d2-109">使用此模式，用户请求被路由到控制器，后者负责使用模型来执行用户操作和/或检索查询结果。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-109">Using this pattern, user requests are routed to a Controller which is responsible for working with the Model to perform user actions and/or retrieve results of queries.</span></span> <span data-ttu-id="bf7d2-110">控制器选择要显示给用户的视图，并为其提供所需的任何模型数据。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-110">The Controller chooses the View to display to the user, and provides it with any Model data it requires.</span></span>

<span data-ttu-id="bf7d2-111">下图显示 3 个主要组件及其相互引用关系：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-111">The following diagram shows the three main components and which ones reference the others:</span></span>

![MVC 模式](overview/_static/mvc.png)

<span data-ttu-id="bf7d2-113">这种责任划分有助于根据复杂性缩放应用程序，因为这更易于编码、调试和测试包含单一作业的某个组成部分（模型、视图或控制器）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-113">This delineation of responsibilities helps you scale the application in terms of complexity because it's easier to code, debug, and test something (model, view, or controller) that has a single job.</span></span> <span data-ttu-id="bf7d2-114">但这会加大更新、测试和调试代码的难度，该代码在这 3 个领域的两个或多个领域间存在依赖关系。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-114">It's more difficult to update, test, and debug code that has dependencies spread across two or more of these three areas.</span></span> <span data-ttu-id="bf7d2-115">例如，用户界面逻辑的变更频率往往高于业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-115">For example, user interface logic tends to change more frequently than business logic.</span></span> <span data-ttu-id="bf7d2-116">如果将表示代码和业务逻辑组合在单个对象中，则每次更改用户界面时都必须修改包含业务逻辑的对象。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-116">If presentation code and business logic are combined in a single object, an object containing business logic must be modified every time the user interface is changed.</span></span> <span data-ttu-id="bf7d2-117">这常常会引发错误，并且需要在每次进行细微的用户界面更改后重新测试业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-117">This often introduces errors and requires the retesting of business logic after every minimal user interface change.</span></span>

> [!NOTE]
> <span data-ttu-id="bf7d2-118">视图和控制器均依赖于模型。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-118">Both the view and the controller depend on the model.</span></span> <span data-ttu-id="bf7d2-119">但是，模型既不依赖于视图，也不依赖于控制器。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-119">However, the model depends on neither the view nor the controller.</span></span> <span data-ttu-id="bf7d2-120">这是分离的一个关键优势。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-120">This is one of the key benefits of the separation.</span></span> <span data-ttu-id="bf7d2-121">这种分离允许模型独立于可视化展示进行构建和测试。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-121">This separation allows the model to be built and tested independent of the visual presentation.</span></span>

### <a name="model-responsibilities"></a><span data-ttu-id="bf7d2-122">模型责任</span><span class="sxs-lookup"><span data-stu-id="bf7d2-122">Model Responsibilities</span></span>

<span data-ttu-id="bf7d2-123">MVC 应用程序的模型 (M) 表示应用程序和任何应由其执行的业务逻辑或操作的状态。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-123">The Model in an MVC application represents the state of the application and any business logic or operations that should be performed by it.</span></span> <span data-ttu-id="bf7d2-124">业务逻辑应与保持应用程序状态的任何实现逻辑一起封装在模型中。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-124">Business logic should be encapsulated in the model, along with any implementation logic for persisting the state of the application.</span></span> <span data-ttu-id="bf7d2-125">强类型视图通常使用 ViewModel 类型，旨在包含要在该视图上显示的数据。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-125">Strongly-typed views typically use ViewModel types designed to contain the data to display on that view.</span></span> <span data-ttu-id="bf7d2-126">控制器从模型创建并填充 ViewModel 实例。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-126">The controller creates and populates these ViewModel instances from the model.</span></span>

### <a name="view-responsibilities"></a><span data-ttu-id="bf7d2-127">视图责任</span><span class="sxs-lookup"><span data-stu-id="bf7d2-127">View Responsibilities</span></span>

<span data-ttu-id="bf7d2-128">视图 (V) 负责通过用户界面展示内容。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-128">Views are responsible for presenting content through the user interface.</span></span> <span data-ttu-id="bf7d2-129">它们使用 [Razor 视图引擎](#razor-view-engine)在 HTML 标记中嵌入 .NET 代码。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-129">They use the [Razor view engine](#razor-view-engine) to embed .NET code in HTML markup.</span></span> <span data-ttu-id="bf7d2-130">视图中应该有最小逻辑，并且其中的任何逻辑都必须与展示内容相关。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-130">There should be minimal logic within views, and any logic in them should relate to presenting content.</span></span> <span data-ttu-id="bf7d2-131">如果发现需要在视图文件中执行大量逻辑以显示复杂模型中的数据，请考虑使用 [View Component](views/view-components.md)、ViewModel 或视图模板来简化视图。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-131">If you find the need to perform a great deal of logic in view files in order to display data from a complex model, consider using a [View Component](views/view-components.md), ViewModel, or view template to simplify the view.</span></span>

### <a name="controller-responsibilities"></a><span data-ttu-id="bf7d2-132">控制器职责</span><span class="sxs-lookup"><span data-stu-id="bf7d2-132">Controller Responsibilities</span></span>

<span data-ttu-id="bf7d2-133">控制器 (C) 是处理用户交互、使用模型并最终选择要呈现的视图的组件。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-133">Controllers are the components that handle user interaction, work with the model, and ultimately select a view to render.</span></span> <span data-ttu-id="bf7d2-134">在 MVC 应用程序中，视图仅显示信息；控制器处理并响应用户输入和交互。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-134">In an MVC application, the view only displays information; the controller handles and responds to user input and interaction.</span></span> <span data-ttu-id="bf7d2-135">在 MVC 模式中，控制器是初始入口点，负责选择要使用的模型类型和要呈现的视图（因此得名 - 它控制应用如何响应给定请求）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-135">In the MVC pattern, the controller is the initial entry point, and is responsible for selecting which model types to work with and which view to render (hence its name - it controls how the app responds to a given request).</span></span>

> [!NOTE]
> <span data-ttu-id="bf7d2-136">控制器不应由于责任过多而变得过于复杂。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-136">Controllers shouldn't be overly complicated by too many responsibilities.</span></span> <span data-ttu-id="bf7d2-137">要阻止控制器逻辑变得过于复杂，请将业务逻辑推出控制器并推入域模型。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-137">To keep controller logic from becoming overly complex, push business logic out of the controller and into the domain model.</span></span>

>[!TIP]
> <span data-ttu-id="bf7d2-138">如果发现控制器操作经常执行相同类型的操作，可将这些常见操作移入[筛选器](#filters)。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-138">If you find that your controller actions frequently perform the same kinds of actions, move these common actions into [filters](#filters).</span></span>

## <a name="what-is-aspnet-core-mvc"></a><span data-ttu-id="bf7d2-139">什么是 ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="bf7d2-139">What is ASP.NET Core MVC</span></span>

<span data-ttu-id="bf7d2-140">ASP.NET Core MVC 框架是轻量级、开源、高度可测试的演示框架，并针对 ASP.NET Core 进行了优化。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-140">The ASP.NET Core MVC framework is a lightweight, open source, highly testable presentation framework optimized for use with ASP.NET Core.</span></span>

<span data-ttu-id="bf7d2-141">ASP.NET Core MVC 提供一种基于模式的方式，用于生成可彻底分开管理事务的动态网站。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-141">ASP.NET Core MVC provides a patterns-based way to build dynamic websites that enables a clean separation of concerns.</span></span> <span data-ttu-id="bf7d2-142">它提供对标记的完全控制，支持 TDD 友好开发并使用最新的 Web 标准。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-142">It gives you full control over markup, supports TDD-friendly development and uses the latest web standards.</span></span>

## <a name="features"></a><span data-ttu-id="bf7d2-143">功能</span><span class="sxs-lookup"><span data-stu-id="bf7d2-143">Features</span></span>

<span data-ttu-id="bf7d2-144">ASP.NET Core MVC 包括以下功能：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-144">ASP.NET Core MVC includes the following:</span></span>

* [<span data-ttu-id="bf7d2-145">路由</span><span class="sxs-lookup"><span data-stu-id="bf7d2-145">Routing</span></span>](#routing)
* [<span data-ttu-id="bf7d2-146">模型绑定</span><span class="sxs-lookup"><span data-stu-id="bf7d2-146">Model binding</span></span>](#model-binding)
* [<span data-ttu-id="bf7d2-147">模型验证</span><span class="sxs-lookup"><span data-stu-id="bf7d2-147">Model validation</span></span>](#model-validation)
* [<span data-ttu-id="bf7d2-148">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="bf7d2-148">Dependency injection</span></span>](../fundamentals/dependency-injection.md)
* [<span data-ttu-id="bf7d2-149">筛选器</span><span class="sxs-lookup"><span data-stu-id="bf7d2-149">Filters</span></span>](#filters)
* [<span data-ttu-id="bf7d2-150">区域</span><span class="sxs-lookup"><span data-stu-id="bf7d2-150">Areas</span></span>](#areas)
* [<span data-ttu-id="bf7d2-151">Web API</span><span class="sxs-lookup"><span data-stu-id="bf7d2-151">Web APIs</span></span>](#web-apis)
* [<span data-ttu-id="bf7d2-152">可测试性</span><span class="sxs-lookup"><span data-stu-id="bf7d2-152">Testability</span></span>](#testability)
* [<span data-ttu-id="bf7d2-153">Razor 视图引擎</span><span class="sxs-lookup"><span data-stu-id="bf7d2-153">Razor view engine</span></span>](#razor-view-engine)
* [<span data-ttu-id="bf7d2-154">强类型视图</span><span class="sxs-lookup"><span data-stu-id="bf7d2-154">Strongly typed views</span></span>](#strongly-typed-views)
* [<span data-ttu-id="bf7d2-155">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="bf7d2-155">Tag Helpers</span></span>](#tag-helpers)
* [<span data-ttu-id="bf7d2-156">视图组件</span><span class="sxs-lookup"><span data-stu-id="bf7d2-156">View Components</span></span>](#view-components)

### <a name="routing"></a><span data-ttu-id="bf7d2-157">路由</span><span class="sxs-lookup"><span data-stu-id="bf7d2-157">Routing</span></span>

<span data-ttu-id="bf7d2-158">ASP.NET Core MVC 建立在 [ASP.NET Core 的路由](../fundamentals/routing.md)之上，是一个功能强大的 URL 映射组件，可用于生成具有易于理解和可搜索 URL 的应用程序。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-158">ASP.NET Core MVC is built on top of [ASP.NET Core's routing](../fundamentals/routing.md), a powerful URL-mapping component that lets you build applications that have comprehensible and searchable URLs.</span></span> <span data-ttu-id="bf7d2-159">它可让你定义适用于搜索引擎优化 (SEO) 和链接生成的应用程序 URL 命名模式，而不考虑如何组织 Web 服务器上的文件。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-159">This enables you to define your application's URL naming patterns that work well for search engine optimization (SEO) and for link generation, without regard for how the files on your web server are organized.</span></span> <span data-ttu-id="bf7d2-160">可以使用支持路由值约束、默认值和可选值的方便路由模板语法来定义路由。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-160">You can define your routes using a convenient route template syntax that supports route value constraints, defaults and optional values.</span></span>

<span data-ttu-id="bf7d2-161">通过基于约定的路由，可以全局定义应用程序接受的 URL 格式以及每个格式映射到给定控制器上特定操作方法的方式。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-161">*Convention-based routing* enables you to globally define the URL formats that your application accepts and how each of those formats maps to a specific action method on given controller.</span></span> <span data-ttu-id="bf7d2-162">接收传入请求时，路由引擎分析 URL 并将其匹配到定义的 URL 格式之一，然后调用关联的控制器操作方法。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-162">When an incoming request is received, the routing engine parses the URL and matches it to one of the defined URL formats, and then calls the associated controller's action method.</span></span>

```csharp
routes.MapRoute(name: "Default", template: "{controller=Home}/{action=Index}/{id?}");
```

<span data-ttu-id="bf7d2-163">借助属性路由，可以通过用定义应用程序路由的属性修饰控制器和操作来指定路由信息。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-163">*Attribute routing* enables you to specify routing information by decorating your controllers and actions with attributes that define your application's routes.</span></span> <span data-ttu-id="bf7d2-164">这意味着路由定义位于与之相关联的控制器和操作旁。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-164">This means that your route definitions are placed next to the controller and action with which they're associated.</span></span>

```csharp
[Route("api/[controller]")]
public class ProductsController : Controller
{
  [HttpGet("{id}")]
  public IActionResult GetProduct(int id)
  {
    ...
  }
}
```

### <a name="model-binding"></a><span data-ttu-id="bf7d2-165">模型绑定</span><span class="sxs-lookup"><span data-stu-id="bf7d2-165">Model binding</span></span>

<span data-ttu-id="bf7d2-166">ASP.NET Core MVC [模型绑定](models/model-binding.md)将客户端请求数据（窗体值、路由数据、查询字符串参数、HTTP 头）转换到控制器可以处理的对象中。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-166">ASP.NET Core MVC [model binding](models/model-binding.md) converts client request data  (form values, route data, query string parameters, HTTP headers) into objects that the controller can handle.</span></span> <span data-ttu-id="bf7d2-167">因此，控制器逻辑不必找出传入的请求数据；它只需具备作为其操作方法的参数的数据。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-167">As a result, your controller logic doesn't have to do the work of figuring out the incoming request data; it simply has the data as parameters to its action methods.</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null) { ... }
   ```

### <a name="model-validation"></a><span data-ttu-id="bf7d2-168">模型验证</span><span class="sxs-lookup"><span data-stu-id="bf7d2-168">Model validation</span></span>

<span data-ttu-id="bf7d2-169">ASP.NET Core MVC 通过使用数据注释验证属性修饰模型对象来支持[验证](models/validation.md)。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-169">ASP.NET Core MVC supports [validation](models/validation.md) by decorating your model object with data annotation validation attributes.</span></span> <span data-ttu-id="bf7d2-170">验证属性在值发布到服务器前在客户端上进行检查，并在调用控制器操作前在服务器上进行检查。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-170">The validation attributes are checked on the client side before values are posted to the server, as well as on the server before the controller action is called.</span></span>

```csharp
using System.ComponentModel.DataAnnotations;
public class LoginViewModel
{
    [Required]
    [EmailAddress]
    public string Email { get; set; }

    [Required]
    [DataType(DataType.Password)]
    public string Password { get; set; }

    [Display(Name = "Remember me?")]
    public bool RememberMe { get; set; }
}
```

<span data-ttu-id="bf7d2-171">控制器操作：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-171">A controller action:</span></span>

```csharp
public async Task<IActionResult> Login(LoginViewModel model, string returnUrl = null)
{
    if (ModelState.IsValid)
    {
      // work with the model
    }
    // At this point, something failed, redisplay form
    return View(model);
}
```

<span data-ttu-id="bf7d2-172">框架处理客户端和服务器上的验证请求数据。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-172">The framework handles validating request data both on the client and on the server.</span></span> <span data-ttu-id="bf7d2-173">在模型类型上指定的验证逻辑作为非介入式注释添加到呈现的视图，并使用 [jQuery 验证](https://jqueryvalidation.org/)在浏览器中强制执行。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-173">Validation logic specified on model types is added to the rendered views as unobtrusive annotations and is enforced in the browser with [jQuery Validation](https://jqueryvalidation.org/).</span></span>

### <a name="dependency-injection"></a><span data-ttu-id="bf7d2-174">依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="bf7d2-174">Dependency injection</span></span>

<span data-ttu-id="bf7d2-175">ASP.NET Core 内置有对[依赖关系注入 (DI)](../fundamentals/dependency-injection.md) 的支持。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-175">ASP.NET Core has built-in support for [dependency injection (DI)](../fundamentals/dependency-injection.md).</span></span> <span data-ttu-id="bf7d2-176">在 ASP.NET Core MVC 中，[控制器](controllers/dependency-injection.md)可通过其构造函数请求所需服务，使其能够遵循 [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies)（显式依赖关系原则）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-176">In ASP.NET Core MVC, [controllers](controllers/dependency-injection.md) can request needed services through their constructors, allowing them to follow the [Explicit Dependencies Principle](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#explicit-dependencies).</span></span>

<span data-ttu-id="bf7d2-177">应用还可通过 `@inject` 指令使用[视图文件中的依赖关系注入](views/dependency-injection.md)：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-177">Your app can also use [dependency injection in view files](views/dependency-injection.md), using the `@inject` directive:</span></span>

```cshtml
@inject SomeService ServiceName
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ServiceName.GetTitle</title>
</head>
<body>
    <h1>@ServiceName.GetTitle</h1>
</body>
</html>
```

### <a name="filters"></a><span data-ttu-id="bf7d2-178">筛选器</span><span class="sxs-lookup"><span data-stu-id="bf7d2-178">Filters</span></span>

<span data-ttu-id="bf7d2-179">[筛选器](controllers/filters.md)帮助开发者封装横切关注点，例如异常处理或授权。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-179">[Filters](controllers/filters.md) help developers encapsulate cross-cutting concerns, like exception handling or authorization.</span></span> <span data-ttu-id="bf7d2-180">筛选器允许操作方法运行自定义预处理和后处理逻辑，并且可以配置为在给定请求的执行管道内的特定点上运行。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-180">Filters enable running custom pre- and post-processing logic for action methods, and can be configured to run at certain points within the execution pipeline for a given request.</span></span> <span data-ttu-id="bf7d2-181">筛选器可以作为属性应用于控制器或操作（也可以全局运行）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-181">Filters can be applied to controllers or actions as attributes (or can be run globally).</span></span> <span data-ttu-id="bf7d2-182">此框架中包括多个筛选器（例如 `Authorize`）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-182">Several filters (such as `Authorize`) are included in the framework.</span></span> <span data-ttu-id="bf7d2-183">`[Authorize]` 是用于创建 MVC 授权筛选器的属性。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-183">`[Authorize]` is the attribute that is used to create MVC authorization filters.</span></span>

```csharp
[Authorize]
public class AccountController : Controller
```

### <a name="areas"></a><span data-ttu-id="bf7d2-184">区域</span><span class="sxs-lookup"><span data-stu-id="bf7d2-184">Areas</span></span>

<span data-ttu-id="bf7d2-185">[区域](controllers/areas.md)提供将大型 ASP.NET Core MVC Web 应用分区为较小功能分组的方法。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-185">[Areas](controllers/areas.md) provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="bf7d2-186">区域是应用程序内的一个 MVC 结构。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-186">An area is an MVC structure inside an application.</span></span> <span data-ttu-id="bf7d2-187">在 MVC 项目中，模型、控制器和视图等逻辑组件保存在不同的文件夹中，MVC 使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-187">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="bf7d2-188">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-188">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="bf7d2-189">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。每个单位都有自己的逻辑组件视图、控制器和模型。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-189">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span>

### <a name="web-apis"></a><span data-ttu-id="bf7d2-190">Web API</span><span class="sxs-lookup"><span data-stu-id="bf7d2-190">Web APIs</span></span>

<span data-ttu-id="bf7d2-191">除了作为生成网站的强大平台，ASP.NET Core MVC 还对生成 Web API 提供强大的支持。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-191">In addition to being a great platform for building web sites, ASP.NET Core MVC has great support for building Web APIs.</span></span> <span data-ttu-id="bf7d2-192">可以生成可连接大量客户端（包括浏览器和移动设备）的服务。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-192">You can build services that reach a broad range of clients including browsers and mobile devices.</span></span>

<span data-ttu-id="bf7d2-193">该框架包括对 HTTP 内容协商的支持，后者有允许[设置数据格式](xref:web-api/advanced/formatting)为 JSON 或 XML 的内置支持。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-193">The framework includes support for HTTP content-negotiation with built-in support to [format data](xref:web-api/advanced/formatting) as JSON or XML.</span></span> <span data-ttu-id="bf7d2-194">编写[自定义格式化程序](xref:web-api/advanced/custom-formatters)以添加对自己格式的支持。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-194">Write [custom formatters](xref:web-api/advanced/custom-formatters) to add support for your own formats.</span></span>

<span data-ttu-id="bf7d2-195">使用链接生成启用对超媒体的支持。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-195">Use link generation to enable support for hypermedia.</span></span> <span data-ttu-id="bf7d2-196">轻松启用对[跨域资源共享 (CORS)](http://www.w3.org/TR/cors/) 的支持，以便 Web API 可以跨多个 Web 应用程序共享。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-196">Easily enable support for [cross-origin resource sharing (CORS)](http://www.w3.org/TR/cors/) so that your Web APIs can be shared across multiple Web applications.</span></span>

### <a name="testability"></a><span data-ttu-id="bf7d2-197">可测试性</span><span class="sxs-lookup"><span data-stu-id="bf7d2-197">Testability</span></span>

<span data-ttu-id="bf7d2-198">框架对界面和依赖项注入的使用非常适用于单元测试，并且该框架还包括使得[集成测试](xref:test/integration-tests)快速轻松的功能（例如 TestHost 和实体框架的 InMemory 提供程序）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-198">The framework's use of interfaces and dependency injection make it well-suited to unit testing, and the framework includes features (like a TestHost and InMemory provider for Entity Framework) that make [integration tests](xref:test/integration-tests) quick and easy as well.</span></span> <span data-ttu-id="bf7d2-199">详细了解[如何测试控制器逻辑](controllers/testing.md)。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-199">Learn more about [how to test controller logic](controllers/testing.md).</span></span>

### <a name="razor-view-engine"></a><span data-ttu-id="bf7d2-200">Razor 视图引擎</span><span class="sxs-lookup"><span data-stu-id="bf7d2-200">Razor view engine</span></span>

<span data-ttu-id="bf7d2-201">[ASP.NET Core MVC 视图](views/overview.md)使用 [Razor 视图引擎](views/razor.md)呈现视图。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-201">[ASP.NET Core MVC views](views/overview.md) use the [Razor view engine](views/razor.md) to render views.</span></span> <span data-ttu-id="bf7d2-202">Razor 是一种紧凑、富有表现力且流畅的模板标记语言，用于使用嵌入式 C# 代码定义视图。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-202">Razor is a compact, expressive and fluid template markup language for defining views using embedded C# code.</span></span> <span data-ttu-id="bf7d2-203">Razor 用于在服务器上动态生成 Web 内容。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-203">Razor is used to dynamically generate web content on the server.</span></span> <span data-ttu-id="bf7d2-204">可以完全混合服务器代码与客户端内容和代码。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-204">You can cleanly mix server code with client side content and code.</span></span>

```text
<ul>
  @for (int i = 0; i < 5; i++) {
    <li>List item @i</li>
  }
</ul>
```

<span data-ttu-id="bf7d2-205">使用 Razor 视图引擎可以定义[布局](views/layout.md)、[分部视图](views/partial.md)和可替换部分。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-205">Using the Razor view engine you can define [layouts](views/layout.md), [partial views](views/partial.md) and replaceable sections.</span></span>

### <a name="strongly-typed-views"></a><span data-ttu-id="bf7d2-206">强类型视图</span><span class="sxs-lookup"><span data-stu-id="bf7d2-206">Strongly typed views</span></span>

<span data-ttu-id="bf7d2-207">可以基于模型强类型化 MVC 中的 Razor 视图。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-207">Razor views in MVC can be strongly typed based on your model.</span></span> <span data-ttu-id="bf7d2-208">控制器可以将强类型化的模型传递给视图，使视图具备类型检查和 IntelliSense 支持。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-208">Controllers can pass a strongly typed model to views enabling your views to have type checking and IntelliSense support.</span></span>

<span data-ttu-id="bf7d2-209">例如，以下视图呈现类型为 `IEnumerable<Product>` 的模型：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-209">For example, the following view renders a model of type `IEnumerable<Product>`:</span></span>

```cshtml
@model IEnumerable<Product>
<ul>
    @foreach (Product p in Model)
    {
        <li>@p.Name</li>
    }
</ul>
```

### <a name="tag-helpers"></a><span data-ttu-id="bf7d2-210">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="bf7d2-210">Tag Helpers</span></span>

<span data-ttu-id="bf7d2-211">[标记帮助程序](views/tag-helpers/intro.md)使服务器端代码可以在 Razor 文件中参与创建和呈现 HTML 元素。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-211">[Tag Helpers](views/tag-helpers/intro.md) enable server side code to participate in creating and rendering HTML elements in Razor files.</span></span> <span data-ttu-id="bf7d2-212">可以使用标记帮助程序定义自定义标记（例如 `<environment>`），或者修改现有标记的行为（例如 `<label>`）。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-212">You can use tag helpers to define custom tags (for example, `<environment>`) or to modify the behavior of existing tags (for example, `<label>`).</span></span> <span data-ttu-id="bf7d2-213">标记帮助程序基于元素名称及其属性绑定到特定的元素。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-213">Tag Helpers bind to specific elements based on the element name and its attributes.</span></span> <span data-ttu-id="bf7d2-214">它们提供了服务器端呈现的优势，同时仍然保留了 HTML 编辑体验。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-214">They provide the benefits of server-side rendering while still preserving an HTML editing experience.</span></span>

<span data-ttu-id="bf7d2-215">有多种常见任务（例如创建窗体、链接，加载资产等）的内置标记帮助程序，公共 GitHub 存储库和 NuGet 包中甚至还有更多可用标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-215">There are many built-in Tag Helpers for common tasks - such as creating forms, links, loading assets and more - and even more available in public GitHub repositories and as NuGet packages.</span></span> <span data-ttu-id="bf7d2-216">标记帮助程序使用 C# 创建，基于元素名称、属性名称或父标记以 HTML 元素为目标。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-216">Tag Helpers are authored in C#, and they target HTML elements based on element name, attribute name, or parent tag.</span></span> <span data-ttu-id="bf7d2-217">例如，内置 LinkTagHelper 可以用来创建指向 `AccountsController` 的 `Login` 操作的链接：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-217">For example, the built-in LinkTagHelper can be used to create a link to the `Login` action of the `AccountsController`:</span></span>

```cshtml
<p>
    Thank you for confirming your email.
    Please <a asp-controller="Account" asp-action="Login">Click here to Log in</a>.
</p>
```

<span data-ttu-id="bf7d2-218">可以使用 `EnvironmentTagHelper` 在视图中包括基于运行时环境（例如开发、暂存或生产）的不同脚本（例如原始或缩减脚本）：</span><span class="sxs-lookup"><span data-stu-id="bf7d2-218">The `EnvironmentTagHelper` can be used to include different scripts in your views (for example, raw or minified) based on the runtime environment, such as Development, Staging, or Production:</span></span>

```cshtml
<environment names="Development">
    <script src="~/lib/jquery/dist/jquery.js"></script>
</environment>
<environment names="Staging,Production">
    <script src="https://ajax.aspnetcdn.com/ajax/jquery/jquery-2.1.4.min.js"
            asp-fallback-src="~/lib/jquery/dist/jquery.min.js"
            asp-fallback-test="window.jQuery">
    </script>
</environment>
```

<span data-ttu-id="bf7d2-219">标记帮助程序提供 HTML 友好型开发体验和用于创建 HTML 和 Razor 标记的丰富 IntelliSense 环境。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-219">Tag Helpers provide an HTML-friendly development experience and a rich IntelliSense environment for creating HTML and Razor markup.</span></span> <span data-ttu-id="bf7d2-220">大多数内置标记帮助程序以现有 HTML 元素为目标，为该元素提供服务器端属性。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-220">Most of the built-in Tag Helpers target existing HTML elements and provide server-side attributes for the element.</span></span>

### <a name="view-components"></a><span data-ttu-id="bf7d2-221">视图组件</span><span class="sxs-lookup"><span data-stu-id="bf7d2-221">View Components</span></span>

<span data-ttu-id="bf7d2-222">通过[视图组件](views/view-components.md)可以包装呈现逻辑并在整个应用程序中重用它。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-222">[View Components](views/view-components.md) allow you to package rendering logic and reuse it throughout the application.</span></span> <span data-ttu-id="bf7d2-223">这些组件类似于[分部视图](views/partial.md)，但具有关联逻辑。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-223">They're similar to [partial views](views/partial.md), but with associated logic.</span></span>

## <a name="compatibility-version"></a><span data-ttu-id="bf7d2-224">兼容性版本</span><span class="sxs-lookup"><span data-stu-id="bf7d2-224">Compatibility version</span></span>

<span data-ttu-id="bf7d2-225"><xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> 方法允许应用选择加入或退出 ASP.NET Core MVC 2.1 或更高版本中引入的潜在中断行为变更。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-225">The <xref:Microsoft.Extensions.DependencyInjection.MvcCoreMvcBuilderExtensions.SetCompatibilityVersion*> method allows an app to opt-in or opt-out of potentially breaking behavior changes introduced in ASP.NET Core MVC 2.1 or later.</span></span>

<span data-ttu-id="bf7d2-226">有关更多信息，请参见<xref:mvc/compatibility-version>。</span><span class="sxs-lookup"><span data-stu-id="bf7d2-226">For more information, see <xref:mvc/compatibility-version>.</span></span>
