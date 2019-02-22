---
title: ASP.NET Core 中的区域
author: rick-anderson
description: 了解 ASP.NET MVC 的区域功能如何将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。
ms.author: riande
ms.date: 02/14/2017
uid: mvc/controllers/areas
ms.openlocfilehash: 19e818fa198936ea1bee0da8039e88a3c0abbf6b
ms.sourcegitcommit: d75d8eb26c2cce19876c8d5b65ac8a4b21f625ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/19/2019
ms.locfileid: "56410607"
---
# <a name="areas-in-aspnet-core"></a><span data-ttu-id="d8d14-103">ASP.NET Core 中的区域</span><span class="sxs-lookup"><span data-stu-id="d8d14-103">Areas in ASP.NET Core</span></span>

<span data-ttu-id="d8d14-104">作者：[Dhananjay Kumar](https://twitter.com/debug_mode) 和 [Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d8d14-104">By [Dhananjay Kumar](https://twitter.com/debug_mode)  and [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="d8d14-105">区域是 ASP.NET MVC 功能，用于将相关功能以单独的名称空间（用于路由）和文件夹结构（用于视图）的形式组织到一个组中。</span><span class="sxs-lookup"><span data-stu-id="d8d14-105">Areas are an ASP.NET MVC feature used to organize related functionality into a group as a separate namespace (for routing) and folder structure (for views).</span></span> <span data-ttu-id="d8d14-106">使用区域，将通过为 `controller` 和 `action` 添加另一个路由参数 `area`，创建用于路由目的的层次结构。</span><span class="sxs-lookup"><span data-stu-id="d8d14-106">Using areas creates a hierarchy for the purpose of routing by adding another route parameter, `area`, to `controller` and `action`.</span></span>

<span data-ttu-id="d8d14-107">区域提供了将大型 ASP.NET Core MVC Web 应用分区为较小功能分组的方法。</span><span class="sxs-lookup"><span data-stu-id="d8d14-107">Areas provide a way to partition a large ASP.NET Core MVC Web app into smaller functional groupings.</span></span> <span data-ttu-id="d8d14-108">区域实际上是应用程序内的一个 MVC 结构。</span><span class="sxs-lookup"><span data-stu-id="d8d14-108">An area is effectively an MVC structure inside an application.</span></span> <span data-ttu-id="d8d14-109">在 MVC 项目中，模型、控制器和视图等逻辑组件保存在不同的文件夹中，MVC 使用命名约定来创建这些组件之间的关系。</span><span class="sxs-lookup"><span data-stu-id="d8d14-109">In an MVC project, logical components like Model, Controller, and View are kept in different folders, and MVC uses naming conventions to create the relationship between these components.</span></span> <span data-ttu-id="d8d14-110">对于大型应用，将应用分区为独立的高级功能区域可能更有利。</span><span class="sxs-lookup"><span data-stu-id="d8d14-110">For a large app, it may be advantageous to partition the  app into separate high level areas of functionality.</span></span> <span data-ttu-id="d8d14-111">例如，具有多个业务单位（如结账、计费、搜索等）的电子商务应用。每个单位都有自己的逻辑组件视图、控制器和模型。</span><span class="sxs-lookup"><span data-stu-id="d8d14-111">For instance, an e-commerce app with multiple business units, such as checkout, billing, and search etc. Each of these units have their own logical component views, controllers, and models.</span></span> <span data-ttu-id="d8d14-112">在此方案中，可使用区域对同一项目中的业务组件进行物理分区。</span><span class="sxs-lookup"><span data-stu-id="d8d14-112">In this scenario, you can use Areas to physically partition the business components in the same project.</span></span>

<span data-ttu-id="d8d14-113">区域可以说是 ASP.NET Core MVC 项目中的较小功能单位，它具有自己的一组控制器、视图和模型。</span><span class="sxs-lookup"><span data-stu-id="d8d14-113">An area can be defined as smaller functional units in an ASP.NET Core MVC project with its own set of controllers, views, and models.</span></span>

<span data-ttu-id="d8d14-114">以下情况，请考虑在 MVC 项目中使用区域：</span><span class="sxs-lookup"><span data-stu-id="d8d14-114">Consider using Areas in an MVC project when:</span></span>

* <span data-ttu-id="d8d14-115">应用程序由应进行逻辑区分的多个高级功能组件组成</span><span class="sxs-lookup"><span data-stu-id="d8d14-115">Your application is made of multiple high-level functional components that should be logically separated</span></span>

* <span data-ttu-id="d8d14-116">想对 MVC 项目进行分区，使每个功能区域可以独立工作</span><span class="sxs-lookup"><span data-stu-id="d8d14-116">You want to partition your MVC project so that each functional area can be worked on independently</span></span>

<span data-ttu-id="d8d14-117">区域特性：</span><span class="sxs-lookup"><span data-stu-id="d8d14-117">Area features:</span></span>

* <span data-ttu-id="d8d14-118">一个 ASP.NET Core MVC 应用可以有任意数量的区域。</span><span class="sxs-lookup"><span data-stu-id="d8d14-118">An ASP.NET Core MVC app can have any number of areas.</span></span>

* <span data-ttu-id="d8d14-119">每个区域都有自己的控制器、模型和视图。</span><span class="sxs-lookup"><span data-stu-id="d8d14-119">Each area has its own controllers, models, and views.</span></span>

* <span data-ttu-id="d8d14-120">区域可用于将大型 MVC 项目组织为可以独立工作的多个高级组件。</span><span class="sxs-lookup"><span data-stu-id="d8d14-120">Areas allow you to organize large MVC projects into multiple high-level components that can be worked on independently.</span></span>

* <span data-ttu-id="d8d14-121">区域支持具有相同名称的多个控制器 - 只要它们具有不同的区域。</span><span class="sxs-lookup"><span data-stu-id="d8d14-121">Areas support multiple controllers with the same name, as long as they have different *areas*.</span></span>

<span data-ttu-id="d8d14-122">我们来看看演示如何创建和使用区域的示例。</span><span class="sxs-lookup"><span data-stu-id="d8d14-122">Let's take a look at an example to illustrate how Areas are created and used.</span></span> <span data-ttu-id="d8d14-123">假设你有一个商店应用，它有两组不同的控制器和视图：产品和服务。</span><span class="sxs-lookup"><span data-stu-id="d8d14-123">Let's say you have a store app that has two distinct groupings of controllers and views: Products and Services.</span></span> <span data-ttu-id="d8d14-124">使用 MVC 区域的典型文件夹结构如下所示：</span><span class="sxs-lookup"><span data-stu-id="d8d14-124">A typical folder structure for that using MVC areas looks like below:</span></span>

* <span data-ttu-id="d8d14-125">项目名称</span><span class="sxs-lookup"><span data-stu-id="d8d14-125">Project name</span></span>
  * <span data-ttu-id="d8d14-126">区域</span><span class="sxs-lookup"><span data-stu-id="d8d14-126">Areas</span></span>
    * <span data-ttu-id="d8d14-127">产品</span><span class="sxs-lookup"><span data-stu-id="d8d14-127">Products</span></span>
      * <span data-ttu-id="d8d14-128">Controllers</span><span class="sxs-lookup"><span data-stu-id="d8d14-128">Controllers</span></span>
        * <span data-ttu-id="d8d14-129">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d8d14-129">HomeController.cs</span></span>
        * <span data-ttu-id="d8d14-130">ManageController.cs</span><span class="sxs-lookup"><span data-stu-id="d8d14-130">ManageController.cs</span></span>
      * <span data-ttu-id="d8d14-131">视图</span><span class="sxs-lookup"><span data-stu-id="d8d14-131">Views</span></span>
        * <span data-ttu-id="d8d14-132">主页</span><span class="sxs-lookup"><span data-stu-id="d8d14-132">Home</span></span>
          * <span data-ttu-id="d8d14-133">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d8d14-133">Index.cshtml</span></span>
        * <span data-ttu-id="d8d14-134">管理</span><span class="sxs-lookup"><span data-stu-id="d8d14-134">Manage</span></span>
          * <span data-ttu-id="d8d14-135">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d8d14-135">Index.cshtml</span></span>
    * <span data-ttu-id="d8d14-136">服务</span><span class="sxs-lookup"><span data-stu-id="d8d14-136">Services</span></span>
      * <span data-ttu-id="d8d14-137">Controllers</span><span class="sxs-lookup"><span data-stu-id="d8d14-137">Controllers</span></span>
        * <span data-ttu-id="d8d14-138">HomeController.cs</span><span class="sxs-lookup"><span data-stu-id="d8d14-138">HomeController.cs</span></span>
      * <span data-ttu-id="d8d14-139">视图</span><span class="sxs-lookup"><span data-stu-id="d8d14-139">Views</span></span>
        * <span data-ttu-id="d8d14-140">主页</span><span class="sxs-lookup"><span data-stu-id="d8d14-140">Home</span></span>
          * <span data-ttu-id="d8d14-141">Index.cshtml</span><span class="sxs-lookup"><span data-stu-id="d8d14-141">Index.cshtml</span></span>

<span data-ttu-id="d8d14-142">当 MVC 尝试呈现 Area 中的视图时，它将默认尝试检查以下位置：</span><span class="sxs-lookup"><span data-stu-id="d8d14-142">When MVC tries to render a view in an Area, by default, it tries to look in the following locations:</span></span>

```text
/Areas/<Area-Name>/Views/<Controller-Name>/<Action-Name>.cshtml
   /Areas/<Area-Name>/Views/Shared/<Action-Name>.cshtml
   /Views/Shared/<Action-Name>.cshtml
   ```

<span data-ttu-id="d8d14-143">以上是默认位置，可以通过 `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions` 上的 `AreaViewLocationFormats` 进行更改。</span><span class="sxs-lookup"><span data-stu-id="d8d14-143">These are the default locations which can be changed via the `AreaViewLocationFormats` on the `Microsoft.AspNetCore.Mvc.Razor.RazorViewEngineOptions`.</span></span>

<span data-ttu-id="d8d14-144">例如，在下面的代码中，文件夹名称不是“Area”，而是改为了“Categories”。</span><span class="sxs-lookup"><span data-stu-id="d8d14-144">For example, in the below code instead of having the folder name as 'Areas', it has been changed to 'Categories'.</span></span>

```csharp
services.Configure<RazorViewEngineOptions>(options =>
   {
       options.AreaViewLocationFormats.Clear();
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/{1}/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Categories/{2}/Views/Shared/{0}.cshtml");
       options.AreaViewLocationFormats.Add("/Views/Shared/{0}.cshtml");
   });
   ```

<span data-ttu-id="d8d14-145">值得注意的一点是，这里唯独看重的是 Views 文件夹的结构，而 Controllers 和 Models 等其他文件夹则无关紧要。</span><span class="sxs-lookup"><span data-stu-id="d8d14-145">One thing to note is that the structure of the *Views* folder is the only one which is considered important here and the content of the rest of the folders like *Controllers* and *Models* does **not** matter.</span></span> <span data-ttu-id="d8d14-146">例如，完全可以不设 Controllers 和 Models 文件夹。</span><span class="sxs-lookup"><span data-stu-id="d8d14-146">For example, you need not have a *Controllers* and *Models* folder at all.</span></span> <span data-ttu-id="d8d14-147">这是可行的，因为 Controllers 和 Models 的内容只是请求相应视图时才会编译到 .dll 中的代码，而 Views 的内容则不是。</span><span class="sxs-lookup"><span data-stu-id="d8d14-147">This works because the content of *Controllers* and *Models* is just code which gets compiled into a .dll where as the content of the *Views* isn't until a request to that view has been made.</span></span>

<span data-ttu-id="d8d14-148">定义文件夹层次结构后，需告知 MVC 每个控制器与一个区域相关联。</span><span class="sxs-lookup"><span data-stu-id="d8d14-148">Once you've defined the folder hierarchy, you need to tell MVC that each controller is associated with an area.</span></span> <span data-ttu-id="d8d14-149">可使用 `[Area]` 属性修饰控制器名称，来完成此操作。</span><span class="sxs-lookup"><span data-stu-id="d8d14-149">You do that by decorating the controller name with the `[Area]` attribute.</span></span>

```csharp
...
   namespace MyStore.Areas.Products.Controllers
   {
       [Area("Products")]
       public class HomeController : Controller
       {
           // GET: /Products/Home/Index
           public IActionResult Index()
           {
               return View();
           }

           // GET: /Products/Home/Create
           public IActionResult Create()
           {
               return View();
           }
       }
   }
   ```

<span data-ttu-id="d8d14-150">设置适用于新创建区域的路由定义。</span><span class="sxs-lookup"><span data-stu-id="d8d14-150">Set up a route definition that works with your newly created areas.</span></span> <span data-ttu-id="d8d14-151">[路由到控制器操作](routing.md)文章详细介绍了如何创建路由定义，包括使用常规路由和属性路由。</span><span class="sxs-lookup"><span data-stu-id="d8d14-151">The [Route to controller actions](routing.md) article goes into detail about how to create route definitions, including using conventional routes versus attribute routes.</span></span> <span data-ttu-id="d8d14-152">在此示例中，我们将使用常规路由。</span><span class="sxs-lookup"><span data-stu-id="d8d14-152">In this example, we'll use a conventional route.</span></span> <span data-ttu-id="d8d14-153">为此，请打开 Startup.cs 文件，并通过添加以下名为 `areaRoute` 的路由定义修改文件。</span><span class="sxs-lookup"><span data-stu-id="d8d14-153">To do so, open the *Startup.cs* file and modify it by adding the `areaRoute` named route definition below.</span></span>

```csharp
...
   app.UseMvc(routes =>
   {
     routes.MapRoute(
         name: "areaRoute",
         template: "{area:exists}/{controller=Home}/{action=Index}/{id?}");

     routes.MapRoute(
         name: "default",
         template: "{controller=Home}/{action=Index}/{id?}");
   });
   ```

<span data-ttu-id="d8d14-154">浏览到 `http://<yourApp>/products`，将调用 `Products` 区域中 `HomeController` 的 `Index` 操作方法。</span><span class="sxs-lookup"><span data-stu-id="d8d14-154">Browsing to `http://<yourApp>/products`, the `Index` action method of the `HomeController` in the `Products` area will be invoked.</span></span>

## <a name="link-generation"></a><span data-ttu-id="d8d14-155">链接生成</span><span class="sxs-lookup"><span data-stu-id="d8d14-155">Link Generation</span></span>

* <span data-ttu-id="d8d14-156">生成从基于区域的控制器内的动作到同一控制器内另一个动作的链接。</span><span class="sxs-lookup"><span data-stu-id="d8d14-156">Generating links from an action within an area based controller to another action within the same controller.</span></span>

  <span data-ttu-id="d8d14-157">假设当前的请求路径类似于 `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="d8d14-157">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="d8d14-158">HtmlHelper 语法：`@Html.ActionLink("Go to Product's Home Page", "Index")`</span><span class="sxs-lookup"><span data-stu-id="d8d14-158">HtmlHelper syntax: `@Html.ActionLink("Go to Product's Home Page", "Index")`</span></span>

  <span data-ttu-id="d8d14-159">TagHelper 语法：`<a asp-action="Index">Go to Product's Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d8d14-159">TagHelper syntax: `<a asp-action="Index">Go to Product's Home Page</a>`</span></span>

  <span data-ttu-id="d8d14-160">请注意，我们不需要提供“area”和“controller”值，因为它们在当前请求的上下文中已经可用。</span><span class="sxs-lookup"><span data-stu-id="d8d14-160">Note that we need not supply the 'area' and 'controller' values here as they're already available in the context of the current request.</span></span> <span data-ttu-id="d8d14-161">此类值称为 `ambient` 值。</span><span class="sxs-lookup"><span data-stu-id="d8d14-161">These kind of values are called `ambient` values.</span></span>

* <span data-ttu-id="d8d14-162">生成从基于区域的控制器内的动作到不同控制器上另一个动作的链接</span><span class="sxs-lookup"><span data-stu-id="d8d14-162">Generating links from an action within an area based controller to another action on a different controller</span></span>

  <span data-ttu-id="d8d14-163">假设当前的请求路径类似于 `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="d8d14-163">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="d8d14-164">HtmlHelper 语法：`@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`</span><span class="sxs-lookup"><span data-stu-id="d8d14-164">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products Home Page", "Index", "Manage")`</span></span>

  <span data-ttu-id="d8d14-165">TagHelper 语法：`<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d8d14-165">TagHelper syntax: `<a asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span></span>

  <span data-ttu-id="d8d14-166">请注意，此处使用“area”的环境值，但上面显式指定了“controller”值。</span><span class="sxs-lookup"><span data-stu-id="d8d14-166">Note that here the ambient value of an 'area' is used but the 'controller' value is specified explicitly above.</span></span>

* <span data-ttu-id="d8d14-167">生成从基于区域的控制器内的动作到不同区域中不同控制器上另一个动作的链接。</span><span class="sxs-lookup"><span data-stu-id="d8d14-167">Generating links from an action within an area based controller to another action on a different controller and a different area.</span></span>

  <span data-ttu-id="d8d14-168">假设当前的请求路径类似于 `/Products/Home/Create`</span><span class="sxs-lookup"><span data-stu-id="d8d14-168">Let's say the current request's path is like `/Products/Home/Create`</span></span>

  <span data-ttu-id="d8d14-169">HtmlHelper 语法：`@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`</span><span class="sxs-lookup"><span data-stu-id="d8d14-169">HtmlHelper syntax: `@Html.ActionLink("Go to Services Home Page", "Index", "Home", new { area = "Services" })`</span></span>

  <span data-ttu-id="d8d14-170">TagHelper 语法：`<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d8d14-170">TagHelper syntax: `<a asp-area="Services" asp-controller="Home" asp-action="Index">Go to Services Home Page</a>`</span></span>

  <span data-ttu-id="d8d14-171">请注意，此处不使用环境值。</span><span class="sxs-lookup"><span data-stu-id="d8d14-171">Note that here no ambient values are used.</span></span>

* <span data-ttu-id="d8d14-172">生成从基于区域的控制器内的动作到不在区域中的不同控制器上另一个动作的链接。</span><span class="sxs-lookup"><span data-stu-id="d8d14-172">Generating links from an action within an area based controller to another action on a different controller and **not** in an area.</span></span>

  <span data-ttu-id="d8d14-173">HtmlHelper 语法：`@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`</span><span class="sxs-lookup"><span data-stu-id="d8d14-173">HtmlHelper syntax: `@Html.ActionLink("Go to Manage Products  Home Page", "Index", "Home", new { area = "" })`</span></span>

  <span data-ttu-id="d8d14-174">TagHelper 语法：`<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span><span class="sxs-lookup"><span data-stu-id="d8d14-174">TagHelper syntax: `<a asp-area="" asp-controller="Manage" asp-action="Index">Go to Manage Products Home Page</a>`</span></span>

  <span data-ttu-id="d8d14-175">由于我们要生成到基于非区域的控制器操作的链接，因此在此清空“area”的环境值。</span><span class="sxs-lookup"><span data-stu-id="d8d14-175">Since we want to generate links to a non-area based controller action, we empty the ambient value for 'area' here.</span></span>

## <a name="publishing-areas"></a><span data-ttu-id="d8d14-176">发布区域</span><span class="sxs-lookup"><span data-stu-id="d8d14-176">Publishing Areas</span></span>

<span data-ttu-id="d8d14-177">当 .csproj 文件中包含 `<Project Sdk="Microsoft.NET.Sdk.Web">` 时，所有 `*.cshtml` 和 `wwwroot/**` 文件将发布到输出。</span><span class="sxs-lookup"><span data-stu-id="d8d14-177">All `*.cshtml` and `wwwroot/**` files are published to output when `<Project Sdk="Microsoft.NET.Sdk.Web">` is included in the *.csproj* file.</span></span>
