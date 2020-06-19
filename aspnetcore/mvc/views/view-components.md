---
title: ASP.NET Core 中的视图组件
author: rick-anderson
description: 了解如何在 ASP.NET Core 中使用视图组件，以及如何将其添加到应用中。
ms.author: riande
ms.custom: mvc
ms.date: 12/18/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/view-components
ms.openlocfilehash: 8e97dc69ef167b5c08522c91691e0aded9f56908
ms.sourcegitcommit: 490434a700ba8c5ed24d849bd99d8489858538e3
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/19/2020
ms.locfileid: "85102927"
---
# <a name="view-components-in-aspnet-core"></a><span data-ttu-id="9e4a2-103">ASP.NET Core 中的视图组件</span><span class="sxs-lookup"><span data-stu-id="9e4a2-103">View components in ASP.NET Core</span></span>

<span data-ttu-id="9e4a2-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="9e4a2-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="9e4a2-105">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="9e4a2-105">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="view-components"></a><span data-ttu-id="9e4a2-106">视图组件</span><span class="sxs-lookup"><span data-stu-id="9e4a2-106">View components</span></span>

<span data-ttu-id="9e4a2-107">视图组件与分部视图类似，但它们的功能更加强大。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-107">View components are similar to partial views, but they're much more powerful.</span></span> <span data-ttu-id="9e4a2-108">视图组件不使用模型绑定，并且仅依赖调用时提供的数据。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-108">View components don't use model binding, and only depend on the data provided when calling into it.</span></span> <span data-ttu-id="9e4a2-109">本文是使用控制器和视图编写的，但视图组件也适用于 Razor 页。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-109">This article was written using controllers and views, but view components also work with Razor Pages.</span></span>

<span data-ttu-id="9e4a2-110">视图组件：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-110">A view component:</span></span>

* <span data-ttu-id="9e4a2-111">呈现一个区块而不是整个响应。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-111">Renders a chunk rather than a whole response.</span></span>
* <span data-ttu-id="9e4a2-112">包括控制器和视图间发现的相同关注点分离和可测试性优势。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-112">Includes the same separation-of-concerns and testability benefits found between a controller and view.</span></span>
* <span data-ttu-id="9e4a2-113">可以有参数和业务逻辑。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-113">Can have parameters and business logic.</span></span>
* <span data-ttu-id="9e4a2-114">通常从布局页调用。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-114">Is typically invoked from a layout page.</span></span>

<span data-ttu-id="9e4a2-115">视图组件可用于具有可重用呈现逻辑（对分部视图来说过于复杂）的任何位置，例如：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-115">View components are intended anywhere you have reusable rendering logic that's too complex for a partial view, such as:</span></span>

* <span data-ttu-id="9e4a2-116">动态导航菜单</span><span class="sxs-lookup"><span data-stu-id="9e4a2-116">Dynamic navigation menus</span></span>
* <span data-ttu-id="9e4a2-117">标记云（查询数据库的位置）</span><span class="sxs-lookup"><span data-stu-id="9e4a2-117">Tag cloud (where it queries the database)</span></span>
* <span data-ttu-id="9e4a2-118">登录面板</span><span class="sxs-lookup"><span data-stu-id="9e4a2-118">Login panel</span></span>
* <span data-ttu-id="9e4a2-119">购物车</span><span class="sxs-lookup"><span data-stu-id="9e4a2-119">Shopping cart</span></span>
* <span data-ttu-id="9e4a2-120">最近发布的文章</span><span class="sxs-lookup"><span data-stu-id="9e4a2-120">Recently published articles</span></span>
* <span data-ttu-id="9e4a2-121">典型博客上的边栏内容</span><span class="sxs-lookup"><span data-stu-id="9e4a2-121">Sidebar content on a typical blog</span></span>
* <span data-ttu-id="9e4a2-122">一个登录面板，呈现在每页上并显示注销或登录链接，具体取决于用户的登录状态</span><span class="sxs-lookup"><span data-stu-id="9e4a2-122">A login panel that would be rendered on every page and show either the links to log out or log in, depending on the log in state of the user</span></span>

<span data-ttu-id="9e4a2-123">视图组件由两部分组成：类（通常派生自 [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)）及其返回的结果（通常为视图）。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-123">A view component consists of two parts: the class (typically derived from [ViewComponent](/dotnet/api/microsoft.aspnetcore.mvc.viewcomponent)) and the result it returns (typically a view).</span></span> <span data-ttu-id="9e4a2-124">与控制器一样，视图组件也可以是 POCO，但大多数开发人员都希望利用派生自 `ViewComponent` 的可用方法和属性。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-124">Like controllers, a view component can be a POCO, but most developers will want to take advantage of the methods and properties available by deriving from `ViewComponent`.</span></span>

<span data-ttu-id="9e4a2-125">考虑视图组件是否满足应用的规范时，请考虑 Razor 改用组件。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-125">When considering if view components meet an app's specifications, consider using Razor Components instead.</span></span> Razor<span data-ttu-id="9e4a2-126">组件还将标记与 c # 代码结合起来，以生成可重用的 UI 单元。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-126"> Components also combine markup with C# code to produce reusable UI units.</span></span> Razor<span data-ttu-id="9e4a2-127">在提供客户端 UI 逻辑和组合时，组件旨在提高开发人员的工作效率。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-127"> Components are designed for developer productivity when providing client-side UI logic and composition.</span></span> <span data-ttu-id="9e4a2-128">有关详细信息，请参阅 <xref:blazor/components/index>。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-128">For more information, see <xref:blazor/components/index>.</span></span>

## <a name="creating-a-view-component"></a><span data-ttu-id="9e4a2-129">创建视图组件</span><span class="sxs-lookup"><span data-stu-id="9e4a2-129">Creating a view component</span></span>

<span data-ttu-id="9e4a2-130">本部分包含创建视图组件的高级别要求。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-130">This section contains the high-level requirements to create a view component.</span></span> <span data-ttu-id="9e4a2-131">本文后续部分将详细检查每个步骤并创建视图组件。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-131">Later in the article, we'll examine each step in detail and create a view component.</span></span>

### <a name="the-view-component-class"></a><span data-ttu-id="9e4a2-132">视图组件类</span><span class="sxs-lookup"><span data-stu-id="9e4a2-132">The view component class</span></span>

<span data-ttu-id="9e4a2-133">可通过以下任一方法创建视图组件类：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-133">A view component class can be created by any of the following:</span></span>

* <span data-ttu-id="9e4a2-134">从 ViewComponent 派生\*\*</span><span class="sxs-lookup"><span data-stu-id="9e4a2-134">Deriving from *ViewComponent*</span></span>
* <span data-ttu-id="9e4a2-135">使用 `[ViewComponent]` 属性修饰类，或者从具有 `[ViewComponent]` 属性的类派生</span><span class="sxs-lookup"><span data-stu-id="9e4a2-135">Decorating a class with the `[ViewComponent]` attribute, or deriving from a class with the `[ViewComponent]` attribute</span></span>
* <span data-ttu-id="9e4a2-136">创建名称以 ViewComponent 后缀结尾的类\*\*</span><span class="sxs-lookup"><span data-stu-id="9e4a2-136">Creating a class where the name ends with the suffix *ViewComponent*</span></span>

<span data-ttu-id="9e4a2-137">与控制器一样，视图组件必须是公共、非嵌套和非抽象的类。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-137">Like controllers, view components must be public, non-nested, and non-abstract classes.</span></span> <span data-ttu-id="9e4a2-138">视图组件名称是删除了“ViewComponent”后缀的类名。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-138">The view component name is the class name with the "ViewComponent" suffix removed.</span></span> <span data-ttu-id="9e4a2-139">也可以使用 `ViewComponentAttribute.Name` 属性显式指定它。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-139">It can also be explicitly specified using the `ViewComponentAttribute.Name` property.</span></span>

<span data-ttu-id="9e4a2-140">视图组件类：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-140">A view component class:</span></span>

* <span data-ttu-id="9e4a2-141">完全支持构造函数[依赖关系注入](../../fundamentals/dependency-injection.md)</span><span class="sxs-lookup"><span data-stu-id="9e4a2-141">Fully supports constructor [dependency injection](../../fundamentals/dependency-injection.md)</span></span>

* <span data-ttu-id="9e4a2-142">不参与控制器生命周期，这意味着不能在视图组件中使用[筛选器](../controllers/filters.md)</span><span class="sxs-lookup"><span data-stu-id="9e4a2-142">Doesn't take part in the controller lifecycle, which means you can't use [filters](../controllers/filters.md) in a view component</span></span>

### <a name="view-component-methods"></a><span data-ttu-id="9e4a2-143">视图组件方法</span><span class="sxs-lookup"><span data-stu-id="9e4a2-143">View component methods</span></span>

<span data-ttu-id="9e4a2-144">视图组件以返回 `Task<IViewComponentResult>` 的 `InvokeAsync` 方法，或是以返回 `IViewComponentResult` 的同步 `Invoke` 方法定义其逻辑。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-144">A view component defines its logic in an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or in a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span> <span data-ttu-id="9e4a2-145">参数直接来自视图组件的调用，而不是来自模型绑定。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-145">Parameters come directly from invocation of the view component, not from model binding.</span></span> <span data-ttu-id="9e4a2-146">视图组件从不直接处理请求。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-146">A view component never directly handles a request.</span></span> <span data-ttu-id="9e4a2-147">通常，视图组件通过调用 `View` 方法来初始化模型并将其传递到视图。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-147">Typically, a view component initializes a model and passes it to a view by calling the `View` method.</span></span> <span data-ttu-id="9e4a2-148">总之，视图组件方法：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-148">In summary, view component methods:</span></span>

* <span data-ttu-id="9e4a2-149">定义返回 `Task<IViewComponentResult>` 的 `InvokeAsync` 方法，或是返回 `IViewComponentResult` 的同步 `Invoke` 方法。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-149">Define an `InvokeAsync` method that returns a `Task<IViewComponentResult>` or a synchronous `Invoke` method that returns an `IViewComponentResult`.</span></span>
* <span data-ttu-id="9e4a2-150">通常通过调用方法来初始化模型并将其传递给视图 `ViewComponent` `View` 。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-150">Typically initializes a model and passes it to a view by calling the `ViewComponent` `View` method.</span></span>
* <span data-ttu-id="9e4a2-151">参数来自调用方法，而不是 HTTP。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-151">Parameters come from the calling method, not HTTP.</span></span> <span data-ttu-id="9e4a2-152">没有模型绑定。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-152">There's no model binding.</span></span>
* <span data-ttu-id="9e4a2-153">不可直接作为 HTTP 终结点进行访问。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-153">Are not reachable directly as an HTTP endpoint.</span></span> <span data-ttu-id="9e4a2-154">通过代码调用它们（通常在视图中）。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-154">They're invoked from your code (usually in a view).</span></span> <span data-ttu-id="9e4a2-155">视图组件从不处理请求。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-155">A view component never handles a request.</span></span>
* <span data-ttu-id="9e4a2-156">在签名上重载，而不是当前 HTTP 请求的任何详细信息。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-156">Are overloaded on the signature rather than any details from the current HTTP request.</span></span>

### <a name="view-search-path"></a><span data-ttu-id="9e4a2-157">视图搜索路径</span><span class="sxs-lookup"><span data-stu-id="9e4a2-157">View search path</span></span>

<span data-ttu-id="9e4a2-158">运行时在以下路径中搜索视图：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-158">The runtime searches for the view in the following paths:</span></span>

* <span data-ttu-id="9e4a2-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="9e4a2-159">/Views/{Controller Name}/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="9e4a2-160">/Views/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="9e4a2-160">/Views/Shared/Components/{View Component Name}/{View Name}</span></span>
* <span data-ttu-id="9e4a2-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span><span class="sxs-lookup"><span data-stu-id="9e4a2-161">/Pages/Shared/Components/{View Component Name}/{View Name}</span></span>

<span data-ttu-id="9e4a2-162">搜索路径适用于使用控制器 + 视图和页的项目 Razor 。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-162">The search path applies to projects using controllers + views and Razor Pages.</span></span>

<span data-ttu-id="9e4a2-163">视图组件的默认视图名称为“默认”，这意味着视图文件通常命名为“Default.cshtml”\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-163">The default view name for a view component is *Default*, which means your view file will typically be named *Default.cshtml*.</span></span> <span data-ttu-id="9e4a2-164">可以在创建视图组件结果或调用 `View` 方法时指定不同的视图名称。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-164">You can specify a different view name when creating the view component result or when calling the `View` method.</span></span>

<span data-ttu-id="9e4a2-165">建议将视图文件命名为 Default.cshtml 并使用 Views/Shared/Components/{View Component Name}/{View Name} 路径。\*\*\*\*</span><span class="sxs-lookup"><span data-stu-id="9e4a2-165">We recommend you name the view file *Default.cshtml* and use the *Views/Shared/Components/{View Component Name}/{View Name}* path.</span></span> <span data-ttu-id="9e4a2-166">此示例中使用的 `PriorityList` 视图组件对视图组件视图使用 Views/Shared/Components/PriorityList/Default.cshtml\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-166">The `PriorityList` view component used in this sample uses *Views/Shared/Components/PriorityList/Default.cshtml* for the view component view.</span></span>

### <a name="customize-the-view-search-path"></a><span data-ttu-id="9e4a2-167">自定义视图搜索路径</span><span class="sxs-lookup"><span data-stu-id="9e4a2-167">Customize the view search path</span></span>

<span data-ttu-id="9e4a2-168">若要自定义视图搜索路径，请修改 Razor 的 <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> 集合。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-168">To customize the view search path, modify Razor's <xref:Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions.ViewLocationFormats> collection.</span></span> <span data-ttu-id="9e4a2-169">例如，将新项添加到集合，以搜索路径“/Components/{视图组件名称}/{视图名称}”中的视图：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-169">For example, to search for views within the path "/Components/{View Component Name}/{View Name}", add a new item to the collection:</span></span>

[!code-cs[](view-components/samples_snapshot/2.x/Startup.cs?name=snippet_ViewLocationFormats&highlight=4)]

<span data-ttu-id="9e4a2-170">在前面的代码中，占位符“{0}”表示路径“Components/{视图组件名称}/{视图名称}”。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-170">In the preceding code, the placeholder "{0}" represents the path "Components/{View Component Name}/{View Name}".</span></span>

## <a name="invoking-a-view-component"></a><span data-ttu-id="9e4a2-171">调用视图组件</span><span class="sxs-lookup"><span data-stu-id="9e4a2-171">Invoking a view component</span></span>

<span data-ttu-id="9e4a2-172">要使用视图组件，请在视图中调用以下内容：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-172">To use the view component, call the following inside a view:</span></span>

```cshtml
@await Component.InvokeAsync("Name of view component", {Anonymous Type Containing Parameters})
```

<span data-ttu-id="9e4a2-173">参数将传递给 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-173">The parameters will be passed to the `InvokeAsync` method.</span></span> <span data-ttu-id="9e4a2-174">本文中开发的 `PriorityList` 视图组件调用自 Views/ToDo/Index.cshtml\*\* 视图文件。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-174">The `PriorityList` view component developed in the article is invoked from the *Views/ToDo/Index.cshtml* view file.</span></span> <span data-ttu-id="9e4a2-175">在下例中，使用两个参数调用 `InvokeAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-175">In the following, the `InvokeAsync` method is called with two parameters:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

::: moniker range=">= aspnetcore-1.1"

## <a name="invoking-a-view-component-as-a-tag-helper"></a><span data-ttu-id="9e4a2-176">调用视图组件作为标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="9e4a2-176">Invoking a view component as a Tag Helper</span></span>

<span data-ttu-id="9e4a2-177">对于 ASP.NET Core 1.1 及更高版本，可以调用视图组件作为[标记帮助程序](xref:mvc/views/tag-helpers/intro)：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-177">For ASP.NET Core 1.1 and higher, you can invoke a view component as a [Tag Helper](xref:mvc/views/tag-helpers/intro):</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="9e4a2-178">标记帮助程序采用 Pascal 大小写格式的类和方法参数将转换为各自相应的[短横线格式](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101)。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-178">Pascal-cased class and method parameters for Tag Helpers are translated into their [kebab case](https://stackoverflow.com/questions/11273282/whats-the-name-for-dash-separated-case/12273101).</span></span> <span data-ttu-id="9e4a2-179">要调用视图组件的标记帮助程序使用 `<vc></vc>` 元素。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-179">The Tag Helper to invoke a view component uses the `<vc></vc>` element.</span></span> <span data-ttu-id="9e4a2-180">按如下方式指定视图组件：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-180">The view component is specified as follows:</span></span>

```cshtml
<vc:[view-component-name]
  parameter1="parameter1 value"
  parameter2="parameter2 value">
</vc:[view-component-name]>
```

<span data-ttu-id="9e4a2-181">若要将视图组件用作标记帮助程序，请使用 `@addTagHelper` 指令注册包含视图组件的程序集。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-181">To use a view component as a Tag Helper, register the assembly containing the view component using the `@addTagHelper` directive.</span></span> <span data-ttu-id="9e4a2-182">如果视图组件位于名为“`MyWebApp`”的程序集中，请将以下指令添加到 _ViewImports.cshtml\*\* 文件：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-182">If your view component is in an assembly called `MyWebApp`, add the following directive to the *_ViewImports.cshtml* file:</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="9e4a2-183">可将视图组件作为标记帮助程序注册到任何引用视图组件的文件。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-183">You can register a view component as a Tag Helper to any file that references the view component.</span></span> <span data-ttu-id="9e4a2-184">要详细了解如何注册标记帮助程序，请参阅[管理标记帮助程序作用域](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope)。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-184">See [Managing Tag Helper Scope](xref:mvc/views/tag-helpers/intro#managing-tag-helper-scope) for more information on how to register Tag Helpers.</span></span>

<span data-ttu-id="9e4a2-185">本教程中使用的 `InvokeAsync` 方法：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-185">The `InvokeAsync` method used in this tutorial:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="9e4a2-186">在标记帮助程序标记中：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-186">In Tag Helper markup:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexTagHelper.cshtml?range=37-38)]

<span data-ttu-id="9e4a2-187">在以上示例中，`PriorityList` 视图组件变为 `priority-list`。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-187">In the sample above, the `PriorityList` view component becomes `priority-list`.</span></span> <span data-ttu-id="9e4a2-188">视图组件的参数作为短横线格式的属性进行传递。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-188">The parameters to the view component are passed as attributes in kebab case.</span></span>

::: moniker-end

### <a name="invoking-a-view-component-directly-from-a-controller"></a><span data-ttu-id="9e4a2-189">从控制器直接调用视图组件</span><span class="sxs-lookup"><span data-stu-id="9e4a2-189">Invoking a view component directly from a controller</span></span>

<span data-ttu-id="9e4a2-190">视图组件通常从视图调用，但你可以直接从控制器方法调用它们。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-190">View components are typically invoked from a view, but you can invoke them directly from a controller method.</span></span> <span data-ttu-id="9e4a2-191">尽管视图组件不定义控制器等终结点，但你可以轻松实现返回 `ViewComponentResult` 内容的控制器操作。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-191">While view components don't define endpoints like controllers, you can easily implement a controller action that returns the content of a `ViewComponentResult`.</span></span>

<span data-ttu-id="9e4a2-192">在此示例中，视图组件直接从控制器调用：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-192">In this example, the view component is called directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

## <a name="walkthrough-creating-a-simple-view-component"></a><span data-ttu-id="9e4a2-193">演练：创建简单的视图组件</span><span class="sxs-lookup"><span data-stu-id="9e4a2-193">Walkthrough: Creating a simple view component</span></span>

<span data-ttu-id="9e4a2-194">[下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample)、生成和测试起始代码。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-194">[Download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/views/view-components/sample), build and test the starter code.</span></span> <span data-ttu-id="9e4a2-195">它是一个带有 `ToDo` 控制器的简单项目，该控制器显示 ToDo\*\* 项的列表。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-195">It's a simple project with a `ToDo` controller that displays a list of *ToDo* items.</span></span>

![ToDo 列表](view-components/_static/2dos.png)

### <a name="add-a-viewcomponent-class"></a><span data-ttu-id="9e4a2-197">添加 ViewComponent 类</span><span class="sxs-lookup"><span data-stu-id="9e4a2-197">Add a ViewComponent class</span></span>

<span data-ttu-id="9e4a2-198">创建一个 ViewComponents 文件夹并添加以下 `PriorityListViewComponent` 类\*\*：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-198">Create a *ViewComponents* folder and add the following `PriorityListViewComponent` class:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponent1.cs?name=snippet1)]

<span data-ttu-id="9e4a2-199">代码说明：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-199">Notes on the code:</span></span>

* <span data-ttu-id="9e4a2-200">视图组件类可以包含在项目的任意文件夹中\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-200">View component classes can be contained in **any** folder in the project.</span></span>
* <span data-ttu-id="9e4a2-201">因为类名 PriorityListViewComponent 以后缀 ViewComponent 结尾，所以运行时将在从视图引用类组件时使用字符串“PriorityList”\*\*\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-201">Because the class name PriorityList**ViewComponent** ends with the suffix **ViewComponent**, the runtime will use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="9e4a2-202">我稍后将进行详细解释。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-202">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="9e4a2-203">`[ViewComponent]` 属性可以更改用于引用视图组件的名称。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-203">The `[ViewComponent]` attribute can change the name used to reference a view component.</span></span> <span data-ttu-id="9e4a2-204">例如，我们可以将类命名为 `XYZ` 并应用 `ViewComponent` 属性：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-204">For example, we could've named the class `XYZ` and applied the `ViewComponent` attribute:</span></span>

  ```csharp
  [ViewComponent(Name = "PriorityList")]
     public class XYZ : ViewComponent
     ```

* <span data-ttu-id="9e4a2-205">上面的 `[ViewComponent]` 属性通知视图组件选择器在查找与组件相关联的视图时使用名称 `PriorityList`，以及在从视图引用类组件时使用字符串“PriorityList”。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-205">The `[ViewComponent]` attribute above tells the view component selector to use the name `PriorityList` when looking for the views associated with the component, and to use the string "PriorityList" when referencing the class component from a view.</span></span> <span data-ttu-id="9e4a2-206">我稍后将进行详细解释。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-206">I'll explain that in more detail later.</span></span>
* <span data-ttu-id="9e4a2-207">组件使用[依赖关系注入](../../fundamentals/dependency-injection.md)以使数据上下文可用。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-207">The component uses [dependency injection](../../fundamentals/dependency-injection.md) to make the data context available.</span></span>
* <span data-ttu-id="9e4a2-208">`InvokeAsync` 公开可以从视图调用的方法，且可以采用任意数量的参数。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-208">`InvokeAsync` exposes a method which can be called from a view, and it can take an arbitrary number of arguments.</span></span>
* <span data-ttu-id="9e4a2-209">`InvokeAsync` 方法返回满足 `isDone` 和 `maxPriority` 参数的 `ToDo` 项集。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-209">The `InvokeAsync` method returns the set of `ToDo` items that satisfy the `isDone` and `maxPriority` parameters.</span></span>

### <a name="create-the-view-component-razor-view"></a><span data-ttu-id="9e4a2-210">创建视图组件 Razor 视图</span><span class="sxs-lookup"><span data-stu-id="9e4a2-210">Create the view component Razor view</span></span>

* <span data-ttu-id="9e4a2-211">创建 Views/Shared/Components 文件夹\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-211">Create the *Views/Shared/Components* folder.</span></span> <span data-ttu-id="9e4a2-212">此文件夹 **必须** 命名为 *Components*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-212">This folder **must** be named *Components*.</span></span>

* <span data-ttu-id="9e4a2-213">创建 Views/Shared/Components/PriorityList 文件夹\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-213">Create the *Views/Shared/Components/PriorityList* folder.</span></span> <span data-ttu-id="9e4a2-214">此文件夹名称必须与视图组件类的名称或类名去掉后缀（如果遵照约定并在类名中使用了“ViewComponent”后缀）的名称相匹配\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-214">This folder name must match the name of the view component class, or the name of the class minus the suffix (if we followed convention and used the *ViewComponent* suffix in the class name).</span></span> <span data-ttu-id="9e4a2-215">如果使用了 `ViewComponent` 属性，则类名称需要匹配指定的属性。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-215">If you used the `ViewComponent` attribute, the class name would need to match the attribute designation.</span></span>

* <span data-ttu-id="9e4a2-216">创建*Views/Shared/Components/PriorityList/Default ...* Razor view：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-216">Create a *Views/Shared/Components/PriorityList/Default.cshtml* Razor view:</span></span>


  [!code-cshtml[](view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/Default1.cshtml)]

   <span data-ttu-id="9e4a2-217">Razor视图采用列表 `TodoItem` ，并显示它们。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-217">The Razor view takes a list of `TodoItem` and displays them.</span></span> <span data-ttu-id="9e4a2-218">如果视图组件 `InvokeAsync` 方法不传递视图名称（如示例中所示），则按照约定使用“默认”作为视图名称\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-218">If the view component `InvokeAsync` method doesn't pass the name of the view (as in our sample), *Default* is used for the view name by convention.</span></span> <span data-ttu-id="9e4a2-219">在本教程后面部分，我将演示如何传递视图名称。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-219">Later in the tutorial, I'll show you how to pass the name of the view.</span></span> <span data-ttu-id="9e4a2-220">要替代特定控制器的默认样式，请将视图添加到控制器特定的视图文件夹（例如 Views/ToDo/Components/PriorityList/Default.cshtml\*\*）。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-220">To override the default styling for a specific controller, add a view to the controller-specific view folder (for example *Views/ToDo/Components/PriorityList/Default.cshtml)*.</span></span>

    <span data-ttu-id="9e4a2-221">如果视图组件是控制器特定的，则可将其添加到控制器特定的文件夹 (Views/ToDo/Components/PriorityList/Default.cshtml\*\*)。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-221">If the view component is controller-specific, you can add it to the controller-specific folder (*Views/ToDo/Components/PriorityList/Default.cshtml*).</span></span>

* <span data-ttu-id="9e4a2-222">将包含优先级列表组件调用的 `div` 添加到 Views/ToDo/index.cshtml\*\* 文件底部：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-222">Add a `div` containing a call to the priority list component to the bottom of the *Views/ToDo/index.cshtml* file:</span></span>

    [!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFirst.cshtml?range=34-38)]

<span data-ttu-id="9e4a2-223">标记 `@await Component.InvokeAsync` 显示调用视图组件的语法。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-223">The markup `@await Component.InvokeAsync` shows the syntax for calling view components.</span></span> <span data-ttu-id="9e4a2-224">第一个参数是要调用的组件的名称。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-224">The first argument is the name of the component we want to invoke or call.</span></span> <span data-ttu-id="9e4a2-225">后续参数将传递给该组件。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-225">Subsequent parameters are passed to the component.</span></span> <span data-ttu-id="9e4a2-226">`InvokeAsync` 可以采用任意数量的参数。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-226">`InvokeAsync` can take an arbitrary number of arguments.</span></span>

<span data-ttu-id="9e4a2-227">测试应用。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-227">Test the app.</span></span> <span data-ttu-id="9e4a2-228">下图显示 ToDo 列表和优先级项：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-228">The following image shows the ToDo list and the priority items:</span></span>

![Todo 列表和优先级项](view-components/_static/pi.png)

<span data-ttu-id="9e4a2-230">也可直接从控制器调用视图组件：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-230">You can also call the view component directly from the controller:</span></span>

[!code-csharp[](view-components/sample/ViewCompFinal/Controllers/ToDoController.cs?name=snippet_IndexVC)]

![IndexVC 操作的优先级项](view-components/_static/indexvc.png)

### <a name="specifying-a-view-name"></a><span data-ttu-id="9e4a2-232">指定视图名称</span><span class="sxs-lookup"><span data-stu-id="9e4a2-232">Specifying a view name</span></span>

<span data-ttu-id="9e4a2-233">在某些情况下，复杂的视图组件可能需要指定非默认视图。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-233">A complex view component might need to specify a non-default view under some conditions.</span></span> <span data-ttu-id="9e4a2-234">以下代码显示如何从 `InvokeAsync` 方法指定“PVC”视图。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-234">The following code shows how to specify the "PVC" view  from the `InvokeAsync` method.</span></span> <span data-ttu-id="9e4a2-235">更新 `PriorityListViewComponent` 类中的 `InvokeAsync` 方法。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-235">Update the `InvokeAsync` method in the `PriorityListViewComponent` class.</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityListViewComponentFinal.cs?highlight=4,5,6,7,8,9&range=28-39)]

<span data-ttu-id="9e4a2-236">将 Views/Shared/Components/PriorityList/Default.cshtml 文件复制到名为 Views/Shared/Components/PriorityList/PVC.cshtml 的视图\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-236">Copy the *Views/Shared/Components/PriorityList/Default.cshtml* file to a view named *Views/Shared/Components/PriorityList/PVC.cshtml*.</span></span> <span data-ttu-id="9e4a2-237">添加标题以指示正在使用 PVC 视图。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-237">Add a heading to indicate the PVC view is being used.</span></span>

[!code-cshtml[](../../mvc/views/view-components/sample/ViewCompFinal/Views/Shared/Components/PriorityList/PVC.cshtml?highlight=3)]

<span data-ttu-id="9e4a2-238">更新 Views/ToDo/Index.cshtml\*\*：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-238">Update *Views/ToDo/Index.cshtml*:</span></span>

<!-- Views/ToDo/Index.cshtml is never imported, so change to test tutorial -->

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexFinal.cshtml?range=35)]

<span data-ttu-id="9e4a2-239">运行应用并验证 PVC 视图。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-239">Run the app and verify PVC view.</span></span>

![优先级视图组件](view-components/_static/pvc.png)

<span data-ttu-id="9e4a2-241">如果不呈现 PVC 视图，请验证是否调用优先级为 4 或更高的视图组件。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-241">If the PVC view isn't rendered, verify you are calling the view component with a priority of 4 or higher.</span></span>

### <a name="examine-the-view-path"></a><span data-ttu-id="9e4a2-242">检查视图路径</span><span class="sxs-lookup"><span data-stu-id="9e4a2-242">Examine the view path</span></span>

* <span data-ttu-id="9e4a2-243">将优先级参数更改为 3 或更低，从而不返回优先级视图。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-243">Change the priority parameter to three or less so the priority view isn't returned.</span></span>
* <span data-ttu-id="9e4a2-244">将 Views/ToDo/Components/PriorityList/Default.cshtml\*\* 暂时重命名为 1Default.cshtml\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-244">Temporarily rename the *Views/ToDo/Components/PriorityList/Default.cshtml* to *1Default.cshtml*.</span></span>
* <span data-ttu-id="9e4a2-245">测试应用，你将收到以下错误：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-245">Test the app, you'll get the following error:</span></span>

   ```
   An unhandled exception occurred while processing the request.
   InvalidOperationException: The view 'Components/PriorityList/Default' wasn't found. The following locations were searched:
   /Views/ToDo/Components/PriorityList/Default.cshtml
   /Views/Shared/Components/PriorityList/Default.cshtml
   EnsureSuccessful
   ```

* <span data-ttu-id="9e4a2-246">将 Views/ToDo/Components/PriorityList/1Default.cshtml\*\* 复制到 Views/Shared/Components/PriorityList/Default.cshtml\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-246">Copy *Views/ToDo/Components/PriorityList/1Default.cshtml* to *Views/Shared/Components/PriorityList/Default.cshtml*.</span></span>
* <span data-ttu-id="9e4a2-247">将一些标记添加到共享\*\* ToDo 视图组件视图，以指示视图来自“Shared”\*\* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-247">Add some markup to the *Shared* ToDo view component view to indicate the view is from the *Shared* folder.</span></span>
* <span data-ttu-id="9e4a2-248">测试“共享”组件视图\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-248">Test the **Shared** component view.</span></span>

![有共享组件视图的 ToDo 输出](view-components/_static/shared.png)

### <a name="avoiding-hard-coded-strings"></a><span data-ttu-id="9e4a2-250">避免使用硬编码字符串</span><span class="sxs-lookup"><span data-stu-id="9e4a2-250">Avoiding hard-coded strings</span></span>

<span data-ttu-id="9e4a2-251">若要确保编译时的安全性，可以用类名替换硬编码的视图组件名称。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-251">If you want compile time safety, you can replace the hard-coded view component name with the class name.</span></span> <span data-ttu-id="9e4a2-252">创建没有“ViewComponent”后缀的视图组件：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-252">Create the view component without the "ViewComponent" suffix:</span></span>

[!code-csharp[](../../mvc/views/view-components/sample/ViewCompFinal/ViewComponents/PriorityList.cs?highlight=10&range=5-35)]

<span data-ttu-id="9e4a2-253">向 `using` 视图文件添加语句 Razor ，并使用 `nameof` 运算符：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-253">Add a `using` statement to your Razor view file, and use the `nameof` operator:</span></span>

[!code-cshtml[](view-components/sample/ViewCompFinal/Views/ToDo/IndexNameof.cshtml?range=1-6,35-)]

## <a name="perform-synchronous-work"></a><span data-ttu-id="9e4a2-254">执行同步工作</span><span class="sxs-lookup"><span data-stu-id="9e4a2-254">Perform synchronous work</span></span>

<span data-ttu-id="9e4a2-255">如果不需要执行异步工作，框架将处理调用同步 `Invoke` 方法。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-255">The framework handles invoking a synchronous `Invoke` method if you don't need to perform asynchronous work.</span></span> <span data-ttu-id="9e4a2-256">以下方法将创建同步 `Invoke` 视图组件：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-256">The following method creates a synchronous `Invoke` view component:</span></span>

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

<span data-ttu-id="9e4a2-257">视图组件的 Razor 文件列出传递给方法的字符串 `Invoke` （*Views/Home/Components/PriorityList/* default.aspx）：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-257">The view component's Razor file lists the strings passed to the `Invoke` method (*Views/Home/Components/PriorityList/Default.cshtml*):</span></span>

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

<span data-ttu-id="9e4a2-258">Razor使用以下方法之一在文件中调用视图组件（例如， *Views/Home/Index*）：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-258">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) using one of the following approaches:</span></span>

* <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>
* [<span data-ttu-id="9e4a2-259">标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="9e4a2-259">Tag Helper</span></span>](xref:mvc/views/tag-helpers/intro)

<span data-ttu-id="9e4a2-260">若要使用 <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 方法，请调用 `Component.InvokeAsync`：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-260">To use the <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> approach, call `Component.InvokeAsync`:</span></span>

::: moniker-end

::: moniker range="< aspnetcore-1.1"

<span data-ttu-id="9e4a2-261">视图组件在文件中调用 Razor （例如， *Views/Home/Index*） <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper> 。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-261">The view component is invoked in a Razor file (for example, *Views/Home/Index.cshtml*) with <xref:Microsoft.AspNetCore.Mvc.IViewComponentHelper>.</span></span>

<span data-ttu-id="9e4a2-262">调用 `Component.InvokeAsync`：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-262">Call `Component.InvokeAsync`:</span></span>

::: moniker-end

```cshtml
@await Component.InvokeAsync(nameof(PriorityList), new { maxPriority = 4, isDone = true })
```

::: moniker range=">= aspnetcore-1.1"

<span data-ttu-id="9e4a2-263">若要使用标记帮助程序，请使用 `@addTagHelper` 指令注册包含视图组件的程序集（视图组件位于名为 `MyWebApp` 的程序集中）：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-263">To use the Tag Helper, register the assembly containing the View Component using the `@addTagHelper` directive (the view component is in an assembly called `MyWebApp`):</span></span>

```cshtml
@addTagHelper *, MyWebApp
```

<span data-ttu-id="9e4a2-264">使用标记文件中的 "查看组件标记帮助器" Razor ：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-264">Use the view component Tag Helper in the Razor markup file:</span></span>

```cshtml
<vc:priority-list max-priority="999" is-done="false">
</vc:priority-list>
```

::: moniker-end

<span data-ttu-id="9e4a2-265">的方法签名 `PriorityList.Invoke` 是同步的，但会 Razor `Component.InvokeAsync` 在标记文件中查找并调用方法。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-265">The method signature of `PriorityList.Invoke` is synchronous, but Razor finds and calls the method with `Component.InvokeAsync` in the markup file.</span></span>

## <a name="all-view-component-parameters-are-required"></a><span data-ttu-id="9e4a2-266">所有视图组件参数都是必需的</span><span class="sxs-lookup"><span data-stu-id="9e4a2-266">All view component parameters are required</span></span>

<span data-ttu-id="9e4a2-267">视图组件中的每个参数都是必需的属性。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-267">Each parameter in a view component is a required attribute.</span></span> <span data-ttu-id="9e4a2-268">请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/5011)。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-268">See [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/5011).</span></span> <span data-ttu-id="9e4a2-269">如果省略任何参数：</span><span class="sxs-lookup"><span data-stu-id="9e4a2-269">If any  parameter is omitted:</span></span>

* <span data-ttu-id="9e4a2-270">`InvokeAsync` 方法签名不匹配，因此该方法将不会执行。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-270">The `InvokeAsync` method signature won't match, therefore the method won't execute.</span></span>
* <span data-ttu-id="9e4a2-271">ViewComponent 不会呈现任何标记。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-271">The ViewComponent won't render any markup.</span></span>
* <span data-ttu-id="9e4a2-272">不会引发任何错误。</span><span class="sxs-lookup"><span data-stu-id="9e4a2-272">No errors will be thrown.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9e4a2-273">其他资源</span><span class="sxs-lookup"><span data-stu-id="9e4a2-273">Additional resources</span></span>

* [<span data-ttu-id="9e4a2-274">视图中的依赖关系注入</span><span class="sxs-lookup"><span data-stu-id="9e4a2-274">Dependency injection into views</span></span>](xref:mvc/views/dependency-injection)
