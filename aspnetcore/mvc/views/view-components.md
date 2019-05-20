---
title: ASP.NET Core 中的视图组件
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用视图组件，以及如何将其添加到应用中。
ms.author: riande
ms.custom: mvc
ms.date: 5/14/2019
uid: mvc/views/view-components
ms.openlocfilehash: 17fd7aa977868d522df9f27e0c23d07b016bfb7c
ms.sourcegitcommit: 3ee6ee0051c3d2c8d47a58cb17eef1a84a4c46a0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/14/2019
ms.locfileid: "65621072"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="b762c-103">ASP.NET Core 中的视图组件</span><span class="sxs-lookup"><span data-stu-id="b762c-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="b762c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="b762c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="b762c-105">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="b762c-105">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="b762c-106">视图组件</span><span class="sxs-lookup"><span data-stu-id="b762c-106">View components</span></span>

<span data-ttu-id="b762c-107">视图组件与分部视图类似，但它们的功能更加强大。</span><span class="sxs-lookup"><span data-stu-id="b762c-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="b762c-108">视图组件不使用模型绑定，并且仅依赖调用时提供的数据。</span><span class="sxs-lookup"><span data-stu-id="b762c-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="b762c-109">本文是使用控制器和视图编写的，但视图组件也适用于 Razor Pages。</span><span class="sxs-lookup"><span data-stu-id="b762c-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="b762c-110">视图组件：</span><span class="sxs-lookup"><span data-stu-id="b762c-110">A view component:</span></span>

* <span data-ttu-id="b762c-111">呈现一个区块而不是整个响应。</span><span class="sxs-lookup"><span data-stu-id="b762c-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="b762c-112">包括控制器和视图间发现的相同关注点分离和可测试性优势。</span><span class="sxs-lookup"><span data-stu-id="b762c-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="b762c-113">可以有参数和业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="b762c-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="b762c-114">通常从布局页调用。</span><span class="sxs-lookup"><span data-stu-id="b762c-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="b762c-115">视图组件可用于具有可重用呈现逻辑（对分部视图来说过于复杂）的任何位置，例如：</span><span class="sxs-lookup"><span data-stu-id="b762c-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="b762c-116">动态导航菜单</span><span class="sxs-lookup"><span data-stu-id="b762c-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="b762c-117">标记云（查询数据库的位置）</span><span class="sxs-lookup"><span data-stu-id="b762c-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="b762c-118">登录面板</span><span class="sxs-lookup"><span data-stu-id="b762c-118">Login panel</span></span>
* <span data-ttu-id="b762c-119">购物车</span><span class="sxs-lookup"><span data-stu-id="b762c-119">Shopping cart</span></span>
* <span data-ttu-id="b762c-120">最近发布的文章</span><span class="sxs-lookup"><span data-stu-id="b762c-120">Recently published articles</span></span>
* <span data-ttu-id="b762c-121">典型博客上的边栏内容</span><span class="sxs-lookup"><span data-stu-id="b762c-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="b762c-122">一个登录面板，呈现在每页上并显示注销或登录链接，具体取决于用户的登录状态</span><span class="sxs-lookup"><span data-stu-id="b762c-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="b762c-123">视图组件由两部分组成：类（通常派生自 [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)）及其返回的结果（通常为视图）。</span><span class="sxs-lookup"><span data-stu-id="b762c-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="b762c-124">与控制器一样，视图组件也可以是 POCO，但大多数开发人员都希望利用派生自 `ViewComponent` 的可用方法和属性。</span><span class="sxs-lookup"><span data-stu-id="b762c-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="b762c-125">在考虑视图组件是否符合应用的规范时，请考虑改用 Razor 组件。</span><span class="sxs-lookup"><span data-stu-id="b762c-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> <span data-ttu-id="b762c-126">Razor 组件还将标记与 C# 代码组合来生成可重用的 UI 单元。</span><span class="sxs-lookup"><span data-stu-id="b762c-126">Razor Components also combine markup with C# code to produce reusable UI units.</span></span> <span data-ttu-id="b762c-127">Razor 组件专用于让开发人员在提供客户端 UI 逻辑和组合时保持高效。</span><span class="sxs-lookup"><span data-stu-id="b762c-127">Razor Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="b762c-128">有关更多信息，请参见<xref:blazor/components>。</span><span class="sxs-lookup"><span data-stu-id="b762c-128">For more information, see <xref:blazor/components>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="b762c-129">创建视图组件</span><span class="sxs-lookup"><span data-stu-id="b762c-129">Creating a view component</span></span>

<span data-ttu-id="b762c-130">本部分包含创建视图组件的高级别要求。</span><span class="sxs-lookup"><span data-stu-id="b762c-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="b762c-131">本文后续部分将详细检查每个步骤并创建视图组件。</span><span class="sxs-lookup"><span data-stu-id="b762c-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="b762c-132">视图组件类</span><span class="sxs-lookup"><span data-stu-id="b762c-132">The view component class</span></span>

<span data-ttu-id="b762c-133">可通过以下任一方法创建视图组件类：</span><span class="sxs-lookup"><span data-stu-id="b762c-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="b762c-134">从 ViewComponent 派生</span><span class="sxs-lookup"><span data-stu-id="b762c-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="b762c-135">使用 `[ViewComponent]` 属性修饰类，或者从具有 `[ViewComponent]` 属性的类派生</span><span class="sxs-lookup"><span data-stu-id="b762c-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="b762c-136">创建名称以 ViewComponent 后缀结尾的类</span><span class="sxs-lookup"><span data-stu-id="b762c-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="b762c-137">与控制器一样，视图组件必须是公共、非嵌套和非抽象的类。</span><span class="sxs-lookup"><span data-stu-id="b762c-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="b762c-138">视图组件名称是删除了“ViewComponent”后缀的类名。</span><span class="sxs-lookup"><span data-stu-id="b762c-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="b762c-139">也可以使用 `ViewComponentAttribute.Name` 属性显式指定它。</span><span class="sxs-lookup"><span data-stu-id="b762c-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="b762c-140">视图组件类：</span><span class="sxs-lookup"><span data-stu-id="b762c-140">A view component class:</span></span>

* <span data-ttu-id="b762c-141">完全支持构造函数[依赖关系注入](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="b762c-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="b762c-142">不参与控制器生命周期，这意味着不能在视图组件中使用[筛选器](../controllers/filters.md)</span><span class="sxs-lookup"><span data-stu-id="b762c-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="b762c-143">视图组件方法</span><span class="sxs-lookup"><span data-stu-id="b762c-143">View component methods</span></span>

<span data-ttu-id="b762c-144">视图组件以返回 `Task<IViewComponentResult>` 的 `InvokeAsync` 方法，或是以返回 `IViewComponentResult` 的同步 `Invoke` 方法定义其逻辑。</span><span class="sxs-lookup"><span data-stu-id="b762c-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="b762c-145">参数直接来自视图组件的调用，而不是来自模型绑定。</span><span class="sxs-lookup"><span data-stu-id="b762c-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="b762c-146">视图组件从不直接处理请求。</span><span class="sxs-lookup"><span data-stu-id="b762c-146">A view component never directly handles a request.</span></span> <span data-ttu-id="b762c-147">通常，视图组件通过调用 `View` 方法来初始化模型并将其传递到视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="b762c-148">总之，视图组件方法：</span><span class="sxs-lookup"><span data-stu-id="b762c-148">In summary, view component methods:</span></span>

* <span data-ttu-id="b762c-149">定义返回 `Task<IViewComponentResult>` 的 `InvokeAsync` 方法，或是返回 `IViewComponentResult` 的同步 `Invoke` 方法。</span><span class="sxs-lookup"><span data-stu-id="b762c-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="b762c-150">一般通过调用 `ViewComponent` `View` 方法来初始化模型并将其传递到视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="b762c-151">参数来自调用方法，而不是 HTTP。</span><span class="sxs-lookup"><span data-stu-id="b762c-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="b762c-152">没有模型绑定。</span><span class="sxs-lookup"><span data-stu-id="b762c-152">There's no model binding.</span></span>
* <span data-ttu-id="b762c-153">不可直接作为 HTTP 终结点进行访问。</span><span class="sxs-lookup"><span data-stu-id="b762c-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="b762c-154">通过代码调用它们（通常在视图中）。</span><span class="sxs-lookup"><span data-stu-id="b762c-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="b762c-155">视图组件从不处理请求。</span><span class="sxs-lookup"><span data-stu-id="b762c-155">A view component never handles a request.</span></span>
* <span data-ttu-id="b762c-156">在签名上重载，而不是当前 HTTP 请求的任何详细信息。</span><span class="sxs-lookup"><span data-stu-id="b762c-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="b762c-157">视图搜索路径</span><span class="sxs-lookup"><span data-stu-id="b762c-157">View search path</span></span>

<span data-ttu-id="b762c-158">运行时在以下路径中搜索视图：</span><span class="sxs-lookup"><span data-stu-id="b762c-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="b762c-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="b762c-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="b762c-160">/Views/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="b762c-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="b762c-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="b762c-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="b762c-162">搜索路径适用于使用控制器 + 视图和 Razor Pages 的项目。</span><span class="sxs-lookup"><span data-stu-id="b762c-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="b762c-163">视图组件的默认视图名称为“默认”，这意味着视图文件通常命名为“Default.cshtml”。</span><span class="sxs-lookup"><span data-stu-id="b762c-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="b762c-164">可以在创建视图组件结果或调用 `View` 方法时指定不同的视图名称。</span><span class="sxs-lookup"><span data-stu-id="b762c-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="b762c-165">建议将视图文件命名为 Default.cshtml 并使用 Views/Shared/Components/{View Component Name}/{View Name} 路径。</span><span class="sxs-lookup"><span data-stu-id="b762c-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="b762c-166">此示例中使用的 `PriorityList` 视图组件对视图组件视图使用 Views/Shared/Components/PriorityList/Default.cshtml。</span><span class="sxs-lookup"><span data-stu-id="b762c-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="b762c-167">调用视图组件</span><span class="sxs-lookup"><span data-stu-id="b762c-167">Invoking a view component</span></span>

<span data-ttu-id="b762c-168">要使用视图组件，请在视图中调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="b762c-168">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="b762c-169">参数将传递给 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="b762c-169">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="b762c-170">本文中开发的 `PriorityList` 视图组件调用自 Views/ToDo/Index.cshtml 视图文件。</span><span class="sxs-lookup"><span data-stu-id="b762c-170">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="b762c-171">在下例中，使用两个参数调用 `InvokeAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b762c-171">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="b762c-172">调用视图组件作为标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="b762c-172">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="b762c-173">对于 ASP.NET Core 1.1 及更高版本，可以调用视图组件作为[标记帮助程序](xref:mvc/views/tag-helpers/intro)：</span><span class="sxs-lookup"><span data-stu-id="b762c-173">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="b762c-174">标记帮助程序采用 Pascal 大小写格式的类和方法参数将转换为各自相应的[短横线格式](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)。</span><span class="sxs-lookup"><span data-stu-id="b762c-174">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="b762c-175">要调用视图组件的标记帮助程序使用 `<vc></vc>` 元素。</span><span class="sxs-lookup"><span data-stu-id="b762c-175">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="b762c-176">按如下方式指定视图组件：</span><span class="sxs-lookup"><span data-stu-id="b762c-176">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="b762c-177">若要将视图组件用作标记帮助程序，请使用 `@addTagHelper` 指令注册包含视图组件的程序集。</span><span class="sxs-lookup"><span data-stu-id="b762c-177">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="b762c-178">如果视图组件位于名为“`MyWebApp`”的程序集中，请将以下指令添加到 _ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="b762c-178">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="b762c-179">可将视图组件作为标记帮助程序注册到任何引用视图组件的文件。</span><span class="sxs-lookup"><span data-stu-id="b762c-179">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="b762c-180">要详细了解如何注册标记帮助程序，请参阅[管理标记帮助程序作用域](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)。</span><span class="sxs-lookup"><span data-stu-id="b762c-180">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="b762c-181">本教程中使用的 `InvokeAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="b762c-181">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="b762c-182">在标记帮助程序标记中：</span><span class="sxs-lookup"><span data-stu-id="b762c-182">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="b762c-183">在以上示例中，`PriorityList` 视图组件变为 `priority-list`。</span><span class="sxs-lookup"><span data-stu-id="b762c-183">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="b762c-184">视图组件的参数作为短横线格式的属性进行传递。</span><span class="sxs-lookup"><span data-stu-id="b762c-184">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="b762c-185">从控制器直接调用视图组件</span><span class="sxs-lookup"><span data-stu-id="b762c-185">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="b762c-186">视图组件通常从视图调用，但你可以直接从控制器方法调用它们。</span><span class="sxs-lookup"><span data-stu-id="b762c-186">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="b762c-187">尽管视图组件不定义控制器等终结点，但你可以轻松实现返回 `ViewComponentResult` 内容的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="b762c-187">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="b762c-188">在此示例中，视图组件直接从控制器调用：</span><span class="sxs-lookup"><span data-stu-id="b762c-188">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="b762c-189">演练：创建简单的视图组件</span><span class="sxs-lookup"><span data-stu-id="b762c-189">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="b762c-190">[下载](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、生成和测试起始代码。</span><span class="sxs-lookup"><span data-stu-id="b762c-190">[Download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="b762c-191">它是一个带有 `ToDo` 控制器的简单项目，该控制器显示 ToDo 项的列表。</span><span class="sxs-lookup"><span data-stu-id="b762c-191">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![ToDo 列表](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="b762c-193">添加 ViewComponent 类</span><span class="sxs-lookup"><span data-stu-id="b762c-193">Add a ViewComponent class</span></span>

<span data-ttu-id="b762c-194">创建一个 ViewComponents 文件夹并添加以下 `PriorityListViewComponent` 类：</span><span class="sxs-lookup"><span data-stu-id="b762c-194">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="b762c-195">代码说明：</span><span class="sxs-lookup"><span data-stu-id="b762c-195">Notes on the code:</span></span>

* <span data-ttu-id="b762c-196">视图组件类可以包含在项目的任意文件夹中。</span><span class="sxs-lookup"><span data-stu-id="b762c-196">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="b762c-197">因为类名 PriorityListViewComponent 以后缀 ViewComponent 结尾，所以运行时将在从视图引用类组件时使用字符串“PriorityList”。</span><span class="sxs-lookup"><span data-stu-id="b762c-197">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="b762c-198">我稍后将进行详细解释。</span><span class="sxs-lookup"><span data-stu-id="b762c-198">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="b762c-199">`[ViewComponent]` 属性可以更改用于引用视图组件的名称。</span><span class="sxs-lookup"><span data-stu-id="b762c-199">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="b762c-200">例如，我们可以将类命名为 `XYZ` 并应用 `ViewComponent` 属性：</span><span class="sxs-lookup"><span data-stu-id="b762c-200">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="b762c-201">上面的 `[ViewComponent]` 属性通知视图组件选择器在查找与组件相关联的视图时使用名称 `PriorityList`，以及在从视图引用类组件时使用字符串“PriorityList”。</span><span class="sxs-lookup"><span data-stu-id="b762c-201">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="b762c-202">我稍后将进行详细解释。</span><span class="sxs-lookup"><span data-stu-id="b762c-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="b762c-203">组件使用[依赖关系注入](../../fundamentals/dependency-injection.md)以使数据上下文可用。</span><span class="sxs-lookup"><span data-stu-id="b762c-203">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="b762c-204">`InvokeAsync` 公开可以从视图调用的方法，且可以采用任意数量的参数。</span><span class="sxs-lookup"><span data-stu-id="b762c-204">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="b762c-205">`InvokeAsync` 方法返回满足 `isDone` 和 `maxPriority` 参数的 `ToDo` 项集。</span><span class="sxs-lookup"><span data-stu-id="b762c-205">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="b762c-206">创建视图组件 Razor 视图</span><span class="sxs-lookup"><span data-stu-id="b762c-206">Create the view component Razor view</span></span>

* <span data-ttu-id="b762c-207">创建 Views/Shared/Components 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b762c-207">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="b762c-208">此文件夹 **必须** 命名为 *Components*。</span><span class="sxs-lookup"><span data-stu-id="b762c-208">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="b762c-209">创建 Views/Shared/Components/PriorityList 文件夹。</span><span class="sxs-lookup"><span data-stu-id="b762c-209">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="b762c-210">此文件夹名称必须与视图组件类的名称或类名去掉后缀（如果遵照约定并在类名中使用了“ViewComponent”后缀）的名称相匹配。</span><span class="sxs-lookup"><span data-stu-id="b762c-210">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="b762c-211">如果使用了 `ViewComponent` 属性，则类名称需要匹配指定的属性。</span><span class="sxs-lookup"><span data-stu-id="b762c-211">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="b762c-212">创建 Views/Shared/Components/PriorityList/Default.cshtml Razor 视图：</span><span class="sxs-lookup"><span data-stu-id="b762c-212">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="b762c-213">Razor 视图获取并显示 `TodoItem` 列表。</span><span class="sxs-lookup"><span data-stu-id="b762c-213">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="b762c-214">如果视图组件 `InvokeAsync` 方法不传递视图名称（如示例中所示），则按照约定使用“默认”作为视图名称。</span><span class="sxs-lookup"><span data-stu-id="b762c-214">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="b762c-215">在本教程后面部分，我将演示如何传递视图名称。</span><span class="sxs-lookup"><span data-stu-id="b762c-215">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="b762c-216">要替代特定控制器的默认样式，请将视图添加到控制器特定的视图文件夹（例如 Views/ToDo/Components/PriorityList/Default.cshtml）。</span><span class="sxs-lookup"><span data-stu-id="b762c-216">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="b762c-217">如果视图组件是控制器特定的，则可将其添加到控制器特定的文件夹 (Views/ToDo/Components/PriorityList/Default.cshtml)。</span><span class="sxs-lookup"><span data-stu-id="b762c-217">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="b762c-218">将包含优先级列表组件调用的 `div` 添加到 Views/ToDo/index.cshtml 文件底部：</span><span class="sxs-lookup"><span data-stu-id="b762c-218">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="b762c-219">标记 `@await Component.InvokeAsync` 显示调用视图组件的语法。</span><span class="sxs-lookup"><span data-stu-id="b762c-219">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="b762c-220">第一个参数是要调用的组件的名称。</span><span class="sxs-lookup"><span data-stu-id="b762c-220">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="b762c-221">后续参数将传递给该组件。</span><span class="sxs-lookup"><span data-stu-id="b762c-221">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="b762c-222">`InvokeAsync` 可以采用任意数量的参数。</span><span class="sxs-lookup"><span data-stu-id="b762c-222">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="b762c-223">测试应用。</span><span class="sxs-lookup"><span data-stu-id="b762c-223">Test the app.</span></span> <span data-ttu-id="b762c-224">下图显示 ToDo 列表和优先级项：</span><span class="sxs-lookup"><span data-stu-id="b762c-224">The following image shows the ToDo list and the priority items:</span></span>

![Todo 列表和优先级项](view-components/_static/pi.png)

<span data-ttu-id="b762c-226">也可直接从控制器调用视图组件：</span><span class="sxs-lookup"><span data-stu-id="b762c-226">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 操作的优先级项](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="b762c-228">指定视图名称</span><span class="sxs-lookup"><span data-stu-id="b762c-228">Specifying a view name</span></span>

<span data-ttu-id="b762c-229">在某些情况下，复杂的视图组件可能需要指定非默认视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-229">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="b762c-230">以下代码显示如何从 `InvokeAsync` 方法指定“PVC”视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-230">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="b762c-231">更新 `PriorityListViewComponent` 类中的 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="b762c-231">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="b762c-232">将 Views/Shared/Components/PriorityList/Default.cshtml 文件复制到名为 Views/Shared/Components/PriorityList/PVC.cshtml 的视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-232">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="b762c-233">添加标题以指示正在使用 PVC 视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-233">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="b762c-234">更新 Views/ToDo/Index.cshtml：</span><span class="sxs-lookup"><span data-stu-id="b762c-234">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="b762c-235">运行应用并验证 PVC 视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-235">Run the app and verify PVC view.</span></span>

![优先级视图组件](view-components/_static/pvc.png)

<span data-ttu-id="b762c-237">如果不呈现 PVC 视图，请验证是否调用优先级为 4 或更高的视图组件。</span><span class="sxs-lookup"><span data-stu-id="b762c-237">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="b762c-238">检查视图路径</span><span class="sxs-lookup"><span data-stu-id="b762c-238">Examine the view path</span></span>

* <span data-ttu-id="b762c-239">将优先级参数更改为 3 或更低，从而不返回优先级视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-239">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="b762c-240">将 Views/ToDo/Components/PriorityList/Default.cshtml 暂时重命名为 1Default.cshtml。</span><span class="sxs-lookup"><span data-stu-id="b762c-240">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="b762c-241">测试应用，你将收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="b762c-241">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="b762c-242">将 Views/ToDo/Components/PriorityList/1Default.cshtml 复制到 Views/Shared/Components/PriorityList/Default.cshtml。</span><span class="sxs-lookup"><span data-stu-id="b762c-242">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="b762c-243">将一些标记添加到共享 ToDo 视图组件视图，以指示视图来自“Shared”文件夹。</span><span class="sxs-lookup"><span data-stu-id="b762c-243">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="b762c-244">测试“共享”组件视图。</span><span class="sxs-lookup"><span data-stu-id="b762c-244">Test the **Shared** component view.</span></span>

![有共享组件视图的 ToDo 输出](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="b762c-246">避免使用硬编码字符串</span><span class="sxs-lookup"><span data-stu-id="b762c-246">Avoiding hard-coded strings</span></span>

<span data-ttu-id="b762c-247">若要确保编译时的安全性，可以用类名替换硬编码的视图组件名称。</span><span class="sxs-lookup"><span data-stu-id="b762c-247">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="b762c-248">创建没有“ViewComponent”后缀的视图组件：</span><span class="sxs-lookup"><span data-stu-id="b762c-248">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="b762c-249">将 `using` 语句添加到 Razor 视图文件，并使用 `nameof` 运算符：</span><span class="sxs-lookup"><span data-stu-id="b762c-249">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="b762c-250">执行同步工作</span><span class="sxs-lookup"><span data-stu-id="b762c-250">Perform synchronous work</span></span>

<span data-ttu-id="b762c-251">如果不需要执行异步工作，框架将处理调用同步 `Invoke` 方法。</span><span class="sxs-lookup"><span data-stu-id="b762c-251">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="b762c-252">以下方法将创建同步 `Invoke` 视图组件：</span><span class="sxs-lookup"><span data-stu-id="b762c-252">The following method creates a synchronous `Invoke` view component:</span></span>

```csharp
public class PriorityList : ViewComponent
{
    public IViewComponentResult Invoke(int maxPriority, bool isDone)
    {
        var items = new List<string> { $"maxPriority: {maxPriority}", $"isDone: {isDone}" };
        return View(items);
    }
}
```

<span data-ttu-id="b762c-253">视图组件的 Razor 文件列出了传递给 `Invoke` 方法的字符串（Views/Home/Components/PriorityList/Default.cshtml）：</span><span class="sxs-lookup"><span data-stu-id="b762c-253">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

```cshtml
@model List<string>

<h3>Priority Items</h3>
<ul>
    @foreach (var item in Model)
    {
        <li>@item</li>
    }
</ul>
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b762c-254">使用以下方法之一在 Razor 文件（例如，Views/Home/Index.cshtml）中调用视图组件：</span><span class="sxs-lookup"><span data-stu-id="b762c-254">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="b762c-255">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="b762c-255">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="b762c-256">若要使用 <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 方法，请调用 `Component.InvokeAsync`：</span><span class="sxs-lookup"><span data-stu-id="b762c-256">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="b762c-257">使用 <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 在 Razor 文件（例如，Views/Home/Index.cshtml）中调用视图组件。</span><span class="sxs-lookup"><span data-stu-id="b762c-257">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="b762c-258">调用 `Component.InvokeAsync`：</span><span class="sxs-lookup"><span data-stu-id="b762c-258">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="b762c-259">若要使用标记帮助程序，请使用 `@addTagHelper` 指令注册包含视图组件的程序集（视图组件位于名为 `MyWebApp` 的程序集中）：</span><span class="sxs-lookup"><span data-stu-id="b762c-259">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="b762c-260">在 Razor 标记文件中使用视图组件标记帮助程序：</span><span class="sxs-lookup"><span data-stu-id="b762c-260">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="b762c-261">`PriorityList.Invoke` 的方法签名是同步的，但 Razor 在标记文件中使用 `Component.InvokeAsync` 找到并调用该方法。</span><span class="sxs-lookup"><span data-stu-id="b762c-261">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b762c-262">其他资源</span><span class="sxs-lookup"><span data-stu-id="b762c-262">Additional resources</span></span>

* [<span data-ttu-id="b762c-263">视图中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="b762c-263">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
