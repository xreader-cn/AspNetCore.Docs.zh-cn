---
title: ASP.NET Core 中的区域
author: rick-anderson
description: 了解 ASP.NET MVC 的区域功能如何将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。
ms.author: riande
ms.date: 05/10/2019
uid: mvc/controllers/areas
ms.openlocfilehash: f3a75bc307a206e43241b421f448b09011868d08
ms.sourcegitcommit: ffe3ed7921ec6c7c70abaac1d10703ec9a43374c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2019
ms.locfileid: "65535974"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="0888f-103">ASP.NET Core 中的区域</span><span class="sxs-lookup"><span data-stu-id="0888f-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="0888f-104">作者：[Dhananjay Kumar](https://twitter.com/debug_mode) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0888f-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0888f-105">区域是 ASP.NET 功能，用于将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。</span><span class="sxs-lookup"><span data-stu-id="0888f-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="0888f-106">使用区域通过为 `controller` 和 `action` 或 Razor Page `page` 添加其他路由参数 `area`，创建用于路由目的的层次结构。</span><span class="sxs-lookup"><span data-stu-id="0888f-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="0888f-107">区域提供了一种将 ASP.NET Core Web 应用划分为更小的功能组的方法，每个功能组都有自己的一组 Razor Pages、控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="0888f-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="0888f-108">区域实际上是应用内的结构。</span><span class="sxs-lookup"><span data-stu-id="0888f-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="0888f-109">在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="0888f-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="0888f-110">ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="0888f-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="0888f-111">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="0888f-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="0888f-112">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。</span><span class="sxs-lookup"><span data-stu-id="0888f-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="0888f-113">每个单位都有自己的区域，以包含视图、控制器、Razor Pages 和模型。</span><span class="sxs-lookup"><span data-stu-id="0888f-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="0888f-114">如果发生以下情况，请考虑在项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="0888f-114">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="0888f-115">应用由可以进行逻辑分隔的多个高级功能组件组成。</span><span class="sxs-lookup"><span data-stu-id="0888f-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="0888f-116">想对应用进行分区，以便可以独立处理每个功能区域。</span><span class="sxs-lookup"><span data-stu-id="0888f-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="0888f-117">[查看或下载示例代码](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="0888f-117">[View or download sample code](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="0888f-118">下载示例提供了用于测试区域的基本应用。</span><span class="sxs-lookup"><span data-stu-id="0888f-118">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="0888f-119">如果使用 Razor Pages，请参阅本文档中的[使用 Razor Pages 的区域](#areas-with-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="0888f-119">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="0888f-120">带视图的控制器区域</span><span class="sxs-lookup"><span data-stu-id="0888f-120">Areas for controllers with views</span></span>

<span data-ttu-id="0888f-121">使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="0888f-121">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="0888f-122">[区域文件夹结构](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="0888f-122">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="0888f-123">使用 [&lbrack;Area&rbrack;](#attribute) 属性（该属性将控制器与区域关联）进行修饰的控制器：</span><span class="sxs-lookup"><span data-stu-id="0888f-123">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="0888f-124">[添加到启动的区域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="0888f-124">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="0888f-125">区域文件夹结构</span><span class="sxs-lookup"><span data-stu-id="0888f-125">Area folder structure</span></span>

<span data-ttu-id="0888f-126">请考虑具有两个逻辑组（产品和服务）的应用。</span><span class="sxs-lookup"><span data-stu-id="0888f-126">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="0888f-127">使用区域，文件夹结构类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="0888f-127">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="0888f-128">项目名称</span><span class="sxs-lookup"><span data-stu-id="0888f-128">Project name</span></span>
  * <span data-ttu-id="0888f-129">区域</span><span class="sxs-lookup"><span data-stu-id="0888f-129">Areas</span></span>
    * <span data-ttu-id="0888f-130">产品</span><span class="sxs-lookup"><span data-stu-id="0888f-130">Products</span></span>
      * <span data-ttu-id="0888f-131">Controllers</span><span class="sxs-lookup"><span data-stu-id="0888f-131">Controllers</span></span>
        * <span data-ttu-id="0888f-132">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="0888f-132">HomeController.cs</span></span>
        * <span data-ttu-id="0888f-133">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="0888f-133">ManageController.cs</span></span>
      * <span data-ttu-id="0888f-134">视图</span><span class="sxs-lookup"><span data-stu-id="0888f-134">Views</span></span>
        * <span data-ttu-id="0888f-135">主页</span><span class="sxs-lookup"><span data-stu-id="0888f-135">Home</span></span>
          * <span data-ttu-id="0888f-136">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0888f-136">Index.cshtml</span></span>
        * <span data-ttu-id="0888f-137">管理</span><span class="sxs-lookup"><span data-stu-id="0888f-137">Manage</span></span>
          * <span data-ttu-id="0888f-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0888f-138">Index.cshtml</span></span>
          * <span data-ttu-id="0888f-139">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="0888f-139">About.cshtml</span></span>
    * <span data-ttu-id="0888f-140">服务</span><span class="sxs-lookup"><span data-stu-id="0888f-140">Services</span></span>
      * <span data-ttu-id="0888f-141">Controllers</span><span class="sxs-lookup"><span data-stu-id="0888f-141">Controllers</span></span>
        * <span data-ttu-id="0888f-142">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="0888f-142">HomeController.cs</span></span>
      * <span data-ttu-id="0888f-143">视图</span><span class="sxs-lookup"><span data-stu-id="0888f-143">Views</span></span>
        * <span data-ttu-id="0888f-144">主页</span><span class="sxs-lookup"><span data-stu-id="0888f-144">Home</span></span>
          * <span data-ttu-id="0888f-145">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="0888f-145">Index.cshtml</span></span>

<span data-ttu-id="0888f-146">虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="0888f-146">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="0888f-147">视图发现按以下顺序搜索匹配的区域视图文件：</span><span class="sxs-lookup"><span data-stu-id="0888f-147">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="0888f-148">控制器和模型等非视图文件夹的位置无关紧要。</span><span class="sxs-lookup"><span data-stu-id="0888f-148">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="0888f-149">例如，控制器和模型文件夹不是必需的。</span><span class="sxs-lookup"><span data-stu-id="0888f-149">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="0888f-150">控制器和模型的内容是编译成 .dll 文件的代码。</span><span class="sxs-lookup"><span data-stu-id="0888f-150">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="0888f-151">在对该视图发出请求之前，不会编译视图的内容。</span><span class="sxs-lookup"><span data-stu-id="0888f-151">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="0888f-152">将控制器与区域关联</span><span class="sxs-lookup"><span data-stu-id="0888f-152">Associate the controller with an Area</span></span>

<span data-ttu-id="0888f-153">使用[&lbrack;区域&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性指定区域控制器：</span><span class="sxs-lookup"><span data-stu-id="0888f-153">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="0888f-154">添加区域路由</span><span class="sxs-lookup"><span data-stu-id="0888f-154">Add Area route</span></span>

<span data-ttu-id="0888f-155">区域路由通常使用传统路由，而不使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="0888f-155">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="0888f-156">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="0888f-156">Conventional routing is order-dependent.</span></span> <span data-ttu-id="0888f-157">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="0888f-157">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="0888f-158">如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：</span><span class="sxs-lookup"><span data-stu-id="0888f-158">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="0888f-159">在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="0888f-159">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="0888f-160">使用 `{area:...}` 是将路由添加到区域的最简单的机制。</span><span class="sxs-lookup"><span data-stu-id="0888f-160">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="0888f-161">以下代码使用 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 创建两个命名区域路由：</span><span class="sxs-lookup"><span data-stu-id="0888f-161">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="0888f-162">将 `MapAreaRoute` 与 ASP.NET Core 2.2 配合使用时，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/7772)。</span><span class="sxs-lookup"><span data-stu-id="0888f-162">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="0888f-163">有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="0888f-163">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="0888f-164">MVC 区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="0888f-164">Link generation with MVC areas</span></span>

<span data-ttu-id="0888f-165">[示例下载](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)中的以下代码显示指定区域的链接生成：</span><span class="sxs-lookup"><span data-stu-id="0888f-165">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="0888f-166">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="0888f-166">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="0888f-167">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="0888f-167">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="0888f-168">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="0888f-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="0888f-169">在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="0888f-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="0888f-170">如果未指定区域或控制器，路由取决于环境值。</span><span class="sxs-lookup"><span data-stu-id="0888f-170">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="0888f-171">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="0888f-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="0888f-172">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="0888f-172">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="0888f-173">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="0888f-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="0888f-174">使用 _ViewStart.cshtml 文件的共享区域布局</span><span class="sxs-lookup"><span data-stu-id="0888f-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="0888f-175">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。</span><span class="sxs-lookup"><span data-stu-id="0888f-175">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="0888f-176">更改存储视图的默认区域文件夹</span><span class="sxs-lookup"><span data-stu-id="0888f-176">Change default area folder where views are stored</span></span>

<span data-ttu-id="0888f-177">以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="0888f-177">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="0888f-178">使用 Razor Pages 的区域</span><span class="sxs-lookup"><span data-stu-id="0888f-178">Areas with Razor Pages</span></span>

<span data-ttu-id="0888f-179">使用 Razor Pages 的区域需要在应用根目录中有一个“Areas/&lt;area name&gt;/Pages”文件夹。</span><span class="sxs-lookup"><span data-stu-id="0888f-179">Areas with Razor Pages require and *Areas/&lt;area name&gt;/Pages* folder in the root of the app.</span></span> <span data-ttu-id="0888f-180">[示例下载](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)中使用以下文件夹结构</span><span class="sxs-lookup"><span data-stu-id="0888f-180">The following folder structure is used with the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)</span></span>

* <span data-ttu-id="0888f-181">项目名称</span><span class="sxs-lookup"><span data-stu-id="0888f-181">Project name</span></span>
  * <span data-ttu-id="0888f-182">区域</span><span class="sxs-lookup"><span data-stu-id="0888f-182">Areas</span></span>
    * <span data-ttu-id="0888f-183">产品</span><span class="sxs-lookup"><span data-stu-id="0888f-183">Products</span></span>
      * <span data-ttu-id="0888f-184">Pages</span><span class="sxs-lookup"><span data-stu-id="0888f-184">Pages</span></span>
        * <span data-ttu-id="0888f-185">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="0888f-185">_ViewImports</span></span>
        * <span data-ttu-id="0888f-186">关于</span><span class="sxs-lookup"><span data-stu-id="0888f-186">About</span></span>
        * <span data-ttu-id="0888f-187">索引</span><span class="sxs-lookup"><span data-stu-id="0888f-187">Index</span></span>
    * <span data-ttu-id="0888f-188">服务</span><span class="sxs-lookup"><span data-stu-id="0888f-188">Services</span></span>
      * <span data-ttu-id="0888f-189">Pages</span><span class="sxs-lookup"><span data-stu-id="0888f-189">Pages</span></span>
        * <span data-ttu-id="0888f-190">管理</span><span class="sxs-lookup"><span data-stu-id="0888f-190">Manage</span></span>
          * <span data-ttu-id="0888f-191">关于</span><span class="sxs-lookup"><span data-stu-id="0888f-191">About</span></span>
          * <span data-ttu-id="0888f-192">索引</span><span class="sxs-lookup"><span data-stu-id="0888f-192">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="0888f-193">Razor Pages 和区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="0888f-193">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="0888f-194">[示例下载](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)中的以下代码显示指定区域（例如 `asp-area="Products"`）的链接生成：</span><span class="sxs-lookup"><span data-stu-id="0888f-194">The following code from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="0888f-195">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="0888f-195">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="0888f-196">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="0888f-196">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="0888f-197">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="0888f-197">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="0888f-198">在未指定区域的情况下生成的链接仅在从同一区域中的页引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="0888f-198">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="0888f-199">如果未指定区域，路由取决于环境值。</span><span class="sxs-lookup"><span data-stu-id="0888f-199">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="0888f-200">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="0888f-200">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="0888f-201">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="0888f-201">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="0888f-202">例如，考虑从下面的代码生成的链接：</span><span class="sxs-lookup"><span data-stu-id="0888f-202">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="0888f-203">对于上述代码：</span><span class="sxs-lookup"><span data-stu-id="0888f-203">For the preceding code:</span></span>

* <span data-ttu-id="0888f-204">只有当最后一个请求是针对 `Services` 区域的页时，从 `<a asp-page="/Manage/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="0888f-204">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="0888f-205">例如 `/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="0888f-205">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="0888f-206">只有当最后一个请求是针对 `/Home` 中的页时，从 `<a asp-page="/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="0888f-206">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="0888f-207">代码摘自[示例下载](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="0888f-207">The code is from the [sample download](https://github.com/aspnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-viewimports-file"></a><span data-ttu-id="0888f-208">使用 _ViewImports 文件导入命名空间和标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="0888f-208">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="0888f-209">可向每个区域“页面”文件夹添加一个 _ViewImports.cshtml 文件，以将命名空间和标记帮助器导入到该文件夹的每个 Razor 页面中。</span><span class="sxs-lookup"><span data-stu-id="0888f-209">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="0888f-210">请考虑使用示例代码的“服务”区域，它不包含 _ViewImports.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="0888f-210">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="0888f-211">以下标记显示 /Services/Manage/About Razor Page：</span><span class="sxs-lookup"><span data-stu-id="0888f-211">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="0888f-212">在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="0888f-212">In the preceding markup:</span></span>

* <span data-ttu-id="0888f-213">必须使用完全限定的域名来指定模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="0888f-213">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="0888f-214">[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 启动</span><span class="sxs-lookup"><span data-stu-id="0888f-214">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="0888f-215">在示例下载中，“产品”区域包含下列 _ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="0888f-215">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="0888f-216">以下标记显示 /Products/About Razor Page：</span><span class="sxs-lookup"><span data-stu-id="0888f-216">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="0888f-217">在前面的文件中，命名空间和 `@addTagHelper` 指令通过 Areas/Products/Pages/_ViewImports.cshtml 文件导入到文件中。</span><span class="sxs-lookup"><span data-stu-id="0888f-217">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="0888f-218">有关详细信息，请参阅[管理标记帮助程序范围](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="0888f-218">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="0888f-219">Razor Pages 区域的共享布局</span><span class="sxs-lookup"><span data-stu-id="0888f-219">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="0888f-220">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。</span><span class="sxs-lookup"><span data-stu-id="0888f-220">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="0888f-221">发布区域</span><span class="sxs-lookup"><span data-stu-id="0888f-221">Publishing Areas</span></span>

<span data-ttu-id="0888f-222">当 \*.csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 \*.cshtml 文件以及 wwwroot 目录中的文件都将发布到输出中。</span><span class="sxs-lookup"><span data-stu-id="0888f-222">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
