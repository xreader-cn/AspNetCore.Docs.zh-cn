---
title: ASP.NET Core 中的区域
author: rick-anderson
description: 了解 ASP.NET MVC 的区域功能如何将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。
ms.author: riande
ms.date: 02/14/2019
uid: mvc/controllers/areas
ms.openlocfilehash: c21eed04ea68512515da262b6b6895dc1a821039
ms.sourcegitcommit: 2c7ffe349eabdccf2ed748dd303ffd0ba6e1cfe3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/26/2019
ms.locfileid: "56833522"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="5e913-103">ASP.NET Core 中的区域</span><span class="sxs-lookup"><span data-stu-id="5e913-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="5e913-104">作者：[Dhananjay Kumar](https://twitter.com/debug_mode) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5e913-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5e913-105">区域是 ASP.NET 功能，用于将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。</span><span class="sxs-lookup"><span data-stu-id="5e913-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="5e913-106">使用区域通过为 `controller` 和 `action` 或 Razor Page `page` 添加其他路由参数 `area`，创建用于路由目的的层次结构。</span><span class="sxs-lookup"><span data-stu-id="5e913-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="5e913-107">区域提供了一种将 ASP.NET Core Web 应用划分为更小的功能组的方法，每个功能组都有自己的一组 Razor Pages、控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="5e913-107">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="5e913-108">区域实际上是应用内的结构。</span><span class="sxs-lookup"><span data-stu-id="5e913-108">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="5e913-109">在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="5e913-109">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="5e913-110">ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="5e913-110">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="5e913-111">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="5e913-111">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="5e913-112">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。</span><span class="sxs-lookup"><span data-stu-id="5e913-112">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="5e913-113">每个单位都有自己的区域，以包含视图、控制器、Razor Pages 和模型。</span><span class="sxs-lookup"><span data-stu-id="5e913-113">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="5e913-114">如果发生以下情况，请考虑在项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="5e913-114">Consider using Areas in an project when:</span></span>

* <span data-ttu-id="5e913-115">应用由可以进行逻辑分隔的多个高级功能组件组成。</span><span class="sxs-lookup"><span data-stu-id="5e913-115">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="5e913-116">想对应用进行分区，以便可以独立处理每个功能区域。</span><span class="sxs-lookup"><span data-stu-id="5e913-116">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="5e913-117">[查看或下载示例代码](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="5e913-117">[View or download sample code](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="5e913-118">下载示例提供了用于测试区域的基本应用。</span><span class="sxs-lookup"><span data-stu-id="5e913-118">The download sample provides a basic app for testing areas.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="5e913-119">带视图的控制器区域</span><span class="sxs-lookup"><span data-stu-id="5e913-119">Areas for controllers with views</span></span>

<span data-ttu-id="5e913-120">使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="5e913-120">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="5e913-121">[区域文件夹结构](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="5e913-121">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="5e913-122">使用[&lbrack;区域&rbrack;](#attribute)属性（该属性将控制器与区域关联）进行修饰的控制器：[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span><span class="sxs-lookup"><span data-stu-id="5e913-122">Controllers decorated with the [&lbrack;Area&rbrack;](#attribute) attribute to associate the controller with the area: [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]</span></span>
* <span data-ttu-id="5e913-123">[添加到启动的区域路由](#add-area-route)：[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span><span class="sxs-lookup"><span data-stu-id="5e913-123">The [area route added to startup](#add-area-route): [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]</span></span>

## <a name="area-folder-structure"></a><span data-ttu-id="5e913-124">区域文件夹结构</span><span class="sxs-lookup"><span data-stu-id="5e913-124">Area folder structure</span></span>
<span data-ttu-id="5e913-125">请考虑具有两个逻辑组（产品和服务）的应用。</span><span class="sxs-lookup"><span data-stu-id="5e913-125">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="5e913-126">使用区域，文件夹结构类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="5e913-126">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="5e913-127">项目名称</span><span class="sxs-lookup"><span data-stu-id="5e913-127">Project name</span></span>
  * <span data-ttu-id="5e913-128">区域</span><span class="sxs-lookup"><span data-stu-id="5e913-128">Areas</span></span>
    * <span data-ttu-id="5e913-129">产品</span><span class="sxs-lookup"><span data-stu-id="5e913-129">Products</span></span>
      * <span data-ttu-id="5e913-130">Controllers</span><span class="sxs-lookup"><span data-stu-id="5e913-130">Controllers</span></span>
        * <span data-ttu-id="5e913-131">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="5e913-131">HomeController.cs</span></span>
        * <span data-ttu-id="5e913-132">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="5e913-132">ManageController.cs</span></span>
      * <span data-ttu-id="5e913-133">视图</span><span class="sxs-lookup"><span data-stu-id="5e913-133">Views</span></span>
        * <span data-ttu-id="5e913-134">主页</span><span class="sxs-lookup"><span data-stu-id="5e913-134">Home</span></span>
          * <span data-ttu-id="5e913-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="5e913-135">Index.cshtml</span></span>
        * <span data-ttu-id="5e913-136">管理</span><span class="sxs-lookup"><span data-stu-id="5e913-136">Manage</span></span>
          * <span data-ttu-id="5e913-137">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="5e913-137">Index.cshtml</span></span>
          * <span data-ttu-id="5e913-138">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="5e913-138">About.cshtml</span></span>
    * <span data-ttu-id="5e913-139">服务</span><span class="sxs-lookup"><span data-stu-id="5e913-139">Services</span></span>
      * <span data-ttu-id="5e913-140">Controllers</span><span class="sxs-lookup"><span data-stu-id="5e913-140">Controllers</span></span>
        * <span data-ttu-id="5e913-141">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="5e913-141">HomeController.cs</span></span>
      * <span data-ttu-id="5e913-142">视图</span><span class="sxs-lookup"><span data-stu-id="5e913-142">Views</span></span>
        * <span data-ttu-id="5e913-143">主页</span><span class="sxs-lookup"><span data-stu-id="5e913-143">Home</span></span>
          * <span data-ttu-id="5e913-144">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="5e913-144">Index.cshtml</span></span>

<span data-ttu-id="5e913-145">虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="5e913-145">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="5e913-146">视图发现按以下顺序搜索匹配的区域视图文件：</span><span class="sxs-lookup"><span data-stu-id="5e913-146">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="5e913-147">控制器和模型等非视图文件夹的位置无关紧要。</span><span class="sxs-lookup"><span data-stu-id="5e913-147">The location of non-view folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="5e913-148">例如，控制器和模型文件夹不是必需的。</span><span class="sxs-lookup"><span data-stu-id="5e913-148">For example, the *Controllers* and *Models* folder are not required.</span></span> <span data-ttu-id="5e913-149">控制器和模型的内容是编译成 .dll 文件的代码。</span><span class="sxs-lookup"><span data-stu-id="5e913-149">The content of *Controllers* and *Models* is code which gets compiled into a .dll.</span></span> <span data-ttu-id="5e913-150">在对该视图发出请求之前，不会编译视图的内容。</span><span class="sxs-lookup"><span data-stu-id="5e913-150">The content of the *Views* isn't compiled until a request to that view has been made.</span></span>

<!-- TODO review:
The content of the *Views* isn't compiled until a request to that view has been made.

What about precompiled views? 
 -->
<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="5e913-151">将控制器与区域关联</span><span class="sxs-lookup"><span data-stu-id="5e913-151">Associate the controller with an Area</span></span>

<span data-ttu-id="5e913-152">使用[&lbrack;区域&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性指定区域控制器：</span><span class="sxs-lookup"><span data-stu-id="5e913-152">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="5e913-153">添加区域路由</span><span class="sxs-lookup"><span data-stu-id="5e913-153">Add Area route</span></span>

<span data-ttu-id="5e913-154">区域路由通常使用传统路由，而不使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="5e913-154">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="5e913-155">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="5e913-155">Conventional routing is order-dependent.</span></span> <span data-ttu-id="5e913-156">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="5e913-156">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="5e913-157">如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：</span><span class="sxs-lookup"><span data-stu-id="5e913-157">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="5e913-158">在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="5e913-158">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="5e913-159">使用 `{area:...}` 是将路由添加到区域的最简单的机制。</span><span class="sxs-lookup"><span data-stu-id="5e913-159">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="5e913-160">以下代码使用 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 创建两个命名区域路由：</span><span class="sxs-lookup"><span data-stu-id="5e913-160">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="5e913-161">将 `MapAreaRoute` 与 ASP.NET Core 2.2 配合使用时，请参阅[此 GitHub 问题](https://github.com/aspnet/AspNetCore/issues/7772)。</span><span class="sxs-lookup"><span data-stu-id="5e913-161">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/aspnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="5e913-162">有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="5e913-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-areas"></a><span data-ttu-id="5e913-163">区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="5e913-163">Link Generation with Areas</span></span>

<span data-ttu-id="5e913-164">[示例下载](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)中的以下代码显示指定区域的链接生成：</span><span class="sxs-lookup"><span data-stu-id="5e913-164">The following code from the [sample download](https://github.com/aspnet/Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="5e913-165">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="5e913-165">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="5e913-166">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="5e913-166">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="5e913-167">在[布局文件]()中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="5e913-167">The partial view is referenced in the [layout file](), so every page in the app displays the generated links.</span></span> <span data-ttu-id="5e913-168">在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="5e913-168">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="5e913-169">如果未指定区域或控制器，路由取决于环境值。</span><span class="sxs-lookup"><span data-stu-id="5e913-169">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="5e913-170">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="5e913-170">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="5e913-171">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="5e913-171">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="5e913-172">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="5e913-172">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-viewstartcshtml-file"></a><span data-ttu-id="5e913-173">使用 _ViewStart.cshtml 文件的共享区域布局</span><span class="sxs-lookup"><span data-stu-id="5e913-173">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="5e913-174">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。</span><span class="sxs-lookup"><span data-stu-id="5e913-174">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

<!-- This section will be completed after https://github.com/aspnet/Docs/pull/10978 is merged.
<a name="arp"></a>

## Areas for Razor Pages
-->
<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="5e913-175">更改存储视图的默认区域文件夹</span><span class="sxs-lookup"><span data-stu-id="5e913-175">Change default area folder where views are stored</span></span>

<span data-ttu-id="5e913-176">以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="5e913-176">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<!-- TODO review - can we delete this. Areas doesn't change publishing - right? -->
### <a name="publishing-areas"></a><span data-ttu-id="5e913-177">发布区域</span><span class="sxs-lookup"><span data-stu-id="5e913-177">Publishing Areas</span></span>

<span data-ttu-id="5e913-178">当 .csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 `*.cshtml` 和 `wwwroot/**` 文件将发布到输出。</span><span class="sxs-lookup"><span data-stu-id="5e913-178">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
