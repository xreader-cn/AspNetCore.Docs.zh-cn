---
title: ASP.NET Core 中的区域
author: rick-anderson
description: 了解 ASP.NET MVC 的区域功能如何将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。
ms.author: riande
ms.date: 12/05/2019
uid: mvc/controllers/areas
ms.openlocfilehash: 41f7bdd6dbb3e33f843cb2a765dd30f98c81ce21
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78654234"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="ce3b7-103">ASP.NET Core 中的区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="ce3b7-104">作者：[Dhananjay Kumar](https://twitter.com/debug_mode) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="ce3b7-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="ce3b7-105">区域是一种 ASP.NET 功能，用于将相关功能作为单独的组进行组织：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="ce3b7-106">用于路由的命名空间。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-106">Namespace for routing.</span></span>
* <span data-ttu-id="ce3b7-107">视图和 Razor Pages 的文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-107">Folder structure for views and Razor Pages.</span></span>

<span data-ttu-id="ce3b7-108">使用区域通过为 `controller` 和 `action` 或 Razor Page `page` 添加其他路由参数 `area`，创建用于路由目的的层次结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="ce3b7-109">区域提供了一种将 ASP.NET Core Web 应用划分为更小的功能组的方法，每个功能组都有自己的一组 Razor Pages、控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="ce3b7-110">区域实际上是应用内的结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="ce3b7-111">在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="ce3b7-112">ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="ce3b7-113">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="ce3b7-114">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="ce3b7-115">每个单位都有自己的区域，以包含视图、控制器、Razor Pages 和模型。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-115">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="ce3b7-116">如果发生以下情况，请考虑在项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="ce3b7-117">应用由可以进行逻辑分隔的多个高级功能组件组成。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="ce3b7-118">想对应用进行分区，以便可以独立处理每个功能区域。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="ce3b7-119">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="ce3b7-120">下载示例提供了用于测试区域的基本应用。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="ce3b7-121">如果使用 Razor Pages，请参阅本文档中的[使用 Razor Pages 的区域](#areas-with-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-121">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="ce3b7-122">带视图的控制器区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-122">Areas for controllers with views</span></span>

<span data-ttu-id="ce3b7-123">使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="ce3b7-124">[区域文件夹结构](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="ce3b7-125">使用 [`[Area]`](#attribute) 属性将自己与区域关联的控制器：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="ce3b7-126">[添加到启动的区域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="ce3b7-127">区域文件夹结构</span><span class="sxs-lookup"><span data-stu-id="ce3b7-127">Area folder structure</span></span>

<span data-ttu-id="ce3b7-128">请考虑具有两个逻辑组（产品和服务）的应用。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-128">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="ce3b7-129">使用区域，文件夹结构类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="ce3b7-130">项目名称</span><span class="sxs-lookup"><span data-stu-id="ce3b7-130">Project name</span></span>
  * <span data-ttu-id="ce3b7-131">Areas</span><span class="sxs-lookup"><span data-stu-id="ce3b7-131">Areas</span></span>
    * <span data-ttu-id="ce3b7-132">Products</span><span class="sxs-lookup"><span data-stu-id="ce3b7-132">Products</span></span>
      * <span data-ttu-id="ce3b7-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="ce3b7-133">Controllers</span></span>
        * <span data-ttu-id="ce3b7-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="ce3b7-134">HomeController.cs</span></span>
        * <span data-ttu-id="ce3b7-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="ce3b7-135">ManageController.cs</span></span>
      * <span data-ttu-id="ce3b7-136">Views</span><span class="sxs-lookup"><span data-stu-id="ce3b7-136">Views</span></span>
        * <span data-ttu-id="ce3b7-137">Home</span><span class="sxs-lookup"><span data-stu-id="ce3b7-137">Home</span></span>
          * <span data-ttu-id="ce3b7-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-138">Index.cshtml</span></span>
        * <span data-ttu-id="ce3b7-139">Manage</span><span class="sxs-lookup"><span data-stu-id="ce3b7-139">Manage</span></span>
          * <span data-ttu-id="ce3b7-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-140">Index.cshtml</span></span>
          * <span data-ttu-id="ce3b7-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-141">About.cshtml</span></span>
    * <span data-ttu-id="ce3b7-142">Services</span><span class="sxs-lookup"><span data-stu-id="ce3b7-142">Services</span></span>
      * <span data-ttu-id="ce3b7-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="ce3b7-143">Controllers</span></span>
        * <span data-ttu-id="ce3b7-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="ce3b7-144">HomeController.cs</span></span>
      * <span data-ttu-id="ce3b7-145">视图</span><span class="sxs-lookup"><span data-stu-id="ce3b7-145">Views</span></span>
        * <span data-ttu-id="ce3b7-146">Home</span><span class="sxs-lookup"><span data-stu-id="ce3b7-146">Home</span></span>
          * <span data-ttu-id="ce3b7-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-147">Index.cshtml</span></span>

<span data-ttu-id="ce3b7-148">虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="ce3b7-149">视图发现按以下顺序搜索匹配的区域视图文件：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="ce3b7-150">将控制器与区域关联</span><span class="sxs-lookup"><span data-stu-id="ce3b7-150">Associate the controller with an Area</span></span>

<span data-ttu-id="ce3b7-151">使用[&lbrack;区域&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性指定区域控制器：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="ce3b7-152">添加区域路由</span><span class="sxs-lookup"><span data-stu-id="ce3b7-152">Add Area route</span></span>

<span data-ttu-id="ce3b7-153">区域路由通常使用[传统路由](xref:mvc/controllers/routing#cr)，而不是[属性路由](xref:mvc/controllers/routing#ar)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="ce3b7-154">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ce3b7-155">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ce3b7-156">如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="ce3b7-157">在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="ce3b7-158">使用 `MapControllerRoute`的 `{area:...}`：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="ce3b7-159">是将路由添加到区域的最不复杂的机制。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="ce3b7-160">匹配具有 `[Area("Area name")]` 属性的所有控制器。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="ce3b7-161">以下代码使用 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 创建两个命名区域路由：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="ce3b7-162">有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="ce3b7-163">MVC 区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="ce3b7-163">Link generation with MVC areas</span></span>

<span data-ttu-id="ce3b7-164">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)中的以下代码显示指定区域的链接生成：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="ce3b7-165">示例下载包含[部分视图](xref:mvc/views/partial)，其中包含：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="ce3b7-166">前面的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-166">The preceding links.</span></span>
* <span data-ttu-id="ce3b7-167">与前面的链接相似，但未指定 `area`。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="ce3b7-168">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="ce3b7-169">在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="ce3b7-170">如果未指定区域或控制器，路由取决[于环](xref:mvc/controllers/routing#ambient)境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="ce3b7-171">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="ce3b7-172">在许多情况下，对于示例应用程序，使用环境值时，将生成带有未指定区域的标记的错误链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="ce3b7-173">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="ce3b7-174">使用 _ViewStart.cshtml 文件的共享区域布局</span><span class="sxs-lookup"><span data-stu-id="ce3b7-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="ce3b7-175">若要为整个应用共享公共布局，请在[应用程序根文件夹](#arf)中保留 *_ViewStart。*</span><span class="sxs-lookup"><span data-stu-id="ce3b7-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="ce3b7-176">有关详细信息，请参阅<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="ce3b7-177">应用程序根文件夹</span><span class="sxs-lookup"><span data-stu-id="ce3b7-177">Application root folder</span></span>

<span data-ttu-id="ce3b7-178">应用程序根文件夹是包含 ASP.NET Core 模板创建的 web 应用中的*Startup.cs*的文件夹。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="ce3b7-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="ce3b7-180">对于 MVC， *_ViewImports 的/Views/* 和 */Pages/_ViewImports* Razor Pages 不会导入到区域中的视图。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-180">*/Views/_ViewImports.cshtml*, for MVC, and */Pages/_ViewImports.cshtml* for Razor Pages, is not imported to views in areas.</span></span> <span data-ttu-id="ce3b7-181">使用以下方法之一来提供所有视图的视图导入：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="ce3b7-182">将 *_ViewImports*添加到[应用程序根文件夹](#arf)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="ce3b7-183">应用程序根文件夹中的 *_ViewImports*将应用到应用中的所有视图。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="ce3b7-184">将 *_ViewImports*的文件复制到 "区域" 下的相应视图文件夹中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="ce3b7-185">*_ViewImports 的 cshtml*文件通常包含[标记帮助](xref:mvc/views/tag-helpers/intro)程序导入、`@using`和 `@inject` 语句。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="ce3b7-186">有关详细信息，请参阅[导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="ce3b7-187">更改存储视图的默认区域文件夹</span><span class="sxs-lookup"><span data-stu-id="ce3b7-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="ce3b7-188">以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="ce3b7-189">使用 Razor Pages 的区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-189">Areas with Razor Pages</span></span>

<span data-ttu-id="ce3b7-190">具有 Razor Pages 的区域需要应用根目录中的 `Areas/<area name>/Pages` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-190">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="ce3b7-191">以下文件夹结构用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="ce3b7-192">项目名称</span><span class="sxs-lookup"><span data-stu-id="ce3b7-192">Project name</span></span>
  * <span data-ttu-id="ce3b7-193">Areas</span><span class="sxs-lookup"><span data-stu-id="ce3b7-193">Areas</span></span>
    * <span data-ttu-id="ce3b7-194">Products</span><span class="sxs-lookup"><span data-stu-id="ce3b7-194">Products</span></span>
      * <span data-ttu-id="ce3b7-195">Pages</span><span class="sxs-lookup"><span data-stu-id="ce3b7-195">Pages</span></span>
        * <span data-ttu-id="ce3b7-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="ce3b7-196">_ViewImports</span></span>
        * <span data-ttu-id="ce3b7-197">About</span><span class="sxs-lookup"><span data-stu-id="ce3b7-197">About</span></span>
        * <span data-ttu-id="ce3b7-198">Index</span><span class="sxs-lookup"><span data-stu-id="ce3b7-198">Index</span></span>
    * <span data-ttu-id="ce3b7-199">Services</span><span class="sxs-lookup"><span data-stu-id="ce3b7-199">Services</span></span>
      * <span data-ttu-id="ce3b7-200">Pages</span><span class="sxs-lookup"><span data-stu-id="ce3b7-200">Pages</span></span>
        * <span data-ttu-id="ce3b7-201">Manage</span><span class="sxs-lookup"><span data-stu-id="ce3b7-201">Manage</span></span>
          * <span data-ttu-id="ce3b7-202">About</span><span class="sxs-lookup"><span data-stu-id="ce3b7-202">About</span></span>
          * <span data-ttu-id="ce3b7-203">Index</span><span class="sxs-lookup"><span data-stu-id="ce3b7-203">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="ce3b7-204">Razor Pages 和区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="ce3b7-204">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="ce3b7-205">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)中的以下代码显示指定区域（例如 `asp-area="Products"`）的链接生成：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="ce3b7-206">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="ce3b7-207">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="ce3b7-208">在未指定区域的情况下生成的链接仅在从同一区域中的页引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="ce3b7-209">如果未指定区域，路由取决于环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="ce3b7-210">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="ce3b7-211">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="ce3b7-212">例如，考虑从下面的代码生成的链接：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="ce3b7-213">对于上述代码：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-213">For the preceding code:</span></span>

* <span data-ttu-id="ce3b7-214">只有当最后一个请求是针对 `Services` 区域的页时，从 `<a asp-page="/Manage/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="ce3b7-215">例如 `/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="ce3b7-216">只有当最后一个请求是针对 `/Home` 中的页时，从 `<a asp-page="/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="ce3b7-217">代码摘自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="ce3b7-218">使用 _ViewImports 文件导入命名空间和标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="ce3b7-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="ce3b7-219">可向每个区域“页面”文件夹添加一个 _ViewImports.cshtml 文件，以将命名空间和标记帮助器导入到该文件夹的每个 Razor 页面中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="ce3b7-220">请考虑使用示例代码的“服务”区域，它不包含 _ViewImports.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="ce3b7-221">以下标记显示 /Services/Manage/About Razor Page：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-221">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="ce3b7-222">在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-222">In the preceding markup:</span></span>

* <span data-ttu-id="ce3b7-223">必须使用完全限定的域名来指定模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="ce3b7-224">[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 启动</span><span class="sxs-lookup"><span data-stu-id="ce3b7-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="ce3b7-225">在示例下载中，“产品”区域包含下列 _ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="ce3b7-226">以下标记显示 /Products/About Razor Page：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-226">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="ce3b7-227">在前面的文件中，命名空间和 `@addTagHelper` 指令通过 Areas/Products/Pages/_ViewImports.cshtml 文件导入到文件中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="ce3b7-228">有关详细信息，请参阅[管理标记帮助程序范围](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="ce3b7-229">Razor Pages 区域的共享布局</span><span class="sxs-lookup"><span data-stu-id="ce3b7-229">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="ce3b7-230">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="ce3b7-231">发布区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-231">Publishing Areas</span></span>

<span data-ttu-id="ce3b7-232">当 \*.csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 \*.cshtml 文件以及 wwwroot 目录中的文件都将发布到输出中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="ce3b7-233">区域是 ASP.NET 功能，用于将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="ce3b7-234">使用区域通过为 `controller` 和 `action` 或 Razor Page `page` 添加其他路由参数 `area`，创建用于路由目的的层次结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a Razor Page `page`.</span></span>

<span data-ttu-id="ce3b7-235">区域提供了一种将 ASP.NET Core Web 应用划分为更小的功能组的方法，每个功能组都有自己的一组 Razor Pages、控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of Razor Pages, controllers, views, and models.</span></span> <span data-ttu-id="ce3b7-236">区域实际上是应用内的结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="ce3b7-237">在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="ce3b7-238">ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="ce3b7-239">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="ce3b7-240">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="ce3b7-241">每个单位都有自己的区域，以包含视图、控制器、Razor Pages 和模型。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-241">Each of these units have their own area to contain views, controllers, Razor Pages, and models.</span></span>

<span data-ttu-id="ce3b7-242">如果发生以下情况，请考虑在项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="ce3b7-243">应用由可以进行逻辑分隔的多个高级功能组件组成。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="ce3b7-244">想对应用进行分区，以便可以独立处理每个功能区域。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="ce3b7-245">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="ce3b7-246">下载示例提供了用于测试区域的基本应用。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="ce3b7-247">如果使用 Razor Pages，请参阅本文档中的[使用 Razor Pages 的区域](#areas-with-razor-pages)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-247">If you're using Razor Pages, see [Areas with Razor Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="ce3b7-248">带视图的控制器区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-248">Areas for controllers with views</span></span>

<span data-ttu-id="ce3b7-249">使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="ce3b7-250">[区域文件夹结构](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="ce3b7-251">使用 [`[Area]`](#attribute) 属性将自己与区域关联的控制器：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="ce3b7-252">[添加到启动的区域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="ce3b7-253">区域文件夹结构</span><span class="sxs-lookup"><span data-stu-id="ce3b7-253">Area folder structure</span></span>

<span data-ttu-id="ce3b7-254">请考虑具有两个逻辑组（产品和服务）的应用。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-254">Consider an app that has two logical groups, *Products* and *Services*.</span></span> <span data-ttu-id="ce3b7-255">使用区域，文件夹结构类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="ce3b7-256">项目名称</span><span class="sxs-lookup"><span data-stu-id="ce3b7-256">Project name</span></span>
  * <span data-ttu-id="ce3b7-257">Areas</span><span class="sxs-lookup"><span data-stu-id="ce3b7-257">Areas</span></span>
    * <span data-ttu-id="ce3b7-258">Products</span><span class="sxs-lookup"><span data-stu-id="ce3b7-258">Products</span></span>
      * <span data-ttu-id="ce3b7-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="ce3b7-259">Controllers</span></span>
        * <span data-ttu-id="ce3b7-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="ce3b7-260">HomeController.cs</span></span>
        * <span data-ttu-id="ce3b7-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="ce3b7-261">ManageController.cs</span></span>
      * <span data-ttu-id="ce3b7-262">Views</span><span class="sxs-lookup"><span data-stu-id="ce3b7-262">Views</span></span>
        * <span data-ttu-id="ce3b7-263">Home</span><span class="sxs-lookup"><span data-stu-id="ce3b7-263">Home</span></span>
          * <span data-ttu-id="ce3b7-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-264">Index.cshtml</span></span>
        * <span data-ttu-id="ce3b7-265">Manage</span><span class="sxs-lookup"><span data-stu-id="ce3b7-265">Manage</span></span>
          * <span data-ttu-id="ce3b7-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-266">Index.cshtml</span></span>
          * <span data-ttu-id="ce3b7-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-267">About.cshtml</span></span>
    * <span data-ttu-id="ce3b7-268">Services</span><span class="sxs-lookup"><span data-stu-id="ce3b7-268">Services</span></span>
      * <span data-ttu-id="ce3b7-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="ce3b7-269">Controllers</span></span>
        * <span data-ttu-id="ce3b7-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="ce3b7-270">HomeController.cs</span></span>
      * <span data-ttu-id="ce3b7-271">视图</span><span class="sxs-lookup"><span data-stu-id="ce3b7-271">Views</span></span>
        * <span data-ttu-id="ce3b7-272">Home</span><span class="sxs-lookup"><span data-stu-id="ce3b7-272">Home</span></span>
          * <span data-ttu-id="ce3b7-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-273">Index.cshtml</span></span>

<span data-ttu-id="ce3b7-274">虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="ce3b7-275">视图发现按以下顺序搜索匹配的区域视图文件：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="ce3b7-276">将控制器与区域关联</span><span class="sxs-lookup"><span data-stu-id="ce3b7-276">Associate the controller with an Area</span></span>

<span data-ttu-id="ce3b7-277">使用[&lbrack;区域&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)属性指定区域控制器：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="ce3b7-278">添加区域路由</span><span class="sxs-lookup"><span data-stu-id="ce3b7-278">Add Area route</span></span>

<span data-ttu-id="ce3b7-279">区域路由通常使用传统路由，而不使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="ce3b7-280">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="ce3b7-281">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="ce3b7-282">如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="ce3b7-283">在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="ce3b7-284">使用 `{area:...}` 是将路由添加到区域的最简单的机制。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="ce3b7-285">以下代码使用 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 创建两个命名区域路由：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="ce3b7-286">将 `MapAreaRoute` 与 ASP.NET Core 2.2 配合使用时，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/7772)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="ce3b7-287">有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="ce3b7-288">MVC 区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="ce3b7-288">Link generation with MVC areas</span></span>

<span data-ttu-id="ce3b7-289">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)中的以下代码显示指定区域的链接生成：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="ce3b7-290">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="ce3b7-291">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="ce3b7-292">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="ce3b7-293">在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="ce3b7-294">如果未指定区域或控制器，路由取决于环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="ce3b7-295">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="ce3b7-296">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="ce3b7-297">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="ce3b7-298">使用 _ViewStart.cshtml 文件的共享区域布局</span><span class="sxs-lookup"><span data-stu-id="ce3b7-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="ce3b7-299">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="ce3b7-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="ce3b7-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="ce3b7-301">在其标准位置，/Views/_ViewImports.cshtml 不适用于区域。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="ce3b7-302">若要在区域中使用常用的[标记帮助程序](xref:mvc/views/tag-helpers/intro)、`@using` 或 `@inject`，确保将正确的 _ViewImports.cshtml 文件[应用于区域视图](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="ce3b7-303">如果希望所有视图都具有相同的行为，请将 /Views/_ViewImports.cshtml 迁移到应用程序根。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="ce3b7-304">更改存储视图的默认区域文件夹</span><span class="sxs-lookup"><span data-stu-id="ce3b7-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="ce3b7-305">以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-razor-pages"></a><span data-ttu-id="ce3b7-306">使用 Razor Pages 的区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-306">Areas with Razor Pages</span></span>

<span data-ttu-id="ce3b7-307">具有 Razor Pages 的区域需要应用根目录中的 `Areas/<area name>/Pages` 文件夹。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-307">Areas with Razor Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="ce3b7-308">以下文件夹结构用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="ce3b7-309">项目名称</span><span class="sxs-lookup"><span data-stu-id="ce3b7-309">Project name</span></span>
  * <span data-ttu-id="ce3b7-310">Areas</span><span class="sxs-lookup"><span data-stu-id="ce3b7-310">Areas</span></span>
    * <span data-ttu-id="ce3b7-311">Products</span><span class="sxs-lookup"><span data-stu-id="ce3b7-311">Products</span></span>
      * <span data-ttu-id="ce3b7-312">Pages</span><span class="sxs-lookup"><span data-stu-id="ce3b7-312">Pages</span></span>
        * <span data-ttu-id="ce3b7-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="ce3b7-313">_ViewImports</span></span>
        * <span data-ttu-id="ce3b7-314">About</span><span class="sxs-lookup"><span data-stu-id="ce3b7-314">About</span></span>
        * <span data-ttu-id="ce3b7-315">Index</span><span class="sxs-lookup"><span data-stu-id="ce3b7-315">Index</span></span>
    * <span data-ttu-id="ce3b7-316">Services</span><span class="sxs-lookup"><span data-stu-id="ce3b7-316">Services</span></span>
      * <span data-ttu-id="ce3b7-317">Pages</span><span class="sxs-lookup"><span data-stu-id="ce3b7-317">Pages</span></span>
        * <span data-ttu-id="ce3b7-318">Manage</span><span class="sxs-lookup"><span data-stu-id="ce3b7-318">Manage</span></span>
          * <span data-ttu-id="ce3b7-319">About</span><span class="sxs-lookup"><span data-stu-id="ce3b7-319">About</span></span>
          * <span data-ttu-id="ce3b7-320">Index</span><span class="sxs-lookup"><span data-stu-id="ce3b7-320">Index</span></span>

### <a name="link-generation-with-razor-pages-and-areas"></a><span data-ttu-id="ce3b7-321">Razor Pages 和区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="ce3b7-321">Link generation with Razor Pages and areas</span></span>

<span data-ttu-id="ce3b7-322">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)中的以下代码显示指定区域（例如 `asp-area="Products"`）的链接生成：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="ce3b7-323">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="ce3b7-324">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="ce3b7-325">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="ce3b7-326">在未指定区域的情况下生成的链接仅在从同一区域中的页引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="ce3b7-327">如果未指定区域，路由取决于环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="ce3b7-328">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="ce3b7-329">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="ce3b7-330">例如，考虑从下面的代码生成的链接：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="ce3b7-331">对于上述代码：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-331">For the preceding code:</span></span>

* <span data-ttu-id="ce3b7-332">只有当最后一个请求是针对 `Services` 区域的页时，从 `<a asp-page="/Manage/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="ce3b7-333">例如 `/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="ce3b7-334">只有当最后一个请求是针对 `/Home` 中的页时，从 `<a asp-page="/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="ce3b7-335">代码摘自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="ce3b7-336">使用 _ViewImports 文件导入命名空间和标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="ce3b7-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="ce3b7-337">可向每个区域“页面”文件夹添加一个 _ViewImports.cshtml 文件，以将命名空间和标记帮助器导入到该文件夹的每个 Razor 页面中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each Razor Page in the folder.</span></span>

<span data-ttu-id="ce3b7-338">请考虑使用示例代码的“服务”区域，它不包含 _ViewImports.cshtml 文件。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="ce3b7-339">以下标记显示 /Services/Manage/About Razor Page：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-339">The following markup shows the */Services/Manage/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="ce3b7-340">在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-340">In the preceding markup:</span></span>

* <span data-ttu-id="ce3b7-341">必须使用完全限定的域名来指定模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="ce3b7-342">[标记帮助程序](xref:mvc/views/tag-helpers/intro)由 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers` 启动</span><span class="sxs-lookup"><span data-stu-id="ce3b7-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="ce3b7-343">在示例下载中，“产品”区域包含下列 _ViewImports.cshtml 文件：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="ce3b7-344">以下标记显示 /Products/About Razor Page：</span><span class="sxs-lookup"><span data-stu-id="ce3b7-344">The following markup shows the */Products/About* Razor Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="ce3b7-345">在前面的文件中，命名空间和 `@addTagHelper` 指令通过 Areas/Products/Pages/_ViewImports.cshtml 文件导入到文件中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="ce3b7-346">有关详细信息，请参阅[管理标记帮助程序范围](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-razor-pages-areas"></a><span data-ttu-id="ce3b7-347">Razor Pages 区域的共享布局</span><span class="sxs-lookup"><span data-stu-id="ce3b7-347">Shared layout for Razor Pages Areas</span></span>

<span data-ttu-id="ce3b7-348">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="ce3b7-349">发布区域</span><span class="sxs-lookup"><span data-stu-id="ce3b7-349">Publishing Areas</span></span>

<span data-ttu-id="ce3b7-350">当 \*.csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 \*.cshtml 文件以及 wwwroot 目录中的文件都将发布到输出中。</span><span class="sxs-lookup"><span data-stu-id="ce3b7-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
