---
title: 从 ASP.NET MVC 迁移到 ASP.NET Core MVC
author: ardalis
description: 了解如何开始将 ASP.NET MVC 项目迁移到 ASP.NET Core MVC。
ms.author: riande
ms.date: 04/06/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: migration/mvc
ms.openlocfilehash: 59a10c002958e5f719dbd59686f21df69da5f43e
ms.sourcegitcommit: 70e5f982c218db82aa54aa8b8d96b377cfc7283f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/04/2020
ms.locfileid: "82777041"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="fb170-103">从 ASP.NET MVC 迁移到 ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="fb170-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="fb170-104">作者： [Rick Anderson](https://twitter.com/RickAndMSFT)、 [Daniel Roth](https://github.com/danroth27)、 [Steve Smith](https://ardalis.com/)和[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="fb170-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="fb170-105">本文介绍如何开始将 ASP.NET MVC 项目迁移到[ASP.NET CORE mvc](../mvc/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="fb170-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="fb170-106">在此过程中，它突出显示了从 ASP.NET MVC 中更改的许多内容。</span><span class="sxs-lookup"><span data-stu-id="fb170-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="fb170-107">从 ASP.NET MVC 迁移是一个多步骤过程，本文介绍了初始设置、基本控制器和视图、静态内容和客户端依赖关系。</span><span class="sxs-lookup"><span data-stu-id="fb170-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="fb170-108">其他文章介绍了如何迁移在许多 ASP.NET MVC 项目中找到的配置和标识代码。</span><span class="sxs-lookup"><span data-stu-id="fb170-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="fb170-109">示例中的版本号可能不是最新的。</span><span class="sxs-lookup"><span data-stu-id="fb170-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="fb170-110">可能需要相应地更新项目。</span><span class="sxs-lookup"><span data-stu-id="fb170-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="fb170-111">创建 starter ASP.NET MVC 项目</span><span class="sxs-lookup"><span data-stu-id="fb170-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="fb170-112">为了演示升级，我们首先创建一个 ASP.NET MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="fb170-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="fb170-113">创建名为*WebApp1*的命名空间，使命名空间与我们在下一步中创建的 ASP.NET Core 项目匹配。</span><span class="sxs-lookup"><span data-stu-id="fb170-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio“新建项目”对话框](mvc/_static/new-project.png)

!["新建 Web 应用程序" 对话框：在 "ASP.NET 模板" 面板中选择的 MVC 项目模板](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="fb170-116">*可选：* 将解决方案的名称从*WebApp1*更改为*Mvc5*。</span><span class="sxs-lookup"><span data-stu-id="fb170-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="fb170-117">Visual Studio 将显示新的解决方案名称（*Mvc5*），这样就可以更轻松地从下一个项目通知此项目。</span><span class="sxs-lookup"><span data-stu-id="fb170-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="fb170-118">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="fb170-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="fb170-119">使用与上一个项目相同的名称创建新的*空*ASP.NET Core web 应用（*WebApp1*），以便两个项目中的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="fb170-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="fb170-120">通过具有相同的命名空间，可以更轻松地在两个项目之间复制代码。</span><span class="sxs-lookup"><span data-stu-id="fb170-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="fb170-121">必须在与上一个项目相同的目录中创建此项目。</span><span class="sxs-lookup"><span data-stu-id="fb170-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![“新建项目”对话框](mvc/_static/new_core.png)

![New ASP.NET Web 应用程序对话框：在 ASP.NET Core 模板 "面板中选择的空项目模板](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="fb170-124">*可选：* 使用 " *Web 应用程序*" 项目模板创建新的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="fb170-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="fb170-125">将项目命名为 " *WebApp1*"，并选择**单个用户帐户**的身份验证选项。</span><span class="sxs-lookup"><span data-stu-id="fb170-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="fb170-126">将此应用重命名为*FullAspNetCore*。</span><span class="sxs-lookup"><span data-stu-id="fb170-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="fb170-127">创建此项目可在转换时节省时间。</span><span class="sxs-lookup"><span data-stu-id="fb170-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="fb170-128">您可以查看模板生成的代码以查看最终结果或将代码复制到转换项目。</span><span class="sxs-lookup"><span data-stu-id="fb170-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="fb170-129">当您停滞转换步骤以与模板生成的项目进行比较时，这也很有用。</span><span class="sxs-lookup"><span data-stu-id="fb170-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="fb170-130">将站点配置为使用 MVC</span><span class="sxs-lookup"><span data-stu-id="fb170-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="fb170-131">面向 .NET Core 时，默认情况下将引用[AspNetCore 元包](xref:fundamentals/metapackage-app)。</span><span class="sxs-lookup"><span data-stu-id="fb170-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="fb170-132">此包包含 MVC 应用通常使用的包。</span><span class="sxs-lookup"><span data-stu-id="fb170-132">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="fb170-133">如果目标 .NET Framework，则包引用必须单独列出在项目文件中。</span><span class="sxs-lookup"><span data-stu-id="fb170-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="fb170-134">面向 .NET Core 时，默认情况下将引用[AspNetCore。](xref:fundamentals/metapackage)</span><span class="sxs-lookup"><span data-stu-id="fb170-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="fb170-135">此包包含由 MVC 应用程序使用的包。</span><span class="sxs-lookup"><span data-stu-id="fb170-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="fb170-136">如果目标 .NET Framework，则包引用必须单独列出在项目文件中。</span><span class="sxs-lookup"><span data-stu-id="fb170-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="fb170-137">面向 .NET Core 或 .NET Framework 时，将在项目文件中单独列出包由 MVC 应用程序使用的常用包。</span><span class="sxs-lookup"><span data-stu-id="fb170-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="fb170-138">`Microsoft.AspNetCore.Mvc`是 ASP.NET Core MVC 框架。</span><span class="sxs-lookup"><span data-stu-id="fb170-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="fb170-139">`Microsoft.AspNetCore.StaticFiles`为静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="fb170-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="fb170-140">ASP.NET Core 运行时是模块化的，你必须明确选择提供静态文件（请参阅[静态文件](xref:fundamentals/static-files)）。</span><span class="sxs-lookup"><span data-stu-id="fb170-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="fb170-141">打开*Startup.cs*文件并更改代码，使其与以下内容匹配：</span><span class="sxs-lookup"><span data-stu-id="fb170-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="fb170-142">`UseStaticFiles`扩展方法添加静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="fb170-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="fb170-143">如前所述，ASP.NET 运行时是模块化的，你必须明确选择提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="fb170-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="fb170-144">`UseMvc`扩展方法将添加路由。</span><span class="sxs-lookup"><span data-stu-id="fb170-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="fb170-145">有关详细信息，请参阅[应用程序启动](xref:fundamentals/startup)和[路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="fb170-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="fb170-146">添加控制器和视图</span><span class="sxs-lookup"><span data-stu-id="fb170-146">Add a controller and view</span></span>

<span data-ttu-id="fb170-147">在本部分中，你将添加一个最小控制器和视图，用作 ASP.NET MVC 控制器的占位符，以及将在下一部分中迁移的视图。</span><span class="sxs-lookup"><span data-stu-id="fb170-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="fb170-148">添加 "*控制器*" 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="fb170-149">将名为*HomeController.cs*的**控制器类**添加到 "*控制器*" 文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![“添加新项”对话框](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="fb170-151">添加*Views*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="fb170-152">添加 "*视图"/"主*文件夹"。</span><span class="sxs-lookup"><span data-stu-id="fb170-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="fb170-153">将名为*Index*的\*\* Razor视图\**添加到*Views/Home\*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb170-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![“添加新项”对话框](mvc/_static/view.png)

<span data-ttu-id="fb170-155">项目结构如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb170-155">The project structure is shown below:</span></span>

![显示 WebApp1 的文件和文件夹的解决方案资源管理器](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="fb170-157">将*Views/Home/Index. cshtml*文件的内容替换为以下内容：</span><span class="sxs-lookup"><span data-stu-id="fb170-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="fb170-158">运行应用。</span><span class="sxs-lookup"><span data-stu-id="fb170-158">Run the app.</span></span>

![在 Microsoft Edge 中打开的 Web 应用](mvc/_static/hello-world.png)

<span data-ttu-id="fb170-160">有关详细信息，请参阅[控制器](xref:mvc/controllers/actions)和[视图](xref:mvc/views/overview)。</span><span class="sxs-lookup"><span data-stu-id="fb170-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="fb170-161">现在，我们有了最小的工作 ASP.NET Core 项目，接下来可以开始从 ASP.NET MVC 项目迁移功能。</span><span class="sxs-lookup"><span data-stu-id="fb170-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="fb170-162">我们需要移动以下内容：</span><span class="sxs-lookup"><span data-stu-id="fb170-162">We need to move the following:</span></span>

* <span data-ttu-id="fb170-163">客户端内容（CSS、字体和脚本）</span><span class="sxs-lookup"><span data-stu-id="fb170-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="fb170-164">控制器</span><span class="sxs-lookup"><span data-stu-id="fb170-164">controllers</span></span>

* <span data-ttu-id="fb170-165">视图</span><span class="sxs-lookup"><span data-stu-id="fb170-165">views</span></span>

* <span data-ttu-id="fb170-166">模型</span><span class="sxs-lookup"><span data-stu-id="fb170-166">models</span></span>

* <span data-ttu-id="fb170-167">销售</span><span class="sxs-lookup"><span data-stu-id="fb170-167">bundling</span></span>

* <span data-ttu-id="fb170-168">筛选器</span><span class="sxs-lookup"><span data-stu-id="fb170-168">filters</span></span>

* <span data-ttu-id="fb170-169">登录/注销， Identity （在下一教程中完成此操作。）</span><span class="sxs-lookup"><span data-stu-id="fb170-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="fb170-170">控制器和视图</span><span class="sxs-lookup"><span data-stu-id="fb170-170">Controllers and views</span></span>

* <span data-ttu-id="fb170-171">将 ASP.NET MVC `HomeController`中的每个方法复制到新`HomeController`的。</span><span class="sxs-lookup"><span data-stu-id="fb170-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="fb170-172">请注意，在 ASP.NET MVC 中，内置模板的控制器操作方法返回类型为[ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx);在 ASP.NET Core MVC 中，操作方法将`IActionResult`改为返回。</span><span class="sxs-lookup"><span data-stu-id="fb170-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="fb170-173">`ActionResult`实现`IActionResult`，因此无需更改操作方法的返回类型。</span><span class="sxs-lookup"><span data-stu-id="fb170-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="fb170-174">将 ASP.NET MVC 项目中的*About*、 *Contact*和*Index.cshtml* Razor视图文件复制到 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="fb170-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="fb170-175">运行 ASP.NET Core 应用并测试每个方法。</span><span class="sxs-lookup"><span data-stu-id="fb170-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="fb170-176">尚未迁移布局文件或样式，因此呈现的视图仅包含视图文件中的内容。</span><span class="sxs-lookup"><span data-stu-id="fb170-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="fb170-177">你没有`About`和`Contact`视图的布局文件生成链接，因此你必须从浏览器调用它们（将**4492**替换为项目中使用的端口号）。</span><span class="sxs-lookup"><span data-stu-id="fb170-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![联系人页](mvc/_static/contact-page.png)

<span data-ttu-id="fb170-179">请注意缺少样式和菜单项。</span><span class="sxs-lookup"><span data-stu-id="fb170-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="fb170-180">此问题将在下一部分得以解决。</span><span class="sxs-lookup"><span data-stu-id="fb170-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="fb170-181">静态内容</span><span class="sxs-lookup"><span data-stu-id="fb170-181">Static content</span></span>

<span data-ttu-id="fb170-182">在以前版本的 ASP.NET MVC 中，静态内容是从 web 项目的根托管的，与服务器端文件混合。</span><span class="sxs-lookup"><span data-stu-id="fb170-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="fb170-183">在 ASP.NET Core 中，静态内容承载于*wwwroot*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb170-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="fb170-184">需要将静态内容从旧的 ASP.NET MVC 应用复制到 ASP.NET Core 项目中的*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="fb170-185">在此示例转换中：</span><span class="sxs-lookup"><span data-stu-id="fb170-185">In this sample conversion:</span></span>

* <span data-ttu-id="fb170-186">将旧 MVC 项目中的*favicon*文件复制到 ASP.NET Core 项目中的*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="fb170-187">旧的 ASP.NET MVC 项目使用[启动](https://getbootstrap.com/)来设置其样式，并将启动文件存储在 "*内容*" 和 "*脚本*" 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb170-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="fb170-188">生成旧的 ASP.NET MVC 项目的模板引用布局文件（*Views/Shared/_Layout*）中的启动。</span><span class="sxs-lookup"><span data-stu-id="fb170-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="fb170-189">可以将 ASP.NET MVC 项目中的*node.js*和*启动 .css*文件复制到新项目中的*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="fb170-190">相反，我们将在下一节中使用 Cdn 添加对启动（和其他客户端库）的支持。</span><span class="sxs-lookup"><span data-stu-id="fb170-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="fb170-191">迁移布局文件</span><span class="sxs-lookup"><span data-stu-id="fb170-191">Migrate the layout file</span></span>

* <span data-ttu-id="fb170-192">将 *_ViewStart*的 ASP.NET 文件从旧的 MVC 项目的*views*文件夹复制到 ASP.NET Core 项目的*views*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb170-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="fb170-193">未在 ASP.NET Core MVC 中更改 *_ViewStart cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="fb170-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="fb170-194">创建*视图/共享*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="fb170-195">*可选：* 将*FullAspNetCore* MVC 项目的*views*文件夹中 *_ViewImports*为 ASP.NET Core 项目的*views*文件夹。</span><span class="sxs-lookup"><span data-stu-id="fb170-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="fb170-196">删除 *_ViewImports cshtml*文件中的任何命名空间声明。</span><span class="sxs-lookup"><span data-stu-id="fb170-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="fb170-197">*_ViewImports 的 cshtml*文件提供所有视图文件的命名空间，并提供[标记帮助](xref:mvc/views/tag-helpers/intro)程序。</span><span class="sxs-lookup"><span data-stu-id="fb170-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="fb170-198">标记帮助程序在新的布局文件中使用。</span><span class="sxs-lookup"><span data-stu-id="fb170-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="fb170-199">*_ViewImports* ASP.NET Core 的新文件。</span><span class="sxs-lookup"><span data-stu-id="fb170-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="fb170-200">将 *_Layout*的 ASP.NET 文件从旧的 MVC 项目的*视图/共享*文件夹复制到 ASP.NET Core 项目的*视图/共享*文件夹中。</span><span class="sxs-lookup"><span data-stu-id="fb170-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="fb170-201">打开 *_Layout 的 cshtml*文件并进行以下更改（完成的代码如下所示）：</span><span class="sxs-lookup"><span data-stu-id="fb170-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="fb170-202">替换`@Styles.Render("~/Content/css")`为加载`<link>` *启动 .css*的元素（见下文）。</span><span class="sxs-lookup"><span data-stu-id="fb170-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="fb170-203">删除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="fb170-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="fb170-204">注释掉`@Html.Partial("_LoginPartial")`行（将该行环绕`@*...*@`）。</span><span class="sxs-lookup"><span data-stu-id="fb170-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="fb170-205">有关详细信息，请参阅[迁移Identity身份验证和 ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="fb170-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="fb170-206">替换`@Scripts.Render("~/bundles/jquery")`为`<script>`元素（见下文）。</span><span class="sxs-lookup"><span data-stu-id="fb170-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="fb170-207">替换`@Scripts.Render("~/bundles/bootstrap")`为`<script>`元素（见下文）。</span><span class="sxs-lookup"><span data-stu-id="fb170-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="fb170-208">用于启动 CSS 的替换标记包含：</span><span class="sxs-lookup"><span data-stu-id="fb170-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="fb170-209">JQuery 和启动 JavaScript 的替换标记包含：</span><span class="sxs-lookup"><span data-stu-id="fb170-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="fb170-210">更新后的 *_Layout cshtml*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="fb170-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="fb170-211">在浏览器中查看站点。</span><span class="sxs-lookup"><span data-stu-id="fb170-211">View the site in the browser.</span></span> <span data-ttu-id="fb170-212">它现在应正确加载，并具有所需的样式。</span><span class="sxs-lookup"><span data-stu-id="fb170-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="fb170-213">*可选：* 你可能想要尝试使用新的布局文件。</span><span class="sxs-lookup"><span data-stu-id="fb170-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="fb170-214">对于此项目，您可以从*FullAspNetCore*项目复制布局文件。</span><span class="sxs-lookup"><span data-stu-id="fb170-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="fb170-215">新的布局文件使用[标记帮助](xref:mvc/views/tag-helpers/intro)程序，并具有其他改进功能。</span><span class="sxs-lookup"><span data-stu-id="fb170-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="fb170-216">配置捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="fb170-216">Configure bundling and minification</span></span>

<span data-ttu-id="fb170-217">有关如何配置绑定和缩减的信息，请参阅[捆绑和缩减](../client-side/bundling-and-minification.md)。</span><span class="sxs-lookup"><span data-stu-id="fb170-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="fb170-218">解决 HTTP 500 错误</span><span class="sxs-lookup"><span data-stu-id="fb170-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="fb170-219">有许多问题可能会导致 HTTP 500 错误消息，其中不包含问题根源的相关信息。</span><span class="sxs-lookup"><span data-stu-id="fb170-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="fb170-220">例如，如果*Views/_ViewImports cshtml*文件包含项目中不存在的命名空间，则会收到 HTTP 500 错误。</span><span class="sxs-lookup"><span data-stu-id="fb170-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="fb170-221">默认情况下，在 ASP.NET Core 应用`UseDeveloperExceptionPage`中，会将扩展`IApplicationBuilder`添加到，并在配置为*开发*时执行。</span><span class="sxs-lookup"><span data-stu-id="fb170-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="fb170-222">下面的代码对此进行了详细说明：</span><span class="sxs-lookup"><span data-stu-id="fb170-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="fb170-223">ASP.NET Core 将 web 应用中的未处理异常转换为 HTTP 500 错误响应。</span><span class="sxs-lookup"><span data-stu-id="fb170-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="fb170-224">通常，这些响应中不包含错误详细信息，以防止泄露有关服务器的可能敏感信息。</span><span class="sxs-lookup"><span data-stu-id="fb170-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="fb170-225">有关详细信息，请参阅 "[处理错误](../fundamentals/error-handling.md) **" 中的使用开发者异常页**。</span><span class="sxs-lookup"><span data-stu-id="fb170-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="fb170-226">其他资源</span><span class="sxs-lookup"><span data-stu-id="fb170-226">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>
