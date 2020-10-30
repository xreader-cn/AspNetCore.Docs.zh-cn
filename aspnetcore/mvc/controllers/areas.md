---
title: ASP.NET Core 中的区域
author: rick-anderson
description: 了解 ASP.NET MVC 的区域功能如何将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。
ms.author: riande
ms.date: 03/21/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: mvc/controllers/areas
ms.openlocfilehash: 42eec406813adce4d7edbc1ab66a1f689c4aca0e
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93053522"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="be36c-103">ASP.NET Core 中的区域</span><span class="sxs-lookup"><span data-stu-id="be36c-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="be36c-104">作者：[Dhananjay Kumar](https://twitter.com/debug_mode) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="be36c-104">By [Dhananjay Kumar](https://twitter.com/debug_mode) and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="be36c-105">区域是一种 ASP.NET 功能，用于将相关功能作为单独的组进行组织：</span><span class="sxs-lookup"><span data-stu-id="be36c-105">Areas are an ASP.NET feature used to organize related functionality into a group as a separate:</span></span>

* <span data-ttu-id="be36c-106">用于路由的命名空间。</span><span class="sxs-lookup"><span data-stu-id="be36c-106">Namespace for routing.</span></span>
* <span data-ttu-id="be36c-107">视图和页面的文件夹结构 :::no-loc(Razor)::: 。</span><span class="sxs-lookup"><span data-stu-id="be36c-107">Folder structure for views and :::no-loc(Razor)::: Pages.</span></span>

<span data-ttu-id="be36c-108">使用区域通过向 `area` `controller` 和 `action` 或 :::no-loc(Razor)::: 页添加另一个路由参数来创建用于路由目的的层次结构 `page` 。</span><span class="sxs-lookup"><span data-stu-id="be36c-108">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="be36c-109">区域提供了一种方法，用于将 ASP.NET Core Web 应用划分为较小的功能组，每个组都有自己的一组 :::no-loc(Razor)::: 页、控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="be36c-109">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="be36c-110">区域实际上是应用内的结构。</span><span class="sxs-lookup"><span data-stu-id="be36c-110">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="be36c-111">在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="be36c-111">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="be36c-112">ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="be36c-112">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="be36c-113">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="be36c-113">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="be36c-114">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。</span><span class="sxs-lookup"><span data-stu-id="be36c-114">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="be36c-115">其中每个单位都有各自的区域来包含视图、控制器、 :::no-loc(Razor)::: 页面和模型。</span><span class="sxs-lookup"><span data-stu-id="be36c-115">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="be36c-116">如果发生以下情况，请考虑在项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="be36c-116">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="be36c-117">应用由可以进行逻辑分隔的多个高级功能组件组成。</span><span class="sxs-lookup"><span data-stu-id="be36c-117">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="be36c-118">想对应用进行分区，以便可以独立处理每个功能区域。</span><span class="sxs-lookup"><span data-stu-id="be36c-118">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="be36c-119">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="be36c-119">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="be36c-120">下载示例提供了用于测试区域的基本应用。</span><span class="sxs-lookup"><span data-stu-id="be36c-120">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="be36c-121">如果使用的是 :::no-loc(Razor)::: 页面，请参阅本文档中 [包含 :::no-loc(Razor)::: 页面的区域](#areas-with-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="be36c-121">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="be36c-122">带视图的控制器区域</span><span class="sxs-lookup"><span data-stu-id="be36c-122">Areas for controllers with views</span></span>

<span data-ttu-id="be36c-123">使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="be36c-123">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="be36c-124">[区域文件夹结构](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="be36c-124">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="be36c-125">具有属性的控制器 [`[Area]`](#attribute) 用于将控制器与区域关联：</span><span class="sxs-lookup"><span data-stu-id="be36c-125">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="be36c-126">[添加到启动的区域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="be36c-126">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="be36c-127">区域文件夹结构</span><span class="sxs-lookup"><span data-stu-id="be36c-127">Area folder structure</span></span>

<span data-ttu-id="be36c-128">请考虑具有两个逻辑组（产品和服务）的应用  。</span><span class="sxs-lookup"><span data-stu-id="be36c-128">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="be36c-129">使用区域，文件夹结构类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="be36c-129">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="be36c-130">项目名称</span><span class="sxs-lookup"><span data-stu-id="be36c-130">Project name</span></span>
  * <span data-ttu-id="be36c-131">Areas</span><span class="sxs-lookup"><span data-stu-id="be36c-131">Areas</span></span>
    * <span data-ttu-id="be36c-132">产品</span><span class="sxs-lookup"><span data-stu-id="be36c-132">Products</span></span>
      * <span data-ttu-id="be36c-133">Controllers</span><span class="sxs-lookup"><span data-stu-id="be36c-133">Controllers</span></span>
        * <span data-ttu-id="be36c-134">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="be36c-134">HomeController.cs</span></span>
        * <span data-ttu-id="be36c-135">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="be36c-135">ManageController.cs</span></span>
      * <span data-ttu-id="be36c-136">视图</span><span class="sxs-lookup"><span data-stu-id="be36c-136">Views</span></span>
        * <span data-ttu-id="be36c-137">主页</span><span class="sxs-lookup"><span data-stu-id="be36c-137">Home</span></span>
          * <span data-ttu-id="be36c-138">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-138">Index.cshtml</span></span>
        * <span data-ttu-id="be36c-139">管理</span><span class="sxs-lookup"><span data-stu-id="be36c-139">Manage</span></span>
          * <span data-ttu-id="be36c-140">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-140">Index.cshtml</span></span>
          * <span data-ttu-id="be36c-141">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-141">About.cshtml</span></span>
    * <span data-ttu-id="be36c-142">服务</span><span class="sxs-lookup"><span data-stu-id="be36c-142">Services</span></span>
      * <span data-ttu-id="be36c-143">Controllers</span><span class="sxs-lookup"><span data-stu-id="be36c-143">Controllers</span></span>
        * <span data-ttu-id="be36c-144">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="be36c-144">HomeController.cs</span></span>
      * <span data-ttu-id="be36c-145">视图</span><span class="sxs-lookup"><span data-stu-id="be36c-145">Views</span></span>
        * <span data-ttu-id="be36c-146">主页</span><span class="sxs-lookup"><span data-stu-id="be36c-146">Home</span></span>
          * <span data-ttu-id="be36c-147">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-147">Index.cshtml</span></span>

<span data-ttu-id="be36c-148">虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="be36c-148">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="be36c-149">视图发现按以下顺序搜索匹配的区域视图文件：</span><span class="sxs-lookup"><span data-stu-id="be36c-149">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="be36c-150">将控制器与区域关联</span><span class="sxs-lookup"><span data-stu-id="be36c-150">Associate the controller with an Area</span></span>

<span data-ttu-id="be36c-151">区域控制器是用[ &lbrack; area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)特性指定的：</span><span class="sxs-lookup"><span data-stu-id="be36c-151">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="be36c-152">添加区域路由</span><span class="sxs-lookup"><span data-stu-id="be36c-152">Add Area route</span></span>

<span data-ttu-id="be36c-153">区域路由通常使用  [传统路由](xref:mvc/controllers/routing#cr) ，而不是 [属性路由](xref:mvc/controllers/routing#ar)。</span><span class="sxs-lookup"><span data-stu-id="be36c-153">Area routes typically use  [conventional routing](xref:mvc/controllers/routing#cr) rather than [attribute routing](xref:mvc/controllers/routing#ar).</span></span> <span data-ttu-id="be36c-154">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="be36c-154">Conventional routing is order-dependent.</span></span> <span data-ttu-id="be36c-155">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="be36c-155">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="be36c-156">如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：</span><span class="sxs-lookup"><span data-stu-id="be36c-156">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup.cs?name=snippet&highlight=21-23)]

<span data-ttu-id="be36c-157">在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="be36c-157">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="be36c-158">使用 `{area:...}` `MapControllerRoute` ：</span><span class="sxs-lookup"><span data-stu-id="be36c-158">Using `{area:...}` with `MapControllerRoute`:</span></span>

* <span data-ttu-id="be36c-159">是将路由添加到区域的最不复杂的机制。</span><span class="sxs-lookup"><span data-stu-id="be36c-159">Is the least complicated mechanism to adding routing to areas.</span></span>
* <span data-ttu-id="be36c-160">将所有控制器与属性进行匹配 `[Area("Area name")]` 。</span><span class="sxs-lookup"><span data-stu-id="be36c-160">Matches all controllers with the `[Area("Area name")]` attribute.</span></span>

<span data-ttu-id="be36c-161">以下代码使用 <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> 创建两个命名区域路由：</span><span class="sxs-lookup"><span data-stu-id="be36c-161">The following code uses <xref:Microsoft.AspNetCore.Builder.ControllerEndpointRouteBuilderExtensions.MapAreaControllerRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/31samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=21-29)]

<span data-ttu-id="be36c-162">有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="be36c-162">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="be36c-163">MVC 区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="be36c-163">Link generation with MVC areas</span></span>

<span data-ttu-id="be36c-164">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)中的以下代码显示指定区域的链接生成：</span><span class="sxs-lookup"><span data-stu-id="be36c-164">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/31samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="be36c-165">示例下载包含 [部分视图](xref:mvc/views/partial) ，其中包含：</span><span class="sxs-lookup"><span data-stu-id="be36c-165">The sample download includes a [partial view](xref:mvc/views/partial) that contains:</span></span>

* <span data-ttu-id="be36c-166">前面的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-166">The preceding links.</span></span>
* <span data-ttu-id="be36c-167">与前面的链接相似，但 `area` 未指定。</span><span class="sxs-lookup"><span data-stu-id="be36c-167">Links similar to the preceding except `area` is not specified.</span></span>

<span data-ttu-id="be36c-168">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-168">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="be36c-169">在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="be36c-169">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="be36c-170">如果未指定区域或控制器，路由取决于环境值[](xref:mvc/controllers/routing#ambient)。</span><span class="sxs-lookup"><span data-stu-id="be36c-170">When the area or controller is not specified, routing depends on the [ambient](xref:mvc/controllers/routing#ambient) values.</span></span> <span data-ttu-id="be36c-171">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="be36c-171">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="be36c-172">在许多情况下，对于示例应用程序，使用环境值时，将生成带有未指定区域的标记的错误链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-172">In many cases for the sample app, using the ambient values generates incorrect links with the markup that doesn't specify the area.</span></span>

<span data-ttu-id="be36c-173">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="be36c-173">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="be36c-174">使用 _ViewStart.cshtml 文件的共享区域布局</span><span class="sxs-lookup"><span data-stu-id="be36c-174">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="be36c-175">若要为整个应用共享公共布局，请在 [应用程序根文件夹](#arf)中保留 *_ViewStart。*</span><span class="sxs-lookup"><span data-stu-id="be36c-175">To share a common layout for the entire app, keep the *_ViewStart.cshtml* in the [application root folder](#arf).</span></span> <span data-ttu-id="be36c-176">有关详细信息，请参阅<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="be36c-176">For more information, see <xref:mvc/views/layout></span></span>

<a name="arf"></a>

### <a name="application-root-folder"></a><span data-ttu-id="be36c-177">应用程序根文件夹</span><span class="sxs-lookup"><span data-stu-id="be36c-177">Application root folder</span></span>

<span data-ttu-id="be36c-178">应用程序根文件夹是包含 ASP.NET Core 模板创建的 web 应用中的 *Startup.cs* 的文件夹。</span><span class="sxs-lookup"><span data-stu-id="be36c-178">The application root folder is the folder containing *Startup.cs* in web app created with the ASP.NET Core templates.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="be36c-179">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-179">_ViewImports.cshtml</span></span>

 <span data-ttu-id="be36c-180">对于 MVC， *_ViewImports 的/Views/* 和 */Pages/_ViewImports* :::no-loc(Razor)::: ，不会导入到区域中的视图。</span><span class="sxs-lookup"><span data-stu-id="be36c-180">*/Views/_ViewImports.cshtml* , for MVC, and */Pages/_ViewImports.cshtml* for :::no-loc(Razor)::: Pages, is not imported to views in areas.</span></span> <span data-ttu-id="be36c-181">使用以下方法之一来提供所有视图的视图导入：</span><span class="sxs-lookup"><span data-stu-id="be36c-181">Use one of the following approaches to provide view imports to all views:</span></span>

* <span data-ttu-id="be36c-182">将 *_ViewImports* 添加到 [应用程序根文件夹](#arf)。</span><span class="sxs-lookup"><span data-stu-id="be36c-182">Add *_ViewImports.cshtml* to the [application root folder](#arf).</span></span> <span data-ttu-id="be36c-183">应用程序根文件夹中的 *_ViewImports* 将应用到应用中的所有视图。</span><span class="sxs-lookup"><span data-stu-id="be36c-183">A *_ViewImports.cshtml* in the application root folder will apply to all views in the app.</span></span>
* <span data-ttu-id="be36c-184">将 *_ViewImports* 的文件复制到 "区域" 下的相应视图文件夹中。</span><span class="sxs-lookup"><span data-stu-id="be36c-184">Copy the *_ViewImports.cshtml* file to the appropriate view folder under areas.</span></span>

<span data-ttu-id="be36c-185">*_ViewImports 的 cshtml* 文件通常包含 [标记帮助](xref:mvc/views/tag-helpers/intro)程序导入、 `@using` 和 `@inject` 语句。</span><span class="sxs-lookup"><span data-stu-id="be36c-185">The *_ViewImports.cshtml* file typically contains [Tag Helpers](xref:mvc/views/tag-helpers/intro) imports, `@using`, and `@inject` statements.</span></span> <span data-ttu-id="be36c-186">有关详细信息，请参阅 [导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="be36c-186">For more information, see [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="be36c-187">更改存储视图的默认区域文件夹</span><span class="sxs-lookup"><span data-stu-id="be36c-187">Change default area folder where views are stored</span></span>

<span data-ttu-id="be36c-188">以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="be36c-188">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/31samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="be36c-189">包含页面的区域 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="be36c-189">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="be36c-190">包含页面的区域 :::no-loc(Razor)::: `Areas/<area name>/Pages` 在应用的根目录中需要一个文件夹。</span><span class="sxs-lookup"><span data-stu-id="be36c-190">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="be36c-191">以下文件夹结构用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples)：</span><span class="sxs-lookup"><span data-stu-id="be36c-191">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples):</span></span>

* <span data-ttu-id="be36c-192">项目名称</span><span class="sxs-lookup"><span data-stu-id="be36c-192">Project name</span></span>
  * <span data-ttu-id="be36c-193">Areas</span><span class="sxs-lookup"><span data-stu-id="be36c-193">Areas</span></span>
    * <span data-ttu-id="be36c-194">产品</span><span class="sxs-lookup"><span data-stu-id="be36c-194">Products</span></span>
      * <span data-ttu-id="be36c-195">页数</span><span class="sxs-lookup"><span data-stu-id="be36c-195">Pages</span></span>
        * <span data-ttu-id="be36c-196">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="be36c-196">_ViewImports</span></span>
        * <span data-ttu-id="be36c-197">关于</span><span class="sxs-lookup"><span data-stu-id="be36c-197">About</span></span>
        * <span data-ttu-id="be36c-198">索引</span><span class="sxs-lookup"><span data-stu-id="be36c-198">Index</span></span>
    * <span data-ttu-id="be36c-199">服务</span><span class="sxs-lookup"><span data-stu-id="be36c-199">Services</span></span>
      * <span data-ttu-id="be36c-200">页数</span><span class="sxs-lookup"><span data-stu-id="be36c-200">Pages</span></span>
        * <span data-ttu-id="be36c-201">管理</span><span class="sxs-lookup"><span data-stu-id="be36c-201">Manage</span></span>
          * <span data-ttu-id="be36c-202">关于</span><span class="sxs-lookup"><span data-stu-id="be36c-202">About</span></span>
          * <span data-ttu-id="be36c-203">索引</span><span class="sxs-lookup"><span data-stu-id="be36c-203">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="be36c-204">与 :::no-loc(Razor)::: 页面和区域一起生成链接</span><span class="sxs-lookup"><span data-stu-id="be36c-204">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="be36c-205">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)中的以下代码显示指定区域（例如 `asp-area="Products"`）的链接生成：</span><span class="sxs-lookup"><span data-stu-id="be36c-205">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="be36c-206">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="be36c-206">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="be36c-207">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-207">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="be36c-208">在未指定区域的情况下生成的链接仅在从同一区域中的页引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="be36c-208">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="be36c-209">如果未指定区域，路由取决于环境  值。</span><span class="sxs-lookup"><span data-stu-id="be36c-209">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="be36c-210">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="be36c-210">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="be36c-211">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-211">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="be36c-212">例如，考虑从下面的代码生成的链接：</span><span class="sxs-lookup"><span data-stu-id="be36c-212">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="be36c-213">对于上述代码：</span><span class="sxs-lookup"><span data-stu-id="be36c-213">For the preceding code:</span></span>

* <span data-ttu-id="be36c-214">只有当最后一个请求是针对 `Services` 区域的页时，从 `<a asp-page="/Manage/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="be36c-214">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="be36c-215">例如，`/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="be36c-215">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="be36c-216">只有当最后一个请求是针对 `/Home` 中的页时，从 `<a asp-page="/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="be36c-216">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="be36c-217">代码摘自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="be36c-217">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/31samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="be36c-218">使用 _ViewImports 文件导入命名空间和标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="be36c-218">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="be36c-219">可以将 *_ViewImports 的 cshtml* 文件添加到每个区域 *页面* 文件夹，以将命名空间和标记帮助程序导入到文件夹中的每一 :::no-loc(Razor)::: 页。</span><span class="sxs-lookup"><span data-stu-id="be36c-219">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="be36c-220">请考虑使用示例代码的“服务”区域，它不包含 _ViewImports.cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="be36c-220">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="be36c-221">以下标记显示了 " */Services/Manage/About* " :::no-loc(Razor)::: 页：</span><span class="sxs-lookup"><span data-stu-id="be36c-221">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="be36c-222">在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="be36c-222">In the preceding markup:</span></span>

* <span data-ttu-id="be36c-223">必须使用完全限定的域名来指定模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="be36c-223">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="be36c-224">[标记帮助](xref:mvc/views/tag-helpers/intro) 程序由启用 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="be36c-224">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="be36c-225">在示例下载中，“产品”区域包含下列 _ViewImports.cshtml 文件  ：</span><span class="sxs-lookup"><span data-stu-id="be36c-225">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="be36c-226">以下标记显示了 " */Products/About* " :::no-loc(Razor)::: 页：</span><span class="sxs-lookup"><span data-stu-id="be36c-226">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/31samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="be36c-227">在前面的文件中，将 `@addTagHelper` 按 *区域/产品/页面/_ViewImports cshtml* 文件将命名空间和指令导入文件。</span><span class="sxs-lookup"><span data-stu-id="be36c-227">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="be36c-228">有关详细信息，请参阅[管理标记帮助程序范围](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="be36c-228">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="be36c-229">页面区域的共享布局 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="be36c-229">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="be36c-230">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹  。</span><span class="sxs-lookup"><span data-stu-id="be36c-230">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="be36c-231">发布区域</span><span class="sxs-lookup"><span data-stu-id="be36c-231">Publishing Areas</span></span>

<span data-ttu-id="be36c-232">当 \*.csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 \*.cshtml 文件以及 wwwroot 目录中的文件都将发布到输出中  。</span><span class="sxs-lookup"><span data-stu-id="be36c-232">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="be36c-233">区域是 ASP.NET 功能，用于将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。</span><span class="sxs-lookup"><span data-stu-id="be36c-233">Areas are an ASP.NET feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="be36c-234">使用区域通过向 `area` `controller` 和 `action` 或 :::no-loc(Razor)::: 页添加另一个路由参数来创建用于路由目的的层次结构 `page` 。</span><span class="sxs-lookup"><span data-stu-id="be36c-234">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action` or a :::no-loc(Razor)::: Page `page`.</span></span>

<span data-ttu-id="be36c-235">区域提供了一种方法，用于将 ASP.NET Core Web 应用划分为较小的功能组，每个组都有自己的一组 :::no-loc(Razor)::: 页、控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="be36c-235">Areas provide a way to partition an ASP.NET Core Web app into smaller functional groups, each  with its own set of :::no-loc(Razor)::: Pages, controllers, views, and models.</span></span> <span data-ttu-id="be36c-236">区域实际上是应用内的结构。</span><span class="sxs-lookup"><span data-stu-id="be36c-236">An area is effectively a structure inside an app.</span></span> <span data-ttu-id="be36c-237">在 ASP.NET Core Web 项目中，Pages、模型、控制器和视图等逻辑组件保存在不同的文件夹中。</span><span class="sxs-lookup"><span data-stu-id="be36c-237">In an ASP.NET Core web project, logical components like Pages, Model, Controller, and View are kept in different folders.</span></span> <span data-ttu-id="be36c-238">ASP.NET Core 运行时使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="be36c-238">The ASP.NET Core runtime uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="be36c-239">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="be36c-239">For a large app, it may be advantageous to partition the app into separate high level areas of functionality.</span></span> <span data-ttu-id="be36c-240">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。</span><span class="sxs-lookup"><span data-stu-id="be36c-240">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search.</span></span> <span data-ttu-id="be36c-241">其中每个单位都有各自的区域来包含视图、控制器、 :::no-loc(Razor)::: 页面和模型。</span><span class="sxs-lookup"><span data-stu-id="be36c-241">Each of these units have their own area to contain views, controllers, :::no-loc(Razor)::: Pages, and models.</span></span>

<span data-ttu-id="be36c-242">如果发生以下情况，请考虑在项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="be36c-242">Consider using Areas in a project when:</span></span>

* <span data-ttu-id="be36c-243">应用由可以进行逻辑分隔的多个高级功能组件组成。</span><span class="sxs-lookup"><span data-stu-id="be36c-243">The app is made of multiple high-level functional components that can be logically separated.</span></span>
* <span data-ttu-id="be36c-244">想对应用进行分区，以便可以独立处理每个功能区域。</span><span class="sxs-lookup"><span data-stu-id="be36c-244">You want to partition the app so that each functional area can be worked on independently.</span></span>

<span data-ttu-id="be36c-245">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)（[如何下载](xref:index#how-to-download-a-sample)）。</span><span class="sxs-lookup"><span data-stu-id="be36c-245">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) ([how to download](xref:index#how-to-download-a-sample)).</span></span> <span data-ttu-id="be36c-246">下载示例提供了用于测试区域的基本应用。</span><span class="sxs-lookup"><span data-stu-id="be36c-246">The download sample provides a basic app for testing areas.</span></span>

<span data-ttu-id="be36c-247">如果使用的是 :::no-loc(Razor)::: 页面，请参阅本文档中 [包含 :::no-loc(Razor)::: 页面的区域](#areas-with-razor-pages) 。</span><span class="sxs-lookup"><span data-stu-id="be36c-247">If you're using :::no-loc(Razor)::: Pages, see [Areas with :::no-loc(Razor)::: Pages](#areas-with-razor-pages) in this document.</span></span>

## <a name="areas-for-controllers-with-views"></a><span data-ttu-id="be36c-248">带视图的控制器区域</span><span class="sxs-lookup"><span data-stu-id="be36c-248">Areas for controllers with views</span></span>

<span data-ttu-id="be36c-249">使用区域、控制器和视图的典型 ASP.NET Core Web 应用包含以下内容：</span><span class="sxs-lookup"><span data-stu-id="be36c-249">A typical ASP.NET Core web app using areas, controllers, and views contains the following:</span></span>

* <span data-ttu-id="be36c-250">[区域文件夹结构](#area-folder-structure)。</span><span class="sxs-lookup"><span data-stu-id="be36c-250">An [Area folder structure](#area-folder-structure).</span></span>
* <span data-ttu-id="be36c-251">具有属性的控制器 [`[Area]`](#attribute) 用于将控制器与区域关联：</span><span class="sxs-lookup"><span data-stu-id="be36c-251">Controllers with the [`[Area]`](#attribute) attribute to associate the controller with the area:</span></span>

  [!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?name=snippet2)]

* <span data-ttu-id="be36c-252">[添加到启动的区域路由](#add-area-route)：</span><span class="sxs-lookup"><span data-stu-id="be36c-252">The [area route added to startup](#add-area-route):</span></span>

  [!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet2&highlight=3-6)]

### <a name="area-folder-structure"></a><span data-ttu-id="be36c-253">区域文件夹结构</span><span class="sxs-lookup"><span data-stu-id="be36c-253">Area folder structure</span></span>

<span data-ttu-id="be36c-254">请考虑具有两个逻辑组（产品和服务）的应用  。</span><span class="sxs-lookup"><span data-stu-id="be36c-254">Consider an app that has two logical groups, *Products* and *Services* .</span></span> <span data-ttu-id="be36c-255">使用区域，文件夹结构类似于以下内容：</span><span class="sxs-lookup"><span data-stu-id="be36c-255">Using areas, the folder structure would be similar to the following:</span></span>

* <span data-ttu-id="be36c-256">项目名称</span><span class="sxs-lookup"><span data-stu-id="be36c-256">Project name</span></span>
  * <span data-ttu-id="be36c-257">Areas</span><span class="sxs-lookup"><span data-stu-id="be36c-257">Areas</span></span>
    * <span data-ttu-id="be36c-258">产品</span><span class="sxs-lookup"><span data-stu-id="be36c-258">Products</span></span>
      * <span data-ttu-id="be36c-259">Controllers</span><span class="sxs-lookup"><span data-stu-id="be36c-259">Controllers</span></span>
        * <span data-ttu-id="be36c-260">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="be36c-260">HomeController.cs</span></span>
        * <span data-ttu-id="be36c-261">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="be36c-261">ManageController.cs</span></span>
      * <span data-ttu-id="be36c-262">视图</span><span class="sxs-lookup"><span data-stu-id="be36c-262">Views</span></span>
        * <span data-ttu-id="be36c-263">主页</span><span class="sxs-lookup"><span data-stu-id="be36c-263">Home</span></span>
          * <span data-ttu-id="be36c-264">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-264">Index.cshtml</span></span>
        * <span data-ttu-id="be36c-265">管理</span><span class="sxs-lookup"><span data-stu-id="be36c-265">Manage</span></span>
          * <span data-ttu-id="be36c-266">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-266">Index.cshtml</span></span>
          * <span data-ttu-id="be36c-267">About.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-267">About.cshtml</span></span>
    * <span data-ttu-id="be36c-268">服务</span><span class="sxs-lookup"><span data-stu-id="be36c-268">Services</span></span>
      * <span data-ttu-id="be36c-269">Controllers</span><span class="sxs-lookup"><span data-stu-id="be36c-269">Controllers</span></span>
        * <span data-ttu-id="be36c-270">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="be36c-270">HomeController.cs</span></span>
      * <span data-ttu-id="be36c-271">视图</span><span class="sxs-lookup"><span data-stu-id="be36c-271">Views</span></span>
        * <span data-ttu-id="be36c-272">主页</span><span class="sxs-lookup"><span data-stu-id="be36c-272">Home</span></span>
          * <span data-ttu-id="be36c-273">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-273">Index.cshtml</span></span>

<span data-ttu-id="be36c-274">虽然前面的布局是使用区域时的典型布局，但只需要视图文件即可使用此文件夹结构。</span><span class="sxs-lookup"><span data-stu-id="be36c-274">While the preceding layout is typical when using Areas, only the view files are required to use this folder structure.</span></span> <span data-ttu-id="be36c-275">视图发现按以下顺序搜索匹配的区域视图文件：</span><span class="sxs-lookup"><span data-stu-id="be36c-275">View discovery searches for a matching area view file in the following order:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
/Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
/Views/Shared/<Action-Name>.cshtml
/Pages/Shared/<Action-Name>.cshtml
```

<a name="attribute"></a>

### <a name="associate-the-controller-with-an-area"></a><span data-ttu-id="be36c-276">将控制器与区域关联</span><span class="sxs-lookup"><span data-stu-id="be36c-276">Associate the controller with an Area</span></span>

<span data-ttu-id="be36c-277">区域控制器是用[ &lbrack; area &rbrack; ](xref:Microsoft.AspNetCore.Mvc.AreaAttribute)特性指定的：</span><span class="sxs-lookup"><span data-stu-id="be36c-277">Area controllers are designated with the [&lbrack;Area&rbrack;](xref:Microsoft.AspNetCore.Mvc.AreaAttribute) attribute:</span></span>

[!code-csharp[](areas/samples/MVCareas/Areas/Products/Controllers/ManageController.cs?highlight=5&name=snippet)]

### <a name="add-area-route"></a><span data-ttu-id="be36c-278">添加区域路由</span><span class="sxs-lookup"><span data-stu-id="be36c-278">Add Area route</span></span>

<span data-ttu-id="be36c-279">区域路由通常使用传统路由，而不使用属性路由。</span><span class="sxs-lookup"><span data-stu-id="be36c-279">Area routes typically use conventional routing rather than attribute routing.</span></span> <span data-ttu-id="be36c-280">传统路由依赖于顺序。</span><span class="sxs-lookup"><span data-stu-id="be36c-280">Conventional routing is order-dependent.</span></span> <span data-ttu-id="be36c-281">一般情况下，具有区域的路由应放在路由表中靠前的位置，因为它们比没有区域的路由更特定。</span><span class="sxs-lookup"><span data-stu-id="be36c-281">In general, routes with areas should be placed earlier in the route table as they're more specific than routes without an area.</span></span>

<span data-ttu-id="be36c-282">如果所有区域的 url 空间一致，则 `{area:...}` 可用作路由模板中的令牌：</span><span class="sxs-lookup"><span data-stu-id="be36c-282">`{area:...}` can be used as a token in route templates if url space is uniform across all areas:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup.cs?name=snippet&highlight=18-21)]

<span data-ttu-id="be36c-283">在前面的代码中，`exists` 应用了路由必须与区域匹配的约束。</span><span class="sxs-lookup"><span data-stu-id="be36c-283">In the preceding code, `exists` applies a constraint that the route must match an area.</span></span> <span data-ttu-id="be36c-284">使用 `{area:...}` 是将路由添加到区域的最简单的机制。</span><span class="sxs-lookup"><span data-stu-id="be36c-284">Using `{area:...}` is the least complicated mechanism to adding routing to areas.</span></span>

<span data-ttu-id="be36c-285">以下代码使用 <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> 创建两个命名区域路由：</span><span class="sxs-lookup"><span data-stu-id="be36c-285">The following code uses <xref:Microsoft.AspNetCore.Builder.MvcAreaRouteBuilderExtensions.MapAreaRoute*> to create two named area routes:</span></span>

[!code-csharp[](areas/samples/MVCareas/StartupMapAreaRoute.cs?name=snippet&highlight=18-27)]

<span data-ttu-id="be36c-286">将 `MapAreaRoute` 与 ASP.NET Core 2.2 配合使用时，请参阅[此 GitHub 问题](https://github.com/dotnet/AspNetCore/issues/7772)。</span><span class="sxs-lookup"><span data-stu-id="be36c-286">When using `MapAreaRoute` with ASP.NET Core 2.2, see [this GitHub issue](https://github.com/dotnet/AspNetCore/issues/7772).</span></span>

<span data-ttu-id="be36c-287">有关详细信息，请参阅[区域路由](xref:mvc/controllers/routing#areas)。</span><span class="sxs-lookup"><span data-stu-id="be36c-287">For more information, see [Area routing](xref:mvc/controllers/routing#areas).</span></span>

### <a name="link-generation-with-mvc-areas"></a><span data-ttu-id="be36c-288">MVC 区域的链接生成</span><span class="sxs-lookup"><span data-stu-id="be36c-288">Link generation with MVC areas</span></span>

<span data-ttu-id="be36c-289">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)中的以下代码显示指定区域的链接生成：</span><span class="sxs-lookup"><span data-stu-id="be36c-289">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples) shows link generation with the area specified:</span></span>

[!code-cshtml[](areas/samples/MVCareas/Views/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="be36c-290">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="be36c-290">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="be36c-291">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="be36c-291">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="be36c-292">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-292">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="be36c-293">在未指定区域的情况下生成的链接仅在从同一区域和控制器中的页面引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="be36c-293">The links generated without specifying the area are only valid when referenced from a page in the same area and controller.</span></span>

<span data-ttu-id="be36c-294">如果未指定区域或控制器，路由取决于环境值  。</span><span class="sxs-lookup"><span data-stu-id="be36c-294">When the area or controller is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="be36c-295">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="be36c-295">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="be36c-296">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-296">In many cases for the sample app, using the ambient values generates incorrect links.</span></span>

<span data-ttu-id="be36c-297">有关详细信息，请参阅[路由到控制器操作](xref:mvc/controllers/routing)。</span><span class="sxs-lookup"><span data-stu-id="be36c-297">For more information, see [Routing to controller actions](xref:mvc/controllers/routing).</span></span>

### <a name="shared-layout-for-areas-using-the-_viewstartcshtml-file"></a><span data-ttu-id="be36c-298">使用 _ViewStart.cshtml 文件的共享区域布局</span><span class="sxs-lookup"><span data-stu-id="be36c-298">Shared layout for Areas using the _ViewStart.cshtml file</span></span>

<span data-ttu-id="be36c-299">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹  。</span><span class="sxs-lookup"><span data-stu-id="be36c-299">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="_viewimportscshtml"></a><span data-ttu-id="be36c-300">_ViewImports.cshtml</span><span class="sxs-lookup"><span data-stu-id="be36c-300">_ViewImports.cshtml</span></span>

<span data-ttu-id="be36c-301">在其标准位置，/Views/_ViewImports.cshtml 不适用于区域  。</span><span class="sxs-lookup"><span data-stu-id="be36c-301">In its standard location, */Views/_ViewImports.cshtml* doesn't apply to areas.</span></span> <span data-ttu-id="be36c-302">若要在你的区域中使用常见 [标记帮助](xref:mvc/views/tag-helpers/intro)程序、或，请 `@using` `@inject` 确保适当的 *_ViewImports cshtml* 文件 [适用于你的区域视图](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="be36c-302">To use common [Tag Helpers](xref:mvc/views/tag-helpers/intro), `@using`, or `@inject` in your area, ensure a proper *_ViewImports.cshtml* file [applies to your area views](xref:mvc/views/layout#importing-shared-directives).</span></span> <span data-ttu-id="be36c-303">如果希望所有视图都具有相同的行为，请将 /Views/_ViewImports.cshtml 迁移到应用程序根  。</span><span class="sxs-lookup"><span data-stu-id="be36c-303">If you want the same behavior in all your views, move */Views/_ViewImports.cshtml* to the application root.</span></span>

<a name="rename"></a>

### <a name="change-default-area-folder-where-views-are-stored"></a><span data-ttu-id="be36c-304">更改存储视图的默认区域文件夹</span><span class="sxs-lookup"><span data-stu-id="be36c-304">Change default area folder where views are stored</span></span>

<span data-ttu-id="be36c-305">以下代码将默认的区域文件夹从 `"Areas"` 改为`"MyAreas"`：</span><span class="sxs-lookup"><span data-stu-id="be36c-305">The following code changes the default area folder from `"Areas"` to `"MyAreas"`:</span></span>

[!code-csharp[](areas/samples/MVCareas/Startup2.cs?name=snippet)]

<a name="arp"></a>

## <a name="areas-with-no-locrazor-pages"></a><span data-ttu-id="be36c-306">包含页面的区域 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="be36c-306">Areas with :::no-loc(Razor)::: Pages</span></span>

<span data-ttu-id="be36c-307">包含页面的区域 :::no-loc(Razor)::: `Areas/<area name>/Pages` 在应用的根目录中需要一个文件夹。</span><span class="sxs-lookup"><span data-stu-id="be36c-307">Areas with :::no-loc(Razor)::: Pages require an `Areas/<area name>/Pages` folder in the root of the app.</span></span> <span data-ttu-id="be36c-308">以下文件夹结构用于[示例应用](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples)：</span><span class="sxs-lookup"><span data-stu-id="be36c-308">The following folder structure is used with the [sample app](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples):</span></span>

* <span data-ttu-id="be36c-309">项目名称</span><span class="sxs-lookup"><span data-stu-id="be36c-309">Project name</span></span>
  * <span data-ttu-id="be36c-310">Areas</span><span class="sxs-lookup"><span data-stu-id="be36c-310">Areas</span></span>
    * <span data-ttu-id="be36c-311">产品</span><span class="sxs-lookup"><span data-stu-id="be36c-311">Products</span></span>
      * <span data-ttu-id="be36c-312">页数</span><span class="sxs-lookup"><span data-stu-id="be36c-312">Pages</span></span>
        * <span data-ttu-id="be36c-313">_ViewImports</span><span class="sxs-lookup"><span data-stu-id="be36c-313">_ViewImports</span></span>
        * <span data-ttu-id="be36c-314">关于</span><span class="sxs-lookup"><span data-stu-id="be36c-314">About</span></span>
        * <span data-ttu-id="be36c-315">索引</span><span class="sxs-lookup"><span data-stu-id="be36c-315">Index</span></span>
    * <span data-ttu-id="be36c-316">服务</span><span class="sxs-lookup"><span data-stu-id="be36c-316">Services</span></span>
      * <span data-ttu-id="be36c-317">页数</span><span class="sxs-lookup"><span data-stu-id="be36c-317">Pages</span></span>
        * <span data-ttu-id="be36c-318">管理</span><span class="sxs-lookup"><span data-stu-id="be36c-318">Manage</span></span>
          * <span data-ttu-id="be36c-319">关于</span><span class="sxs-lookup"><span data-stu-id="be36c-319">About</span></span>
          * <span data-ttu-id="be36c-320">索引</span><span class="sxs-lookup"><span data-stu-id="be36c-320">Index</span></span>

### <a name="link-generation-with-no-locrazor-pages-and-areas"></a><span data-ttu-id="be36c-321">与 :::no-loc(Razor)::: 页面和区域一起生成链接</span><span class="sxs-lookup"><span data-stu-id="be36c-321">Link generation with :::no-loc(Razor)::: Pages and areas</span></span>

<span data-ttu-id="be36c-322">[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)中的以下代码显示指定区域（例如 `asp-area="Products"`）的链接生成：</span><span class="sxs-lookup"><span data-stu-id="be36c-322">The following code from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas) shows link generation with the area specified (for example, `asp-area="Products"`):</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet)]

<span data-ttu-id="be36c-323">使用上述代码生成的链接在应用的任何位置都有效。</span><span class="sxs-lookup"><span data-stu-id="be36c-323">The links generated with the preceding code are valid anywhere in the app.</span></span>

<span data-ttu-id="be36c-324">示例下载包含[部分视图](xref:mvc/views/partial)，该视图包含以前的链接和相同的链接（未指定区域）。</span><span class="sxs-lookup"><span data-stu-id="be36c-324">The sample download includes a [partial view](xref:mvc/views/partial) that contains the preceding links and the same links without specifying the area.</span></span> <span data-ttu-id="be36c-325">在[布局文件](xref:mvc/views/layout)中引用部分视图，因此应用中的每个页面都显示生成的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-325">The partial view is referenced in the [layout file](xref:mvc/views/layout), so every page in the app displays the generated links.</span></span> <span data-ttu-id="be36c-326">在未指定区域的情况下生成的链接仅在从同一区域中的页引用时才有效。</span><span class="sxs-lookup"><span data-stu-id="be36c-326">The links generated without specifying the area are only valid when referenced from a page in the same area.</span></span>

<span data-ttu-id="be36c-327">如果未指定区域，路由取决于环境  值。</span><span class="sxs-lookup"><span data-stu-id="be36c-327">When the area is not specified, routing depends on the *ambient* values.</span></span> <span data-ttu-id="be36c-328">当前请求的当前路由值被视为链接生成的环境值。</span><span class="sxs-lookup"><span data-stu-id="be36c-328">The current route values of the current request are considered ambient values for link generation.</span></span> <span data-ttu-id="be36c-329">在许多情况下，对于示例应用，使用环境值会生成错误的链接。</span><span class="sxs-lookup"><span data-stu-id="be36c-329">In many cases for the sample app, using the ambient values generates incorrect links.</span></span> <span data-ttu-id="be36c-330">例如，考虑从下面的代码生成的链接：</span><span class="sxs-lookup"><span data-stu-id="be36c-330">For example, consider the links generated from the following code:</span></span>

[!code-cshtml[](areas/samples/RPareas/Pages/Shared/_testLinksPartial.cshtml?name=snippet2)]

<span data-ttu-id="be36c-331">对于上述代码：</span><span class="sxs-lookup"><span data-stu-id="be36c-331">For the preceding code:</span></span>

* <span data-ttu-id="be36c-332">只有当最后一个请求是针对 `Services` 区域的页时，从 `<a asp-page="/Manage/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="be36c-332">The link generated from `<a asp-page="/Manage/About">` is correct only when the last request was for a page in `Services` area.</span></span> <span data-ttu-id="be36c-333">例如，`/Services/Manage/`、`/Services/Manage/Index` 或 `/Services/Manage/About`。</span><span class="sxs-lookup"><span data-stu-id="be36c-333">For example, `/Services/Manage/`, `/Services/Manage/Index`, or `/Services/Manage/About`.</span></span>
* <span data-ttu-id="be36c-334">只有当最后一个请求是针对 `/Home` 中的页时，从 `<a asp-page="/About">` 生成的链接才是正确的。</span><span class="sxs-lookup"><span data-stu-id="be36c-334">The link generated from `<a asp-page="/About">` is correct only when the last request was for a page in `/Home`.</span></span>
* <span data-ttu-id="be36c-335">代码摘自[示例下载](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas)。</span><span class="sxs-lookup"><span data-stu-id="be36c-335">The code is from the [sample download](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/mvc/controllers/areas/samples/RPareas).</span></span>

### <a name="import-namespace-and-tag-helpers-with-_viewimports-file"></a><span data-ttu-id="be36c-336">使用 _ViewImports 文件导入命名空间和标记帮助程序</span><span class="sxs-lookup"><span data-stu-id="be36c-336">Import namespace and Tag Helpers with _ViewImports file</span></span>

<span data-ttu-id="be36c-337">可以将 *_ViewImports 的 cshtml* 文件添加到每个区域 *页面* 文件夹，以将命名空间和标记帮助程序导入到文件夹中的每一 :::no-loc(Razor)::: 页。</span><span class="sxs-lookup"><span data-stu-id="be36c-337">A *_ViewImports.cshtml* file can be added to each area *Pages* folder to import the namespace and Tag Helpers to each :::no-loc(Razor)::: Page in the folder.</span></span>

<span data-ttu-id="be36c-338">请考虑使用示例代码的“服务”区域，它不包含 _ViewImports.cshtml 文件  。</span><span class="sxs-lookup"><span data-stu-id="be36c-338">Consider the *Services* area of the sample code, which doesn't contain a *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="be36c-339">以下标记显示了 " */Services/Manage/About* " :::no-loc(Razor)::: 页：</span><span class="sxs-lookup"><span data-stu-id="be36c-339">The following markup shows the */Services/Manage/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Services/Pages/Manage/About.cshtml)]

<span data-ttu-id="be36c-340">在前面的标记中：</span><span class="sxs-lookup"><span data-stu-id="be36c-340">In the preceding markup:</span></span>

* <span data-ttu-id="be36c-341">必须使用完全限定的域名来指定模型 (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`)。</span><span class="sxs-lookup"><span data-stu-id="be36c-341">The fully qualified domain name must be used to specify the model (`@model RPareas.Areas.Services.Pages.Manage.AboutModel`).</span></span>
* <span data-ttu-id="be36c-342">[标记帮助](xref:mvc/views/tag-helpers/intro) 程序由启用 `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span><span class="sxs-lookup"><span data-stu-id="be36c-342">[Tag Helpers](xref:mvc/views/tag-helpers/intro) are enabled by `@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers`</span></span>

<span data-ttu-id="be36c-343">在示例下载中，“产品”区域包含下列 _ViewImports.cshtml 文件  ：</span><span class="sxs-lookup"><span data-stu-id="be36c-343">In the sample download, the Products area contains the following *_ViewImports.cshtml* file:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/_ViewImports.cshtml)]

<span data-ttu-id="be36c-344">以下标记显示了 " */Products/About* " :::no-loc(Razor)::: 页：</span><span class="sxs-lookup"><span data-stu-id="be36c-344">The following markup shows the */Products/About* :::no-loc(Razor)::: Page:</span></span>

[!code-cshtml[](areas/samples/RPareas/Areas/Products/Pages/About.cshtml)]

<span data-ttu-id="be36c-345">在前面的文件中，将 `@addTagHelper` 按 *区域/产品/页面/_ViewImports cshtml* 文件将命名空间和指令导入文件。</span><span class="sxs-lookup"><span data-stu-id="be36c-345">In the preceding file, the namespace and `@addTagHelper` directive is imported to the file by the *Areas/Products/Pages/_ViewImports.cshtml* file.</span></span>

<span data-ttu-id="be36c-346">有关详细信息，请参阅[管理标记帮助程序范围](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope)和[导入共享指令](xref:mvc/views/layout#importing-shared-directives)。</span><span class="sxs-lookup"><span data-stu-id="be36c-346">For more information, see [Managing Tag Helper scope](xref:mvc/views/tag-helpers/intro?view=aspnetcore-2.2#managing-tag-helper-scope) and [Importing Shared Directives](xref:mvc/views/layout#importing-shared-directives).</span></span>

### <a name="shared-layout-for-no-locrazor-pages-areas"></a><span data-ttu-id="be36c-347">页面区域的共享布局 :::no-loc(Razor):::</span><span class="sxs-lookup"><span data-stu-id="be36c-347">Shared layout for :::no-loc(Razor)::: Pages Areas</span></span>

<span data-ttu-id="be36c-348">要共享整个应用的常用布局，请将 _ViewStart.cshtml 移动到应用程序根文件夹  。</span><span class="sxs-lookup"><span data-stu-id="be36c-348">To share a common layout for the entire app, move the *_ViewStart.cshtml* to the application root folder.</span></span>

### <a name="publishing-areas"></a><span data-ttu-id="be36c-349">发布区域</span><span class="sxs-lookup"><span data-stu-id="be36c-349">Publishing Areas</span></span>

<span data-ttu-id="be36c-350">当 \*.csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 \*.cshtml 文件以及 wwwroot 目录中的文件都将发布到输出中  。</span><span class="sxs-lookup"><span data-stu-id="be36c-350">All \*.cshtml files and files within the *wwwroot* directory are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the \*.csproj file.</span></span>
::: moniker-end
