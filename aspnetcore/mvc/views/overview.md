---
title: ASP.NET Core MVC 中的视图
author: ardalis
description: 了解 ASP.NET Core MVC 中的视图如何处理应用的数据表示和用户交互。
ms.author: riande
ms.date: 12/05/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: mvc/views/overview
ms.openlocfilehash: 8630df0ad8ea556c6edf0ab251b3c86493f751e2
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88020868"
---
# <a name="views-in-aspnet-core-mvc"></a><span data-ttu-id="2e09a-103">ASP.NET Core MVC 中的视图</span><span class="sxs-lookup"><span data-stu-id="2e09a-103">Views in ASP.NET Core MVC</span></span>

<span data-ttu-id="2e09a-104">作者：[Steve Smith](https://ardalis.com/)</span><span class="sxs-lookup"><span data-stu-id="2e09a-104">By [Steve Smith](https://ardalis.com/)</span></span>

<span data-ttu-id="2e09a-105">本文档介绍在 ASP.NET Core MVC 应用程序中使用的视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-105">This document explains views used in ASP.NET Core MVC applications.</span></span> <span data-ttu-id="2e09a-106">有关页面的信息 Razor ，请参阅[ Razor 页面简介](xref:razor-pages/index)。</span><span class="sxs-lookup"><span data-stu-id="2e09a-106">For information on Razor Pages, see [Introduction to Razor Pages](xref:razor-pages/index).</span></span>

<span data-ttu-id="2e09a-107">在“模型-视图-控制器(MVC)”模式中，视图处理应用的数据表示和用户交互\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-107">In the Model-View-Controller (MVC) pattern, the *view* handles the app's data presentation and user interaction.</span></span> <span data-ttu-id="2e09a-108">视图是具有嵌入[ Razor 标记](xref:mvc/views/razor)的 HTML 模板。</span><span class="sxs-lookup"><span data-stu-id="2e09a-108">A view is an HTML template with embedded [Razor markup](xref:mvc/views/razor).</span></span> <span data-ttu-id="2e09a-109">Razor标记是与 HTML 标记交互以生成发送到客户端的网页的代码。</span><span class="sxs-lookup"><span data-stu-id="2e09a-109">Razor markup is code that interacts with HTML markup to produce a webpage that's sent to the client.</span></span>

<span data-ttu-id="2e09a-110">在 ASP.NET Core MVC 中，视图是在标记中使用[c # 编程语言](/dotnet/csharp/)的*cshtml*文件。 Razor</span><span class="sxs-lookup"><span data-stu-id="2e09a-110">In ASP.NET Core MVC, views are *.cshtml* files that use the [C# programming language](/dotnet/csharp/) in Razor markup.</span></span> <span data-ttu-id="2e09a-111">通常，视图文件会分组到以每个应用的[控制器](xref:mvc/controllers/actions)命名的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="2e09a-111">Usually, view files are grouped into folders named for each of the app's [controllers](xref:mvc/controllers/actions).</span></span> <span data-ttu-id="2e09a-112">此文件夹存储在应用根目录的“Views”文件夹中\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-112">The folders are stored in a *Views* folder at the root of the app:</span></span>

![Visual Studio 解决方案资源管理器中的“Views”文件夹与“Home”文件夹一同打开，显示 About.cshtml、Contact.cshtml 和 Index.cshtml 文件](overview/_static/views_solution_explorer.png)

<span data-ttu-id="2e09a-114">主页控制器由“Views”文件夹内的“Home”文件夹表示\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-114">The *Home* controller is represented by a *Home* folder inside the *Views* folder.</span></span> <span data-ttu-id="2e09a-115">“Home”文件夹包含“关于”、“联系人”和“索引”（主页）网页的视图\*\*\*\*\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-115">The *Home* folder contains the views for the *About*, *Contact*, and *Index* (homepage) webpages.</span></span> <span data-ttu-id="2e09a-116">用户请求这三个网页中的一个时，主页控制器中的控制器操作决定使用三个视图中的哪一个来生成网页并将其返回给用户\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-116">When a user requests one of these three webpages, controller actions in the *Home* controller determine which of the three views is used to build and return a webpage to the user.</span></span>

<span data-ttu-id="2e09a-117">使用[布局](xref:mvc/views/layout)提供一致的网页部分并减少代码重复。</span><span class="sxs-lookup"><span data-stu-id="2e09a-117">Use [layouts](xref:mvc/views/layout) to provide consistent webpage sections and reduce code repetition.</span></span> <span data-ttu-id="2e09a-118">布局通常包含页眉、导航和菜单元素以及页脚。</span><span class="sxs-lookup"><span data-stu-id="2e09a-118">Layouts often contain the header, navigation and menu elements, and the footer.</span></span> <span data-ttu-id="2e09a-119">页眉和页脚通常包含许多元数据元素的样板标记以及脚本和样式资产的链接。</span><span class="sxs-lookup"><span data-stu-id="2e09a-119">The header and footer usually contain boilerplate markup for many metadata elements and links to script and style assets.</span></span> <span data-ttu-id="2e09a-120">布局有助于在视图中避免这种样板标记。</span><span class="sxs-lookup"><span data-stu-id="2e09a-120">Layouts help you avoid this boilerplate markup in your views.</span></span>

<span data-ttu-id="2e09a-121">[分部视图](xref:mvc/views/partial)通过管理视图的可重用部分来减少代码重复。</span><span class="sxs-lookup"><span data-stu-id="2e09a-121">[Partial views](xref:mvc/views/partial) reduce code duplication by managing reusable parts of views.</span></span> <span data-ttu-id="2e09a-122">例如，分部视图可用于在多个视图中出现的博客网站上的作者简介。</span><span class="sxs-lookup"><span data-stu-id="2e09a-122">For example, a partial view is useful for an author biography on a blog website that appears in several views.</span></span> <span data-ttu-id="2e09a-123">作者简介是普通的视图内容，不需要执行代码就能生成网页的内容。</span><span class="sxs-lookup"><span data-stu-id="2e09a-123">An author biography is ordinary view content and doesn't require code to execute in order to produce the content for the webpage.</span></span> <span data-ttu-id="2e09a-124">可以仅通过模型绑定查看作者简介内容，因此使用这种内容类型的分部视图是理想的选择。</span><span class="sxs-lookup"><span data-stu-id="2e09a-124">Author biography content is available to the view by model binding alone, so using a partial view for this type of content is ideal.</span></span>

<span data-ttu-id="2e09a-125">[视图组件](xref:mvc/views/view-components)与分部视图的相似之处在于它们可以减少重复性代码，但视图组件还适用于需要在服务器上运行代码才能呈现网页的视图内容。</span><span class="sxs-lookup"><span data-stu-id="2e09a-125">[View components](xref:mvc/views/view-components) are similar to partial views in that they allow you to reduce repetitive code, but they're appropriate for view content that requires code to run on the server in order to render the webpage.</span></span> <span data-ttu-id="2e09a-126">呈现的内容需要数据库交互时（例如网站购物车），视图组件非常有用。</span><span class="sxs-lookup"><span data-stu-id="2e09a-126">View components are useful when the rendered content requires database interaction, such as for a website shopping cart.</span></span> <span data-ttu-id="2e09a-127">为了生成网页输出，视图组件不局限于模型绑定。</span><span class="sxs-lookup"><span data-stu-id="2e09a-127">View components aren't limited to model binding in order to produce webpage output.</span></span>

## <a name="benefits-of-using-views"></a><span data-ttu-id="2e09a-128">使用视图的好处</span><span class="sxs-lookup"><span data-stu-id="2e09a-128">Benefits of using views</span></span>

<span data-ttu-id="2e09a-129">视图可帮助在 MVC 应用内建立[关注点分离](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns)，方法是分隔用户界面标记与应用的其他部分。</span><span class="sxs-lookup"><span data-stu-id="2e09a-129">Views help to establish [separation of concerns](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#separation-of-concerns) within an MVC app by separating the user interface markup from other parts of the app.</span></span> <span data-ttu-id="2e09a-130">采用 SoC 设计可使应用模块化，从而提供以下几个好处：</span><span class="sxs-lookup"><span data-stu-id="2e09a-130">Following SoC design makes your app modular, which provides several benefits:</span></span>

* <span data-ttu-id="2e09a-131">应用组织地更好，因此更易于维护。</span><span class="sxs-lookup"><span data-stu-id="2e09a-131">The app is easier to maintain because it's better organized.</span></span> <span data-ttu-id="2e09a-132">视图一般按应用功能进行分组。</span><span class="sxs-lookup"><span data-stu-id="2e09a-132">Views are generally grouped by app feature.</span></span> <span data-ttu-id="2e09a-133">这使得在处理功能时更容易找到相关的视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-133">This makes it easier to find related views when working on a feature.</span></span>
* <span data-ttu-id="2e09a-134">应用的若干部分是松散耦合的。</span><span class="sxs-lookup"><span data-stu-id="2e09a-134">The parts of the app are loosely coupled.</span></span> <span data-ttu-id="2e09a-135">可以生成和更新独立于业务逻辑和数据访问组件的应用视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-135">You can build and update the app's views separately from the business logic and data access components.</span></span> <span data-ttu-id="2e09a-136">可以修改应用的视图，而不必更新应用的其他部分。</span><span class="sxs-lookup"><span data-stu-id="2e09a-136">You can modify the views of the app without necessarily having to update other parts of the app.</span></span>
* <span data-ttu-id="2e09a-137">因为视图是独立的单元，所以更容易测试应用的用户界面部分。</span><span class="sxs-lookup"><span data-stu-id="2e09a-137">It's easier to test the user interface parts of the app because the views are separate units.</span></span>
* <span data-ttu-id="2e09a-138">由于应用组织得更好，因此你不太可能会意外重复用户界面的各个部分。</span><span class="sxs-lookup"><span data-stu-id="2e09a-138">Due to better organization, it's less likely that you'll accidentally repeat sections of the user interface.</span></span>

## <a name="creating-a-view"></a><span data-ttu-id="2e09a-139">创建视图</span><span class="sxs-lookup"><span data-stu-id="2e09a-139">Creating a view</span></span>

<span data-ttu-id="2e09a-140">在 Views / [ControllerName] 文件夹中创建特定于控制器的视图\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-140">Views that are specific to a controller are created in the *Views/[ControllerName]* folder.</span></span> <span data-ttu-id="2e09a-141">控制器之间共享的视图都将置于 Views/Shared 文件夹\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-141">Views that are shared among controllers are placed in the *Views/Shared* folder.</span></span> <span data-ttu-id="2e09a-142">要创建一个视图，请添加新文件，并将其命名为与 .cshtml 文件扩展名相关联的控制器操作的相同名称\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-142">To create a view, add a new file and give it the same name as its associated controller action with the *.cshtml* file extension.</span></span> <span data-ttu-id="2e09a-143">要创建与主页控制器中 About 操作相对应的视图，请在 Views/Home 文件夹中创建一个 About.cshtml 文件\*\*\*\*\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-143">To create a view that corresponds with the *About* action in the *Home* controller, create an *About.cshtml* file in the *Views/Home* folder:</span></span>

[!code-cshtml[](../../common/samples/WebApplication1/Views/Home/About.cshtml)]

<span data-ttu-id="2e09a-144">*Razor* 标记以符号开头 `@` 。</span><span class="sxs-lookup"><span data-stu-id="2e09a-144">*Razor* markup starts with the `@` symbol.</span></span> <span data-ttu-id="2e09a-145">运行 c # 语句，方法是将 c # 代码放在由大括号 () 设置的[ Razor 代码块](xref:mvc/views/razor#razor-code-blocks)中 `{ ... }` 。</span><span class="sxs-lookup"><span data-stu-id="2e09a-145">Run C# statements by placing C# code within [Razor code blocks](xref:mvc/views/razor#razor-code-blocks) set off by curly braces (`{ ... }`).</span></span> <span data-ttu-id="2e09a-146">有关示例，请参阅上面显示的“About”到 `ViewData["Title"]` 的分配。</span><span class="sxs-lookup"><span data-stu-id="2e09a-146">For example, see the assignment of "About" to `ViewData["Title"]` shown above.</span></span> <span data-ttu-id="2e09a-147">只需用 `@` 符号来引用值，即可在 HTML 中显示这些值。</span><span class="sxs-lookup"><span data-stu-id="2e09a-147">You can display values within HTML by simply referencing the value with the `@` symbol.</span></span> <span data-ttu-id="2e09a-148">请参阅上面的 `<h2>` 和 `<h3>` 元素的内容。</span><span class="sxs-lookup"><span data-stu-id="2e09a-148">See the contents of the `<h2>` and `<h3>` elements above.</span></span>

<span data-ttu-id="2e09a-149">以上所示的视图内容只是呈现给用户的整个网页中的一部分。</span><span class="sxs-lookup"><span data-stu-id="2e09a-149">The view content shown above is only part of the entire webpage that's rendered to the user.</span></span> <span data-ttu-id="2e09a-150">其他视图文件中指定了页面布局的其余部分和视图的其他常见方面。</span><span class="sxs-lookup"><span data-stu-id="2e09a-150">The rest of the page's layout and other common aspects of the view are specified in other view files.</span></span> <span data-ttu-id="2e09a-151">要了解详细信息，请参阅[布局主题](xref:mvc/views/layout)。</span><span class="sxs-lookup"><span data-stu-id="2e09a-151">To learn more, see the [Layout topic](xref:mvc/views/layout).</span></span>

## <a name="how-controllers-specify-views"></a><span data-ttu-id="2e09a-152">控制器如何指定视图</span><span class="sxs-lookup"><span data-stu-id="2e09a-152">How controllers specify views</span></span>

<span data-ttu-id="2e09a-153">视图通常以 [ ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult) 的形式从操作返回，这是一种 [ ActionResult ](/dotnet/api/microsoft.aspnetcore.mvc.actionresult)。</span><span class="sxs-lookup"><span data-stu-id="2e09a-153">Views are typically returned from actions as a [ViewResult](/dotnet/api/microsoft.aspnetcore.mvc.viewresult), which is a type of [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult).</span></span> <span data-ttu-id="2e09a-154">操作方法可以直接创建并返回 `ViewResult`，但通常不会这样做。</span><span class="sxs-lookup"><span data-stu-id="2e09a-154">Your action method can create and return a `ViewResult` directly, but that isn't commonly done.</span></span> <span data-ttu-id="2e09a-155">由于大多数控制器均继承自[控制器](/dotnet/api/microsoft.aspnetcore.mvc.controller)，因此只需使用 `View` 帮助程序方法即可返回 `ViewResult`：</span><span class="sxs-lookup"><span data-stu-id="2e09a-155">Since most controllers inherit from [Controller](/dotnet/api/microsoft.aspnetcore.mvc.controller), you simply use the `View` helper method to return the `ViewResult`:</span></span>

<span data-ttu-id="2e09a-156">HomeController.cs\*\*</span><span class="sxs-lookup"><span data-stu-id="2e09a-156">*HomeController.cs*</span></span>

[!code-csharp[](../../common/samples/WebApplication1/Controllers/HomeController.cs?highlight=5&range=16-21)]

<span data-ttu-id="2e09a-157">此操作返回时，最后一节显示的 About.cshtml 视图呈现为以下网页\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-157">When this action returns, the *About.cshtml* view shown in the last section is rendered as the following webpage:</span></span>

![Microsoft Edge 浏览器中呈现的“关于”页面](overview/_static/about-page.png)

<span data-ttu-id="2e09a-159">`View` 帮助程序方法有多个重载。</span><span class="sxs-lookup"><span data-stu-id="2e09a-159">The `View` helper method has several overloads.</span></span> <span data-ttu-id="2e09a-160">可选择指定：</span><span class="sxs-lookup"><span data-stu-id="2e09a-160">You can optionally specify:</span></span>

* <span data-ttu-id="2e09a-161">要返回的显式视图：</span><span class="sxs-lookup"><span data-stu-id="2e09a-161">An explicit view to return:</span></span>

  ```csharp
  return View("Orders");
  ```

* <span data-ttu-id="2e09a-162">要传递给视图的[模型](xref:mvc/models/model-binding)：</span><span class="sxs-lookup"><span data-stu-id="2e09a-162">A [model](xref:mvc/models/model-binding) to pass to the view:</span></span>

  ```csharp
  return View(Orders);
  ```

* <span data-ttu-id="2e09a-163">视图和模型：</span><span class="sxs-lookup"><span data-stu-id="2e09a-163">Both a view and a model:</span></span>

  ```csharp
  return View("Orders", Orders);
  ```

### <a name="view-discovery"></a><span data-ttu-id="2e09a-164">视图发现</span><span class="sxs-lookup"><span data-stu-id="2e09a-164">View discovery</span></span>

<span data-ttu-id="2e09a-165">操作返回一个视图时，会发生称为“视图发现”的过程\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-165">When an action returns a view, a process called *view discovery* takes place.</span></span> <span data-ttu-id="2e09a-166">此过程基于视图名称确定使用哪个视图文件。</span><span class="sxs-lookup"><span data-stu-id="2e09a-166">This process determines which view file is used based on the view name.</span></span> 

<span data-ttu-id="2e09a-167">`View` 方法 的默认行为 (`return View();`) 旨在返回与其从中调用的操作方法同名的视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-167">The default behavior of the `View` method (`return View();`) is to return a view with the same name as the action method from which it's called.</span></span> <span data-ttu-id="2e09a-168">例如，控制器的 "*关于*" `ActionResult` 方法名称用于搜索名为 "*关于*" 的视图文件。</span><span class="sxs-lookup"><span data-stu-id="2e09a-168">For example, the *About* `ActionResult` method name of the controller is used to search for a view file named *About.cshtml*.</span></span> <span data-ttu-id="2e09a-169">运行时首先在 Views/[ControllerName] 文件夹中搜索该视图\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-169">First, the runtime looks in the *Views/[ControllerName]* folder for the view.</span></span> <span data-ttu-id="2e09a-170">如果在此处找不到匹配的视图，则会在“Shared”文件夹中搜索该视图\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-170">If it doesn't find a matching view there, it searches the *Shared* folder for the view.</span></span>

<span data-ttu-id="2e09a-171">用 `return View();` 隐式返回 `ViewResult` 还是用 `return View("<ViewName>");` 将视图名称显式传递给 `View` 方法并不重要。</span><span class="sxs-lookup"><span data-stu-id="2e09a-171">It doesn't matter if you implicitly return the `ViewResult` with `return View();` or explicitly pass the view name to the `View` method with `return View("<ViewName>");`.</span></span> <span data-ttu-id="2e09a-172">在这两种情况下，视图发现都会按以下顺序搜索匹配的视图文件：</span><span class="sxs-lookup"><span data-stu-id="2e09a-172">In both cases, view discovery searches for a matching view file in this order:</span></span>

   1. <span data-ttu-id="2e09a-173">*Views/ \[ ControllerName]/ \[ ViewName] cshtml*</span><span class="sxs-lookup"><span data-stu-id="2e09a-173">*Views/\[ControllerName]/\[ViewName].cshtml*</span></span>
   1. <span data-ttu-id="2e09a-174">*Views/Shared/ \[ ViewName] cshtml*</span><span class="sxs-lookup"><span data-stu-id="2e09a-174">*Views/Shared/\[ViewName].cshtml*</span></span>

<span data-ttu-id="2e09a-175">可以提供视图文件路径而不提供视图名称。</span><span class="sxs-lookup"><span data-stu-id="2e09a-175">A view file path can be provided instead of a view name.</span></span> <span data-ttu-id="2e09a-176">如果使用从应用根目录开始的绝对路径（可选择以“/”或“~/”开头），则须指定 .cshtml 扩展名\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-176">If using an absolute path starting at the app root (optionally starting with "/" or "~/"), the *.cshtml* extension must be specified:</span></span>

```csharp
return View("Views/Home/About.cshtml");
```

<span data-ttu-id="2e09a-177">也可使用相对路径在不同目录中指定视图，而无需指定 .cshtml 扩展名\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-177">You can also use a relative path to specify views in different directories without the *.cshtml* extension.</span></span> <span data-ttu-id="2e09a-178">在 `HomeController` 内，可以使用相对路径返回 Manage 视图的 Index 视图\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-178">Inside the `HomeController`, you can return the *Index* view of your *Manage* views with a relative path:</span></span>

```csharp
return View("../Manage/Index");
```

<span data-ttu-id="2e09a-179">同样，可以用“./”前缀来指示当前的控制器特定目录：</span><span class="sxs-lookup"><span data-stu-id="2e09a-179">Similarly, you can indicate the current controller-specific directory with the "./" prefix:</span></span>

```csharp
return View("./About");
```

<span data-ttu-id="2e09a-180">[分部视图](xref:mvc/views/partial)和[视图组件](xref:mvc/views/view-components)使用类似（但不完全相同）的发现机制。</span><span class="sxs-lookup"><span data-stu-id="2e09a-180">[Partial views](xref:mvc/views/partial) and [view components](xref:mvc/views/view-components) use similar (but not identical) discovery mechanisms.</span></span>

<span data-ttu-id="2e09a-181">可以使用自定义 [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander) 自定义如何在应用中定位视图的默认约定。</span><span class="sxs-lookup"><span data-stu-id="2e09a-181">You can customize the default convention for how views are located within the app by using a custom [IViewLocationExpander](/dotnet/api/microsoft.aspnetcore.mvc.razor.iviewlocationexpander).</span></span>

<span data-ttu-id="2e09a-182">视图发现依赖于按文件名称查找视图文件。</span><span class="sxs-lookup"><span data-stu-id="2e09a-182">View discovery relies on finding view files by file name.</span></span> <span data-ttu-id="2e09a-183">如果基础文件系统区分大小写，则视图名称也可能区分大小写。</span><span class="sxs-lookup"><span data-stu-id="2e09a-183">If the underlying file system is case sensitive, view names are probably case sensitive.</span></span> <span data-ttu-id="2e09a-184">为了各操作系统的兼容性，请在控制器与操作名称之间，关联视图文件夹与文件名称之间匹配大小写。</span><span class="sxs-lookup"><span data-stu-id="2e09a-184">For compatibility across operating systems, match case between controller and action names and associated view folders and file names.</span></span> <span data-ttu-id="2e09a-185">如果在处理区分大小写的文件系统时遇到无法找到视图文件的错误，请确认请求的视图文件与实际视图文件名称之间的大小写是否匹配。</span><span class="sxs-lookup"><span data-stu-id="2e09a-185">If you encounter an error that a view file can't be found while working with a case-sensitive file system, confirm that the casing matches between the requested view file and the actual view file name.</span></span>

<span data-ttu-id="2e09a-186">按照组织视图文件结构的最佳做法，反映控制器、操作和视图之间的关系，实现可维护性和清晰度。</span><span class="sxs-lookup"><span data-stu-id="2e09a-186">Follow the best practice of organizing the file structure for your views to reflect the relationships among controllers, actions, and views for maintainability and clarity.</span></span>

## <a name="passing-data-to-views"></a><span data-ttu-id="2e09a-187">向视图传递数据</span><span class="sxs-lookup"><span data-stu-id="2e09a-187">Passing data to views</span></span>

<span data-ttu-id="2e09a-188">使用多种方法将数据传递给视图：</span><span class="sxs-lookup"><span data-stu-id="2e09a-188">Pass data to views using several approaches:</span></span>

* <span data-ttu-id="2e09a-189">强类型数据：viewmodel</span><span class="sxs-lookup"><span data-stu-id="2e09a-189">Strongly typed data: viewmodel</span></span>
* <span data-ttu-id="2e09a-190">弱类型数据</span><span class="sxs-lookup"><span data-stu-id="2e09a-190">Weakly typed data</span></span>
  * <span data-ttu-id="2e09a-191">`ViewData` (`ViewDataAttribute`)</span><span class="sxs-lookup"><span data-stu-id="2e09a-191">`ViewData` (`ViewDataAttribute`)</span></span>
  * `ViewBag`

### <a name="strongly-typed-data-viewmodel"></a><span data-ttu-id="2e09a-192">强类型数据 (viewmodel)</span><span class="sxs-lookup"><span data-stu-id="2e09a-192">Strongly typed data (viewmodel)</span></span>

<span data-ttu-id="2e09a-193">最可靠的方法是在视图中指定[模型](xref:mvc/models/model-binding)类型。</span><span class="sxs-lookup"><span data-stu-id="2e09a-193">The most robust approach is to specify a [model](xref:mvc/models/model-binding) type in the view.</span></span> <span data-ttu-id="2e09a-194">此模型通常称为 viewmodel\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-194">This model is commonly referred to as a *viewmodel*.</span></span> <span data-ttu-id="2e09a-195">将 viewmodel 类型的实例传递给此操作的视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-195">You pass an instance of the viewmodel type to the view from the action.</span></span>

<span data-ttu-id="2e09a-196">使用 viewmodel 将数据传递给视图可让视图充分利用强类型检查\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-196">Using a viewmodel to pass data to a view allows the view to take advantage of *strong* type checking.</span></span> <span data-ttu-id="2e09a-197">强类型化（或强类型）意味着每个变量和常量都有明确定义的类型（例如 `string`、`int` 或 `DateTime`）\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-197">*Strong typing* (or *strongly typed*) means that every variable and constant has an explicitly defined type (for example, `string`, `int`, or `DateTime`).</span></span> <span data-ttu-id="2e09a-198">在编译时检查视图中使用的类型是否有效。</span><span class="sxs-lookup"><span data-stu-id="2e09a-198">The validity of types used in a view is checked at compile time.</span></span>

<span data-ttu-id="2e09a-199">[Visual Studio](https://visualstudio.microsoft.com) 和 [Visual Studio Code](https://code.visualstudio.com/) 列出了使用 [IntelliSense](/visualstudio/ide/using-intellisense) 功能的强类型类成员。</span><span class="sxs-lookup"><span data-stu-id="2e09a-199">[Visual Studio](https://visualstudio.microsoft.com) and [Visual Studio Code](https://code.visualstudio.com/) list strongly typed class members using a feature called [IntelliSense](/visualstudio/ide/using-intellisense).</span></span> <span data-ttu-id="2e09a-200">如果要查看 viewmodel 的属性，请键入 viewmodel 的变量名称，后跟句点 (`.`)。</span><span class="sxs-lookup"><span data-stu-id="2e09a-200">When you want to see the properties of a viewmodel, type the variable name for the viewmodel followed by a period (`.`).</span></span> <span data-ttu-id="2e09a-201">这有助于提高编写代码的速度并降低错误率。</span><span class="sxs-lookup"><span data-stu-id="2e09a-201">This helps you write code faster with fewer errors.</span></span>

<span data-ttu-id="2e09a-202">使用 `@model` 指令指定模型。</span><span class="sxs-lookup"><span data-stu-id="2e09a-202">Specify a model using the `@model` directive.</span></span> <span data-ttu-id="2e09a-203">使用带有 `@Model` 的模型：</span><span class="sxs-lookup"><span data-stu-id="2e09a-203">Use the model with `@Model`:</span></span>

```cshtml
@model WebApplication1.ViewModels.Address

<h2>Contact</h2>
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="2e09a-204">为了将模型提供给视图，控制器将其作为参数进行传递：</span><span class="sxs-lookup"><span data-stu-id="2e09a-204">To provide the model to the view, the controller passes it as a parameter:</span></span>

```csharp
public IActionResult Contact()
{
    ViewData["Message"] = "Your contact page.";

    var viewModel = new Address()
    {
        Name = "Microsoft",
        Street = "One Microsoft Way",
        City = "Redmond",
        State = "WA",
        PostalCode = "98052-6399"
    };

    return View(viewModel);
}
```

<span data-ttu-id="2e09a-205">没有针对可以提供给视图的模型类型的限制。</span><span class="sxs-lookup"><span data-stu-id="2e09a-205">There are no restrictions on the model types that you can provide to a view.</span></span> <span data-ttu-id="2e09a-206">建议使用普通旧 CLR 对象 (POCO) viewmodel，它几乎没有已定义的行为（方法）。</span><span class="sxs-lookup"><span data-stu-id="2e09a-206">We recommend using Plain Old CLR Object (POCO) viewmodels with little or no behavior (methods) defined.</span></span> <span data-ttu-id="2e09a-207">通常，viewmodel 类要么存储在“Models”文件夹中，要么存储在应用根目录处的单独“ViewModels”文件夹中\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-207">Usually, viewmodel classes are either stored in the *Models* folder or a separate *ViewModels* folder at the root of the app.</span></span> <span data-ttu-id="2e09a-208">上例中使用的 Address viewmodel 是存储在 Address.cs 文件中的 POCO viewmodel\*\*\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-208">The *Address* viewmodel used in the example above is a POCO viewmodel stored in a file named *Address.cs*:</span></span>

```csharp
namespace WebApplication1.ViewModels
{
    public class Address
    {
        public string Name { get; set; }
        public string Street { get; set; }
        public string City { get; set; }
        public string State { get; set; }
        public string PostalCode { get; set; }
    }
}
```

<span data-ttu-id="2e09a-209">可随意对 viewmodel 类型和业务模型类型使用相同的类。</span><span class="sxs-lookup"><span data-stu-id="2e09a-209">Nothing prevents you from using the same classes for both your viewmodel types and your business model types.</span></span> <span data-ttu-id="2e09a-210">但是，使用单独的模型可使视图独立于应用的业务逻辑和数据访问部分。</span><span class="sxs-lookup"><span data-stu-id="2e09a-210">However, using separate models allows your views to vary independently from the business logic and data access parts of your app.</span></span> <span data-ttu-id="2e09a-211">模型为用户发送给应用的数据使用[模型绑定](xref:mvc/models/model-binding)和[验证](xref:mvc/models/validation)时，模型和 viewmodel 的分离也会提供安全优势。</span><span class="sxs-lookup"><span data-stu-id="2e09a-211">Separation of models and viewmodels also offers security benefits when models use [model binding](xref:mvc/models/model-binding) and [validation](xref:mvc/models/validation) for data sent to the app by the user.</span></span>

<a name="VD_VB"></a>

### <a name="weakly-typed-data-viewdata-viewdata-attribute-and-viewbag"></a><span data-ttu-id="2e09a-212">弱类型数据（ViewData、ViewData 属性和 ViewBag）</span><span class="sxs-lookup"><span data-stu-id="2e09a-212">Weakly typed data (ViewData, ViewData attribute, and ViewBag)</span></span>

<span data-ttu-id="2e09a-213">`ViewBag`*在中 Razor 不可用页面。*</span><span class="sxs-lookup"><span data-stu-id="2e09a-213">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="2e09a-214">除了强类型视图，视图还可以访问弱类型（也称为松散类型）的数据集合\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-214">In addition to strongly typed views, views have access to a *weakly typed* (also called *loosely typed*) collection of data.</span></span> <span data-ttu-id="2e09a-215">与强类型不同，弱类型（或松散类型）意味着不显式声明要使用的数据类型\*\*\*\*。</span><span class="sxs-lookup"><span data-stu-id="2e09a-215">Unlike strong types, *weak types* (or *loose types*) means that you don't explicitly declare the type of data you're using.</span></span> <span data-ttu-id="2e09a-216">可以使用弱类型数据集合将少量数据传入及传出控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-216">You can use the collection of weakly typed data for passing small amounts of data in and out of controllers and views.</span></span>

| <span data-ttu-id="2e09a-217">传递数据于...</span><span class="sxs-lookup"><span data-stu-id="2e09a-217">Passing data between a ...</span></span>                        | <span data-ttu-id="2e09a-218">示例</span><span class="sxs-lookup"><span data-stu-id="2e09a-218">Example</span></span>                                                                        |
| ------------------------------------------------- | ------------------------------------------------------------------------------ |
| <span data-ttu-id="2e09a-219">控制器和视图</span><span class="sxs-lookup"><span data-stu-id="2e09a-219">Controller and a view</span></span>                             | <span data-ttu-id="2e09a-220">用数据填充下拉列表。</span><span class="sxs-lookup"><span data-stu-id="2e09a-220">Populating a dropdown list with data.</span></span>                                          |
| <span data-ttu-id="2e09a-221">视图和[布局视图](xref:mvc/views/layout)</span><span class="sxs-lookup"><span data-stu-id="2e09a-221">View and a [layout view](xref:mvc/views/layout)</span></span>   | <span data-ttu-id="2e09a-222">在 **\<title>** 视图文件的布局视图中设置元素内容。</span><span class="sxs-lookup"><span data-stu-id="2e09a-222">Setting the **\<title>** element content in the layout view from a view file.</span></span>  |
| <span data-ttu-id="2e09a-223">[分部视图](xref:mvc/views/partial)和视图</span><span class="sxs-lookup"><span data-stu-id="2e09a-223">[Partial view](xref:mvc/views/partial) and a view</span></span> | <span data-ttu-id="2e09a-224">基于用户请求的网页显示数据的小组件。</span><span class="sxs-lookup"><span data-stu-id="2e09a-224">A widget that displays data based on the webpage that the user requested.</span></span>      |

<span data-ttu-id="2e09a-225">可以通过控制器和视图上的 `ViewData` 或 `ViewBag` 属性来引用此集合。</span><span class="sxs-lookup"><span data-stu-id="2e09a-225">This collection can be referenced through either the `ViewData` or `ViewBag` properties on controllers and views.</span></span> <span data-ttu-id="2e09a-226">`ViewData` 属性是弱类型对象的字典。</span><span class="sxs-lookup"><span data-stu-id="2e09a-226">The `ViewData` property is a dictionary of weakly typed objects.</span></span> <span data-ttu-id="2e09a-227">`ViewBag` 属性是 `ViewData` 的包装器，为基础 `ViewData` 集合提供动态属性。</span><span class="sxs-lookup"><span data-stu-id="2e09a-227">The `ViewBag` property is a wrapper around `ViewData` that provides dynamic properties for the underlying `ViewData` collection.</span></span> <span data-ttu-id="2e09a-228">注意：对于和，键查找不区分大小 `ViewData` 写 `ViewBag` 。</span><span class="sxs-lookup"><span data-stu-id="2e09a-228">Note: Key lookups are case-insensitive for both `ViewData` and `ViewBag`.</span></span>

<span data-ttu-id="2e09a-229">`ViewData` 和 `ViewBag` 在运行时进行动态解析。</span><span class="sxs-lookup"><span data-stu-id="2e09a-229">`ViewData` and `ViewBag` are dynamically resolved at runtime.</span></span> <span data-ttu-id="2e09a-230">由于它们不提供编译时类型检查，因此使用这两者通常比使用 viewmodel 更容易出错。</span><span class="sxs-lookup"><span data-stu-id="2e09a-230">Since they don't offer compile-time type checking, both are generally more error-prone than using a viewmodel.</span></span> <span data-ttu-id="2e09a-231">出于上述原因，一些开发者希望尽量减少或根本不使用 `ViewData` 和 `ViewBag`。</span><span class="sxs-lookup"><span data-stu-id="2e09a-231">For that reason, some developers prefer to minimally or never use `ViewData` and `ViewBag`.</span></span>

<a name="VD"></a>

<span data-ttu-id="2e09a-232">**ViewData**</span><span class="sxs-lookup"><span data-stu-id="2e09a-232">**ViewData**</span></span>

<span data-ttu-id="2e09a-233">`ViewData` 是通过 `string` 键访问的 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 对象。</span><span class="sxs-lookup"><span data-stu-id="2e09a-233">`ViewData` is a [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) object accessed through `string` keys.</span></span> <span data-ttu-id="2e09a-234">字符串数据可以直接存储和使用，而不需要强制转换，但是在提取其他 `ViewData` 对象值时必须将其强制转换为特定类型。</span><span class="sxs-lookup"><span data-stu-id="2e09a-234">String data can be stored and used directly without the need for a cast, but you must cast other `ViewData` object values to specific types when you extract them.</span></span> <span data-ttu-id="2e09a-235">可以使用 `ViewData` 将数据从控制器传递到视图，以及在视图（包括[分部视图](xref:mvc/views/partial)和[布局](xref:mvc/views/layout)）内传递数据。</span><span class="sxs-lookup"><span data-stu-id="2e09a-235">You can use `ViewData` to pass data from controllers to views and within views, including [partial views](xref:mvc/views/partial) and [layouts](xref:mvc/views/layout).</span></span>

<span data-ttu-id="2e09a-236">以下是在操作中使用 `ViewData` 设置问候语和地址值的示例：</span><span class="sxs-lookup"><span data-stu-id="2e09a-236">The following is an example that sets values for a greeting and an address using `ViewData` in an action:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewData["Greeting"] = "Hello";
    ViewData["Address"]  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

<span data-ttu-id="2e09a-237">在视图中处理数据：</span><span class="sxs-lookup"><span data-stu-id="2e09a-237">Work with the data in a view:</span></span>

```cshtml
@{
    // Since Address isn't a string, it requires a cast.
    var address = ViewData["Address"] as Address;
}

@ViewData["Greeting"] World!

<address>
    @address.Name<br>
    @address.Street<br>
    @address.City, @address.State @address.PostalCode
</address>
```

::: moniker range=">= aspnetcore-2.1"

<span data-ttu-id="2e09a-238">**ViewData 特性**</span><span class="sxs-lookup"><span data-stu-id="2e09a-238">**ViewData attribute**</span></span>

<span data-ttu-id="2e09a-239">另一种会使用 [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) 的方法是 [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute)。</span><span class="sxs-lookup"><span data-stu-id="2e09a-239">Another approach that uses the [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary) is [ViewDataAttribute](/dotnet/api/microsoft.aspnetcore.mvc.viewdataattribute).</span></span> <span data-ttu-id="2e09a-240">使用属性标记的控制器或页模型上的属性 Razor `[ViewData]` 将存储其值并从字典中加载它们。</span><span class="sxs-lookup"><span data-stu-id="2e09a-240">Properties on controllers or Razor Page models marked with the `[ViewData]` attribute have their values stored and loaded from the dictionary.</span></span>

<span data-ttu-id="2e09a-241">在下面的示例中，“主页”控制器包含使用 `[ViewData]` 标记的 `Title` 属性。</span><span class="sxs-lookup"><span data-stu-id="2e09a-241">In the following example, the Home controller contains a `Title` property marked with `[ViewData]`.</span></span> <span data-ttu-id="2e09a-242">`About` 方法设置“关于”视图的标题：</span><span class="sxs-lookup"><span data-stu-id="2e09a-242">The `About` method sets the title for the About view:</span></span>

```csharp
public class HomeController : Controller
{
    [ViewData]
    public string Title { get; set; }

    public IActionResult About()
    {
        Title = "About Us";
        ViewData["Message"] = "Your application description page.";

        return View();
    }
}
```

<span data-ttu-id="2e09a-243">在布局中，从 ViewData 字典读取标题：</span><span class="sxs-lookup"><span data-stu-id="2e09a-243">In the layout, the title is read from the ViewData dictionary:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"] - WebApplication</title>
    ...
```

::: moniker-end

<span data-ttu-id="2e09a-244">**ViewBag**</span><span class="sxs-lookup"><span data-stu-id="2e09a-244">**ViewBag**</span></span>

<span data-ttu-id="2e09a-245">`ViewBag`*在中 Razor 不可用页面。*</span><span class="sxs-lookup"><span data-stu-id="2e09a-245">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="2e09a-246">`ViewBag`是一个[DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)对象，它提供对存储在中的对象的动态访问 `ViewData` 。</span><span class="sxs-lookup"><span data-stu-id="2e09a-246">`ViewBag` is a [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata) object that provides dynamic access to the objects stored in `ViewData`.</span></span> <span data-ttu-id="2e09a-247">`ViewBag` 不需要强制转换，因此使用起来更加方便。</span><span class="sxs-lookup"><span data-stu-id="2e09a-247">`ViewBag` can be more convenient to work with, since it doesn't require casting.</span></span> <span data-ttu-id="2e09a-248">下例演示如何使用与上述 `ViewData` 有相同结果的 `ViewBag`：</span><span class="sxs-lookup"><span data-stu-id="2e09a-248">The following example shows how to use `ViewBag` with the same result as using `ViewData` above:</span></span>

```csharp
public IActionResult SomeAction()
{
    ViewBag.Greeting = "Hello";
    ViewBag.Address  = new Address()
    {
        Name = "Steve",
        Street = "123 Main St",
        City = "Hudson",
        State = "OH",
        PostalCode = "44236"
    };

    return View();
}
```

```cshtml
@ViewBag.Greeting World!

<address>
    @ViewBag.Address.Name<br>
    @ViewBag.Address.Street<br>
    @ViewBag.Address.City, @ViewBag.Address.State @ViewBag.Address.PostalCode
</address>
```

<span data-ttu-id="2e09a-249">**同时使用 ViewData 和 ViewBag**</span><span class="sxs-lookup"><span data-stu-id="2e09a-249">**Using ViewData and ViewBag simultaneously**</span></span>

<span data-ttu-id="2e09a-250">`ViewBag`*在中 Razor 不可用页面。*</span><span class="sxs-lookup"><span data-stu-id="2e09a-250">`ViewBag` *isn't available in Razor Pages.*</span></span>

<span data-ttu-id="2e09a-251">由于 `ViewData` 和 `ViewBag` 引用相同的基础 `ViewData` 集合，因此在读取和写入值时，可以同时使用 `ViewData` 和 `ViewBag`，并在两者之间进行混合和匹配。</span><span class="sxs-lookup"><span data-stu-id="2e09a-251">Since `ViewData` and `ViewBag` refer to the same underlying `ViewData` collection, you can use both `ViewData` and `ViewBag` and mix and match between them when reading and writing values.</span></span>

<span data-ttu-id="2e09a-252">在 About.cshtml 视图顶部，使用 `ViewBag` 设置标题并使用 `ViewData` 设置说明\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-252">Set the title using `ViewBag` and the description using `ViewData` at the top of an *About.cshtml* view:</span></span>

```cshtml
@{
    Layout = "/Views/Shared/_Layout.cshtml";
    ViewBag.Title = "About Contoso";
    ViewData["Description"] = "Let us tell you about Contoso's philosophy and mission.";
}
```

<span data-ttu-id="2e09a-253">读取属性，但反向使用 `ViewData` 和 `ViewBag`。</span><span class="sxs-lookup"><span data-stu-id="2e09a-253">Read the properties but reverse the use of `ViewData` and `ViewBag`.</span></span> <span data-ttu-id="2e09a-254">在 _Layout.cshtml 文件中，使用 `ViewData` 获取标题并使用 `ViewBag` 获取说明\*\*：</span><span class="sxs-lookup"><span data-stu-id="2e09a-254">In the *_Layout.cshtml* file, obtain the title using `ViewData` and obtain the description using `ViewBag`:</span></span>

```cshtml
<!DOCTYPE html>
<html lang="en">
<head>
    <title>@ViewData["Title"]</title>
    <meta name="description" content="@ViewBag.Description">
    ...
```

<span data-ttu-id="2e09a-255">请记住，字符串不需要为 `ViewData` 进行强制转换。</span><span class="sxs-lookup"><span data-stu-id="2e09a-255">Remember that strings don't require a cast for `ViewData`.</span></span> <span data-ttu-id="2e09a-256">可以使用 `@ViewData["Title"]` 而不需要强制转换。</span><span class="sxs-lookup"><span data-stu-id="2e09a-256">You can use `@ViewData["Title"]` without casting.</span></span>

<span data-ttu-id="2e09a-257">可同时使用 `ViewData` 和 `ViewBag`也可混合和匹配读取及写入属性。</span><span class="sxs-lookup"><span data-stu-id="2e09a-257">Using both `ViewData` and `ViewBag` at the same time works, as does mixing and matching reading and writing the properties.</span></span> <span data-ttu-id="2e09a-258">呈现以下标记：</span><span class="sxs-lookup"><span data-stu-id="2e09a-258">The following markup is rendered:</span></span>

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <title>About Contoso</title>
    <meta name="description" content="Let us tell you about Contoso's philosophy and mission.">
    ...
```

<span data-ttu-id="2e09a-259">**ViewData 和 ViewBag 之间差异的摘要**</span><span class="sxs-lookup"><span data-stu-id="2e09a-259">**Summary of the differences between ViewData and ViewBag**</span></span>

 <span data-ttu-id="2e09a-260">`ViewBag`页中不可用 Razor 。</span><span class="sxs-lookup"><span data-stu-id="2e09a-260">`ViewBag` isn't available in the Razor Pages.</span></span>

* `ViewData`
  * <span data-ttu-id="2e09a-261">派生自[ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary)，因此它具有可有用的字典属性，例如、、 `ContainsKey` `Add` `Remove` 和 `Clear` 。</span><span class="sxs-lookup"><span data-stu-id="2e09a-261">Derives from [ViewDataDictionary](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.viewdatadictionary), so it has dictionary properties that can be useful, such as `ContainsKey`, `Add`, `Remove`, and `Clear`.</span></span>
  * <span data-ttu-id="2e09a-262">字典中的键是字符串，因此允许有空格。</span><span class="sxs-lookup"><span data-stu-id="2e09a-262">Keys in the dictionary are strings, so whitespace is allowed.</span></span> <span data-ttu-id="2e09a-263">示例： `ViewData["Some Key With Whitespace"]`</span><span class="sxs-lookup"><span data-stu-id="2e09a-263">Example: `ViewData["Some Key With Whitespace"]`</span></span>
  * <span data-ttu-id="2e09a-264">任何非 `string` 类型均须在视图中进行强制转换才能使用 `ViewData`。</span><span class="sxs-lookup"><span data-stu-id="2e09a-264">Any type other than a `string` must be cast in the view to use `ViewData`.</span></span>
* `ViewBag`
  * <span data-ttu-id="2e09a-265">派生自 [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata)，因此它可使用点表示法 (`@ViewBag.SomeKey = <value or object>`) 创建动态属性，且无需强制转换。</span><span class="sxs-lookup"><span data-stu-id="2e09a-265">Derives from [DynamicViewData](/dotnet/api/microsoft.aspnetcore.mvc.viewfeatures.internal.dynamicviewdata), so it allows the creation of dynamic properties using dot notation (`@ViewBag.SomeKey = <value or object>`), and no casting is required.</span></span> <span data-ttu-id="2e09a-266">`ViewBag` 的语法使添加到控制器和视图的速度更快。</span><span class="sxs-lookup"><span data-stu-id="2e09a-266">The syntax of `ViewBag` makes it quicker to add to controllers and views.</span></span>
  * <span data-ttu-id="2e09a-267">更易于检查 NULL 值。</span><span class="sxs-lookup"><span data-stu-id="2e09a-267">Simpler to check for null values.</span></span> <span data-ttu-id="2e09a-268">示例： `@ViewBag.Person?.Name`</span><span class="sxs-lookup"><span data-stu-id="2e09a-268">Example: `@ViewBag.Person?.Name`</span></span>

<span data-ttu-id="2e09a-269">**何时使用 ViewData 或 ViewBag**</span><span class="sxs-lookup"><span data-stu-id="2e09a-269">**When to use ViewData or ViewBag**</span></span>

<span data-ttu-id="2e09a-270">`ViewData` 和 `ViewBag` 都是在控制器和视图之间传递少量数据的有效方法。</span><span class="sxs-lookup"><span data-stu-id="2e09a-270">Both `ViewData` and `ViewBag` are equally valid approaches for passing small amounts of data among controllers and views.</span></span> <span data-ttu-id="2e09a-271">根据偏好选择使用哪种方法。</span><span class="sxs-lookup"><span data-stu-id="2e09a-271">The choice of which one to use is based on preference.</span></span> <span data-ttu-id="2e09a-272">可以混合和匹配 `ViewData` 和 `ViewBag` 对象，但是，使用一致的方法可以更轻松地读取和维护代码。</span><span class="sxs-lookup"><span data-stu-id="2e09a-272">You can mix and match `ViewData` and `ViewBag` objects, however, the code is easier to read and maintain with one approach used consistently.</span></span> <span data-ttu-id="2e09a-273">这两种方法都是在运行时进行动态解析的，因此容易造成运行时错误。</span><span class="sxs-lookup"><span data-stu-id="2e09a-273">Both approaches are dynamically resolved at runtime and thus prone to causing runtime errors.</span></span> <span data-ttu-id="2e09a-274">因而，一些开发团队会避免使用它们。</span><span class="sxs-lookup"><span data-stu-id="2e09a-274">Some development teams avoid them.</span></span>

### <a name="dynamic-views"></a><span data-ttu-id="2e09a-275">动态视图</span><span class="sxs-lookup"><span data-stu-id="2e09a-275">Dynamic views</span></span>

<span data-ttu-id="2e09a-276">不使用 `@model` 声明模型类型，但有模型实例传递给它们的视图（如 `return View(Address);`）可动态引用实例的属性：</span><span class="sxs-lookup"><span data-stu-id="2e09a-276">Views that don't declare a model type using `@model` but that have a model instance passed to them (for example, `return View(Address);`) can reference the instance's properties dynamically:</span></span>

```cshtml
<address>
    @Model.Street<br>
    @Model.City, @Model.State @Model.PostalCode<br>
    <abbr title="Phone">P:</abbr> 425.555.0100
</address>
```

<span data-ttu-id="2e09a-277">此功能提供了灵活性，但不提供编译保护或 IntelliSense。</span><span class="sxs-lookup"><span data-stu-id="2e09a-277">This feature offers flexibility but doesn't offer compilation protection or IntelliSense.</span></span> <span data-ttu-id="2e09a-278">如果属性不存在，则网页生成在运行时会失败。</span><span class="sxs-lookup"><span data-stu-id="2e09a-278">If the property doesn't exist, webpage generation fails at runtime.</span></span>

## <a name="more-view-features"></a><span data-ttu-id="2e09a-279">更多视图功能</span><span class="sxs-lookup"><span data-stu-id="2e09a-279">More view features</span></span>

<span data-ttu-id="2e09a-280">[标记帮助程序](xref:mvc/views/tag-helpers/intro)可以轻松地将服务器端行为添加到现有的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="2e09a-280">[Tag Helpers](xref:mvc/views/tag-helpers/intro) make it easy to add server-side behavior to existing HTML tags.</span></span> <span data-ttu-id="2e09a-281">使用标记帮助程序可避免在视图内编写自定义代码或帮助程序。</span><span class="sxs-lookup"><span data-stu-id="2e09a-281">Using Tag Helpers avoids the need to write custom code or helpers within your views.</span></span> <span data-ttu-id="2e09a-282">标记帮助程序作为属性应用于 HTML 元素，并被无法处理它们的编辑器忽略。</span><span class="sxs-lookup"><span data-stu-id="2e09a-282">Tag helpers are applied as attributes to HTML elements and are ignored by editors that can't process them.</span></span> <span data-ttu-id="2e09a-283">这可让你在各种工具中编辑和呈现视图标记。</span><span class="sxs-lookup"><span data-stu-id="2e09a-283">This allows you to edit and render view markup in a variety of tools.</span></span>

<span data-ttu-id="2e09a-284">通过许多内置 HTML 帮助程序可生成自定义 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="2e09a-284">Generating custom HTML markup can be achieved with many built-in HTML Helpers.</span></span> <span data-ttu-id="2e09a-285">通过[视图组件](xref:mvc/views/view-components)可以处理更复杂的用户界面逻辑。</span><span class="sxs-lookup"><span data-stu-id="2e09a-285">More complex user interface logic can be handled by [View Components](xref:mvc/views/view-components).</span></span> <span data-ttu-id="2e09a-286">视图组件提供的 SoC 与控制器和视图提供的相同。</span><span class="sxs-lookup"><span data-stu-id="2e09a-286">View components provide the same SoC that controllers and views offer.</span></span> <span data-ttu-id="2e09a-287">它们无需使用处理数据（由常见用户界面元素使用）的操作和视图。</span><span class="sxs-lookup"><span data-stu-id="2e09a-287">They can eliminate the need for actions and views that deal with data used by common user interface elements.</span></span>

<span data-ttu-id="2e09a-288">与 ASP.NET Core 的许多其他方面一样，视图支持[依赖关系注入](xref:fundamentals/dependency-injection)，可将服务[注入视图](xref:mvc/views/dependency-injection)。</span><span class="sxs-lookup"><span data-stu-id="2e09a-288">Like many other aspects of ASP.NET Core, views support [dependency injection](xref:fundamentals/dependency-injection), allowing services to be [injected into views](xref:mvc/views/dependency-injection).</span></span>
