---
title: 将从 ASP.NET MVC 迁移到 ASP.NET Core MVC
author: ardalis
description: 了解如何开始迁移到 ASP.NET Core MVC ASP.NET MVC 项目。
ms.author: riande
ms.date: 02/13/2019
uid: migration/mvc
ms.openlocfilehash: 2ca51a145243444722ad8081fd8cdbb65d72b53a
ms.sourcegitcommit: 6ba5fb1fd0b7f9a6a79085b0ef56206e462094b7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2019
ms.locfileid: "56248038"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="b883a-103">将从 ASP.NET MVC 迁移到 ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="b883a-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

<span data-ttu-id="b883a-104">通过[Rick Anderson](https://twitter.com/RickAndMSFT)， [Daniel Roth](https://github.com/danroth27)， [Steve Smith](https://ardalis.com/)，和[Scott Addie](https://scottaddie.com)</span><span class="sxs-lookup"><span data-stu-id="b883a-104">By [Rick Anderson](https://twitter.com/RickAndMSFT), [Daniel Roth](https://github.com/danroth27), [Steve Smith](https://ardalis.com/), and [Scott Addie](https://scottaddie.com)</span></span>

<span data-ttu-id="b883a-105">这篇文章演示如何开始迁移到 ASP.NET MVC 项目[ASP.NET Core MVC](../mvc/overview.md)。</span><span class="sxs-lookup"><span data-stu-id="b883a-105">This article shows how to get started migrating an ASP.NET MVC project to [ASP.NET Core MVC](../mvc/overview.md).</span></span> <span data-ttu-id="b883a-106">在过程中，它重点介绍了许多的 ASP.NET MVC 中已更改的事项。</span><span class="sxs-lookup"><span data-stu-id="b883a-106">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="b883a-107">从 ASP.NET MVC 迁移是一个多步骤过程，这篇文章介绍初始设置、 基本控制器和视图、 静态内容和客户端的依赖项。</span><span class="sxs-lookup"><span data-stu-id="b883a-107">Migrating from ASP.NET MVC is a multiple step process and this article covers the initial setup, basic controllers and views, static content, and client-side dependencies.</span></span> <span data-ttu-id="b883a-108">其他文章介绍了迁移配置和多个 ASP.NET MVC 项目中的标识代码。</span><span class="sxs-lookup"><span data-stu-id="b883a-108">Additional articles cover migrating configuration and identity code found in many ASP.NET MVC projects.</span></span>

> [!NOTE]
> <span data-ttu-id="b883a-109">这些示例中的版本号可能不是最新。</span><span class="sxs-lookup"><span data-stu-id="b883a-109">The version numbers in the samples might not be current.</span></span> <span data-ttu-id="b883a-110">您可能需要相应地更新你的项目。</span><span class="sxs-lookup"><span data-stu-id="b883a-110">You may need to update your projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="b883a-111">创建入门级的 ASP.NET MVC 项目</span><span class="sxs-lookup"><span data-stu-id="b883a-111">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="b883a-112">若要演示升级，我们将首先创建一个 ASP.NET MVC 应用。</span><span class="sxs-lookup"><span data-stu-id="b883a-112">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="b883a-113">创建同名*WebApp1*使命名空间匹配我们在下一步中创建 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="b883a-113">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project we create in the next step.</span></span>

![Visual Studio 新项目对话框](mvc/_static/new-project.png)

![新建 Web 应用程序对话框：ASP.NET 模板窗格中选择 MVC 项目模板](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="b883a-116">*可选：* 更改从解决方案的名称*WebApp1*到*Mvc5*。</span><span class="sxs-lookup"><span data-stu-id="b883a-116">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5*.</span></span> <span data-ttu-id="b883a-117">Visual Studio 将显示新的解决方案名称 (*Mvc5*)，从而更轻松地判断此项目从下一个项目。</span><span class="sxs-lookup"><span data-stu-id="b883a-117">Visual Studio displays the new solution name (*Mvc5*), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="b883a-118">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="b883a-118">Create the ASP.NET Core project</span></span>

<span data-ttu-id="b883a-119">创建一个新*空*ASP.NET Core 与以前的项目同名的 web 应用 (*WebApp1*) 以便将两个项目中的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="b883a-119">Create a new *empty* ASP.NET Core web app with the same name as the previous project (*WebApp1*) so the namespaces in the two projects match.</span></span> <span data-ttu-id="b883a-120">具有相同的命名空间，可以更轻松地将代码在两个项目之间复制。</span><span class="sxs-lookup"><span data-stu-id="b883a-120">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="b883a-121">您必须在与上一个项目以使用相同的名称不同的目录中创建此项目。</span><span class="sxs-lookup"><span data-stu-id="b883a-121">You'll have to create this project in a different directory than the previous project to use the same name.</span></span>

![“新建项目”对话框](mvc/_static/new_core.png)

![新建 ASP.NET Web 应用程序对话框：在 ASP.NET Core 模板面板中选择的空项目模板](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="b883a-124">*可选：* 创建新的 ASP.NET Core 应用程序使用*Web 应用程序*项目模板。</span><span class="sxs-lookup"><span data-stu-id="b883a-124">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="b883a-125">将项目命名*WebApp1*，再选择身份验证选项的**单个用户帐户**。</span><span class="sxs-lookup"><span data-stu-id="b883a-125">Name the project *WebApp1*, and select an authentication option of **Individual User Accounts**.</span></span> <span data-ttu-id="b883a-126">重命名此应用*FullAspNetCore*。</span><span class="sxs-lookup"><span data-stu-id="b883a-126">Rename this app to *FullAspNetCore*.</span></span> <span data-ttu-id="b883a-127">在转换过程中创建此项目为您节省时间。</span><span class="sxs-lookup"><span data-stu-id="b883a-127">Creating this project saves you time in the conversion.</span></span> <span data-ttu-id="b883a-128">您可以看一下若要查看最终结果，或将代码复制到转换项目模板生成代码。</span><span class="sxs-lookup"><span data-stu-id="b883a-128">You can look at the template-generated code to see the end result or to copy code to the conversion project.</span></span> <span data-ttu-id="b883a-129">如果您遇到与模板生成项目进行比较的转换步骤，该技术还有助于。</span><span class="sxs-lookup"><span data-stu-id="b883a-129">It's also helpful when you get stuck on a conversion step to compare with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="b883a-130">将站点配置为使用 MVC</span><span class="sxs-lookup"><span data-stu-id="b883a-130">Configure the site to use MVC</span></span>

::: moniker range=">= aspnetcore-2.1"

* <span data-ttu-id="b883a-131">当面向.NET Core [Microsoft.AspNetCore.App 元包](xref:fundamentals/metapackage-app)默认情况下引用。</span><span class="sxs-lookup"><span data-stu-id="b883a-131">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="b883a-132">此包包含常用包的包 MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="b883a-132">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="b883a-133">如果面向.NET Framework，包引用必须单独列出项目文件中。</span><span class="sxs-lookup"><span data-stu-id="b883a-133">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="= aspnetcore-2.0"

* <span data-ttu-id="b883a-134">当面向.NET Core [Microsoft.AspNetCore.All 元包](xref:fundamentals/metapackage)默认情况下引用。</span><span class="sxs-lookup"><span data-stu-id="b883a-134">When targeting .NET Core, the [Microsoft.AspNetCore.All metapackage](xref:fundamentals/metapackage) is referenced by default.</span></span> <span data-ttu-id="b883a-135">此包包含常用包的包 MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="b883a-135">This package contains packages commonly used packages by MVC apps.</span></span> <span data-ttu-id="b883a-136">如果面向.NET Framework，包引用必须单独列出项目文件中。</span><span class="sxs-lookup"><span data-stu-id="b883a-136">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-2.0"

* <span data-ttu-id="b883a-137">当目标.NET Core 或.NET Framework，包通常用于 MVC 应用程序列出包单独项目文件中。</span><span class="sxs-lookup"><span data-stu-id="b883a-137">When targeting .NET Core or .NET Framework, packages commonly used packages by MVC apps are listed individually in the project file.</span></span>

::: moniker-end

<span data-ttu-id="b883a-138">`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core MVC 框架。</span><span class="sxs-lookup"><span data-stu-id="b883a-138">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="b883a-139">`Microsoft.AspNetCore.StaticFiles` 是静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="b883a-139">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="b883a-140">ASP.NET Core 运行时是一个模块化，和中，你必须显式选择要为静态文件服务 (请参阅[静态文件](xref:fundamentals/static-files))。</span><span class="sxs-lookup"><span data-stu-id="b883a-140">The ASP.NET Core runtime is modular, and you must explicitly opt in to serve static files (see [Static files](xref:fundamentals/static-files)).</span></span>

* <span data-ttu-id="b883a-141">打开*Startup.cs*文件，并更改代码以匹配以下内容：</span><span class="sxs-lookup"><span data-stu-id="b883a-141">Open the *Startup.cs* file and change the code to match the following:</span></span>

  [!code-csharp[](mvc/sample/Startup.cs?highlight=13,26-31)]

<span data-ttu-id="b883a-142">`UseStaticFiles`扩展方法将添加静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="b883a-142">The `UseStaticFiles` extension method adds the static file handler.</span></span> <span data-ttu-id="b883a-143">正如前面提到的是模块化，ASP.NET 运行时，你必须显式选择中提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="b883a-143">As mentioned previously, the ASP.NET runtime is modular, and you must explicitly opt in to serve static files.</span></span> <span data-ttu-id="b883a-144">`UseMvc`扩展方法将添加路由。</span><span class="sxs-lookup"><span data-stu-id="b883a-144">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="b883a-145">有关详细信息，请参阅[应用程序启动](xref:fundamentals/startup)并[路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="b883a-145">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="b883a-146">添加控制器和视图</span><span class="sxs-lookup"><span data-stu-id="b883a-146">Add a controller and view</span></span>

<span data-ttu-id="b883a-147">在本部分中，您将添加一个最小控制器和视图，以用作占位符的 ASP.NET MVC 控制器和视图将在下一节中迁移。</span><span class="sxs-lookup"><span data-stu-id="b883a-147">In this section, you'll add a minimal controller and view to serve as placeholders for the ASP.NET MVC controller and views you'll migrate in the next section.</span></span>

* <span data-ttu-id="b883a-148">添加*控制器*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-148">Add a *Controllers* folder.</span></span>

* <span data-ttu-id="b883a-149">添加**控制器类**名为*HomeController.cs*到*控制器*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-149">Add a **Controller Class** named *HomeController.cs* to the *Controllers* folder.</span></span>

![“添加新项”对话框](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="b883a-151">添加*视图*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-151">Add a *Views* folder.</span></span>

* <span data-ttu-id="b883a-152">添加*Views/Home*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-152">Add a *Views/Home* folder.</span></span>

* <span data-ttu-id="b883a-153">添加**Razor 视图**名为*Index.cshtml*到*Views/Home*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-153">Add a **Razor View** named *Index.cshtml* to the *Views/Home* folder.</span></span>

![“添加新项”对话框](mvc/_static/view.png)

<span data-ttu-id="b883a-155">项目结构如下所示：</span><span class="sxs-lookup"><span data-stu-id="b883a-155">The project structure is shown below:</span></span>

![显示文件和文件夹的 WebApp1 的解决方案资源管理器](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="b883a-157">内容替换为*Views/Home/Index.cshtml*具有以下文件：</span><span class="sxs-lookup"><span data-stu-id="b883a-157">Replace the contents of the *Views/Home/Index.cshtml* file with the following:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="b883a-158">运行应用。</span><span class="sxs-lookup"><span data-stu-id="b883a-158">Run the app.</span></span>

![在 Microsoft Edge 中打开 web 应用](mvc/_static/hello-world.png)

<span data-ttu-id="b883a-160">请参阅[控制器](xref:mvc/controllers/actions)并[视图](xref:mvc/views/overview)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="b883a-160">See [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview) for more information.</span></span>

<span data-ttu-id="b883a-161">现在，我们已最小的工作 ASP.NET Core 项目，我们可以开始从 ASP.NET MVC 项目迁移功能。</span><span class="sxs-lookup"><span data-stu-id="b883a-161">Now that we have a minimal working ASP.NET Core project, we can start migrating functionality from the ASP.NET MVC project.</span></span> <span data-ttu-id="b883a-162">我们需要移动以下：</span><span class="sxs-lookup"><span data-stu-id="b883a-162">We need to move the following:</span></span>

* <span data-ttu-id="b883a-163">客户端的内容 （CSS、 字体和脚本）</span><span class="sxs-lookup"><span data-stu-id="b883a-163">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="b883a-164">控制器</span><span class="sxs-lookup"><span data-stu-id="b883a-164">controllers</span></span>

* <span data-ttu-id="b883a-165">视图</span><span class="sxs-lookup"><span data-stu-id="b883a-165">views</span></span>

* <span data-ttu-id="b883a-166">模型</span><span class="sxs-lookup"><span data-stu-id="b883a-166">models</span></span>

* <span data-ttu-id="b883a-167">捆绑</span><span class="sxs-lookup"><span data-stu-id="b883a-167">bundling</span></span>

* <span data-ttu-id="b883a-168">筛选器</span><span class="sxs-lookup"><span data-stu-id="b883a-168">filters</span></span>

* <span data-ttu-id="b883a-169">输入/输出的日志，标识 （这是在下一步的教程。）</span><span class="sxs-lookup"><span data-stu-id="b883a-169">Log in/out, Identity (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="b883a-170">控制器和视图</span><span class="sxs-lookup"><span data-stu-id="b883a-170">Controllers and views</span></span>

* <span data-ttu-id="b883a-171">复制每个方法从 ASP.NET MVC`HomeController`对新`HomeController`。</span><span class="sxs-lookup"><span data-stu-id="b883a-171">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="b883a-172">请注意，在 ASP.NET MVC 内置模板的控制器操作方法的返回类型[ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); 在 ASP.NET Core MVC，操作方法返回`IActionResult`相反。</span><span class="sxs-lookup"><span data-stu-id="b883a-172">Note that in ASP.NET MVC, the built-in template's controller action method return type is [ActionResult](https://msdn.microsoft.com/library/system.web.mvc.actionresult(v=vs.118).aspx); in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="b883a-173">`ActionResult` 实现`IActionResult`，因此无需更改您的操作方法的返回类型。</span><span class="sxs-lookup"><span data-stu-id="b883a-173">`ActionResult` implements `IActionResult`, so there's no need to change the return type of your action methods.</span></span>

* <span data-ttu-id="b883a-174">复制*About.cshtml*， *Contact.cshtml*，和*Index.cshtml* Razor 视图文件从 ASP.NET MVC 项目添加到 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="b883a-174">Copy the *About.cshtml*, *Contact.cshtml*, and *Index.cshtml* Razor view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

* <span data-ttu-id="b883a-175">运行 ASP.NET Core 应用和测试每个方法。</span><span class="sxs-lookup"><span data-stu-id="b883a-175">Run the ASP.NET Core app and test each method.</span></span> <span data-ttu-id="b883a-176">我们还没有迁移样式的布局文件，因此呈现的视图仅包含在视图文件中的内容。</span><span class="sxs-lookup"><span data-stu-id="b883a-176">We haven't migrated the layout file or styles yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="b883a-177">不会有的布局文件生成链接`About`并`Contact`视图，因此您必须调用，从浏览器 (替换**4492**与项目中使用的端口号)。</span><span class="sxs-lookup"><span data-stu-id="b883a-177">You won't have the layout file generated links for the `About` and `Contact` views, so you'll have to invoke them from the browser (replace **4492** with the port number used in your project).</span></span>

  * `http://localhost:4492/home/about`

  * `http://localhost:4492/home/contact`

![联系信息页](mvc/_static/contact-page.png)

<span data-ttu-id="b883a-179">请注意，没有样式设置和菜单项。</span><span class="sxs-lookup"><span data-stu-id="b883a-179">Note the lack of styling and menu items.</span></span> <span data-ttu-id="b883a-180">此问题将在下一部分得以解决。</span><span class="sxs-lookup"><span data-stu-id="b883a-180">We'll fix that in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="b883a-181">静态内容</span><span class="sxs-lookup"><span data-stu-id="b883a-181">Static content</span></span>

<span data-ttu-id="b883a-182">在以前版本的 ASP.NET MVC 中，静态内容托管从 web 项目的根目录和服务器端文件混合。</span><span class="sxs-lookup"><span data-stu-id="b883a-182">In previous versions of ASP.NET MVC, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="b883a-183">在 ASP.NET Core 静态内容承载于*wwwroot*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-183">In ASP.NET Core, static content is hosted in the *wwwroot* folder.</span></span> <span data-ttu-id="b883a-184">你将想要复制的静态内容从旧 ASP.NET MVC 应用程序到*wwwroot* ASP.NET Core 项目文件夹中的。</span><span class="sxs-lookup"><span data-stu-id="b883a-184">You'll want to copy the static content from your old ASP.NET MVC app to the *wwwroot* folder in your ASP.NET Core project.</span></span> <span data-ttu-id="b883a-185">在此示例转换：</span><span class="sxs-lookup"><span data-stu-id="b883a-185">In this sample conversion:</span></span>

* <span data-ttu-id="b883a-186">复制*favicon.ico*文件从旧的 MVC 项目到*wwwroot* ASP.NET Core 项目文件夹中的。</span><span class="sxs-lookup"><span data-stu-id="b883a-186">Copy the *favicon.ico* file from the old MVC project to the *wwwroot* folder in the ASP.NET Core project.</span></span>

<span data-ttu-id="b883a-187">旧 ASP.NET MVC 项目使用[Bootstrap](https://getbootstrap.com/) Bootstrap 文件在其样式以及存储区*内容*并*脚本*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-187">The old ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* folders.</span></span> <span data-ttu-id="b883a-188">模板，用于生成旧的 ASP.NET MVC 项目，引用布局文件中的 Bootstrap (*Views/Shared/_Layout.cshtml*)。</span><span class="sxs-lookup"><span data-stu-id="b883a-188">The template, which generated the old ASP.NET MVC project, references Bootstrap in the layout file (*Views/Shared/_Layout.cshtml*).</span></span> <span data-ttu-id="b883a-189">无法复制*bootstrap.js*并*bootstrap.css*文件从 ASP.NET MVC 项目到*wwwroot*新项目文件夹中的。</span><span class="sxs-lookup"><span data-stu-id="b883a-189">You could copy the *bootstrap.js* and *bootstrap.css* files from the ASP.NET MVC project to the *wwwroot* folder in the new project.</span></span> <span data-ttu-id="b883a-190">相反，我们将添加对 Bootstrap 的支持 （和其他客户端库），它在下一节中使用 Cdn。</span><span class="sxs-lookup"><span data-stu-id="b883a-190">Instead, we'll add support for Bootstrap (and other client-side libraries) using CDNs in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="b883a-191">将布局文件中迁移</span><span class="sxs-lookup"><span data-stu-id="b883a-191">Migrate the layout file</span></span>

* <span data-ttu-id="b883a-192">复制 *_ViewStart.cshtml*文件从旧的 ASP.NET MVC 项目*视图*文件夹导入到 ASP.NET Core项目*视图*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-192">Copy the *_ViewStart.cshtml* file from the old ASP.NET MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="b883a-193">*_ViewStart.cshtml*文件未更改 ASP.NET Core mvc。</span><span class="sxs-lookup"><span data-stu-id="b883a-193">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="b883a-194">创建*Views/Shared*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-194">Create a *Views/Shared* folder.</span></span>

* <span data-ttu-id="b883a-195">*可选：* 复制 *_ViewImports.cshtml*从*FullAspNetCore* MVC 项目*视图*到 ASP.NET Core 项目的文件夹*视图*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-195">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* folder into the ASP.NET Core project's *Views* folder.</span></span> <span data-ttu-id="b883a-196">中的任何命名空间声明中删除 *_ViewImports.cshtml*文件。</span><span class="sxs-lookup"><span data-stu-id="b883a-196">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="b883a-197">*_ViewImports.cshtml*文件的所有视图文件提供了命名空间和带来[标记帮助程序](xref:mvc/views/tag-helpers/intro)。</span><span class="sxs-lookup"><span data-stu-id="b883a-197">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="b883a-198">新的布局文件中使用标记帮助程序。</span><span class="sxs-lookup"><span data-stu-id="b883a-198">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="b883a-199">*_ViewImports.cshtml*文件是用于 ASP.NET Core 新功能。</span><span class="sxs-lookup"><span data-stu-id="b883a-199">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="b883a-200">复制 *_Layout.cshtml*文件从旧的 ASP.NET MVC 项目*视图/共享*文件夹导入到 ASP.NET Core 项目*视图/共享*文件夹。</span><span class="sxs-lookup"><span data-stu-id="b883a-200">Copy the *_Layout.cshtml* file from the old ASP.NET MVC project's *Views/Shared* folder into the ASP.NET Core project's *Views/Shared* folder.</span></span>

<span data-ttu-id="b883a-201">打开 *_Layout.cshtml*文件，然后进行以下更改 （如下所示的已完成的代码）：</span><span class="sxs-lookup"><span data-stu-id="b883a-201">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="b883a-202">替换`@Styles.Render("~/Content/css")`与`<link>`元素来加载*bootstrap.css* （见下文）。</span><span class="sxs-lookup"><span data-stu-id="b883a-202">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="b883a-203">删除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="b883a-203">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="b883a-204">注释掉`@Html.Partial("_LoginPartial")`行 (括在行的`@*...*@`)。</span><span class="sxs-lookup"><span data-stu-id="b883a-204">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="b883a-205">有关详细信息请参阅[迁移身份验证和标识到 ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="b883a-205">For more information see [Migrate Authentication and Identity to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="b883a-206">替换`@Scripts.Render("~/bundles/jquery")`与`<script>`元素 （见下文）。</span><span class="sxs-lookup"><span data-stu-id="b883a-206">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="b883a-207">替换`@Scripts.Render("~/bundles/bootstrap")`与`<script>`元素 （见下文）。</span><span class="sxs-lookup"><span data-stu-id="b883a-207">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="b883a-208">Bootstrap CSS 包含替换标记：</span><span class="sxs-lookup"><span data-stu-id="b883a-208">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="b883a-209">JQuery 和 Bootstrap JavaScript 包含的替换标记：</span><span class="sxs-lookup"><span data-stu-id="b883a-209">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="b883a-210">已更新 *_Layout.cshtml*文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="b883a-210">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/sample/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="b883a-211">在浏览器中查看该站点。</span><span class="sxs-lookup"><span data-stu-id="b883a-211">View the site in the browser.</span></span> <span data-ttu-id="b883a-212">它应现在正确加载，以就地预期样式。</span><span class="sxs-lookup"><span data-stu-id="b883a-212">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="b883a-213">*可选：* 您可能想要尝试使用新的布局文件。</span><span class="sxs-lookup"><span data-stu-id="b883a-213">*Optional:* You might want to try using the new layout file.</span></span> <span data-ttu-id="b883a-214">对于此项目中，你可以复制中的布局文件*FullAspNetCore*项目。</span><span class="sxs-lookup"><span data-stu-id="b883a-214">For this project you can copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="b883a-215">新的布局文件使用[标记帮助程序](xref:mvc/views/tag-helpers/intro)并且具有其他改进。</span><span class="sxs-lookup"><span data-stu-id="b883a-215">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="b883a-216">配置捆绑和缩减</span><span class="sxs-lookup"><span data-stu-id="b883a-216">Configure bundling and minification</span></span>

<span data-ttu-id="b883a-217">有关如何配置绑定和缩减的信息，请参阅[捆绑和缩减](../client-side/bundling-and-minification.md)。</span><span class="sxs-lookup"><span data-stu-id="b883a-217">For information about how to configure bundling and minification, see [Bundling and Minification](../client-side/bundling-and-minification.md).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="b883a-218">解决 HTTP 500 错误</span><span class="sxs-lookup"><span data-stu-id="b883a-218">Solve HTTP 500 errors</span></span>

<span data-ttu-id="b883a-219">有许多可能会导致 HTTP 500 错误消息包含没有关于此问题的源的信息的问题。</span><span class="sxs-lookup"><span data-stu-id="b883a-219">There are many problems that can cause a HTTP 500 error message that contain no information on the source of the problem.</span></span> <span data-ttu-id="b883a-220">例如，如果*views/_viewimports.cshtml*文件包含在项目中不存在的命名空间，您将收到 HTTP 500 错误。</span><span class="sxs-lookup"><span data-stu-id="b883a-220">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in your project, you'll get a HTTP 500 error.</span></span> <span data-ttu-id="b883a-221">默认情况下，在 ASP.NET Core 应用中，`UseDeveloperExceptionPage`扩展添加到`IApplicationBuilder`和执行在配置后*开发*。</span><span class="sxs-lookup"><span data-stu-id="b883a-221">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development*.</span></span> <span data-ttu-id="b883a-222">这将在下面的代码中详细说明：</span><span class="sxs-lookup"><span data-stu-id="b883a-222">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/sample/Startup.cs?highlight=19-22)]

<span data-ttu-id="b883a-223">ASP.NET Core 将在 web 应用程序中未经处理的异常转换为 HTTP 500 错误响应。</span><span class="sxs-lookup"><span data-stu-id="b883a-223">ASP.NET Core converts unhandled exceptions in a web app into HTTP 500 error responses.</span></span> <span data-ttu-id="b883a-224">通常情况下，错误详细信息不包括在这些响应来防止泄露有关服务器的潜在敏感信息。</span><span class="sxs-lookup"><span data-stu-id="b883a-224">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="b883a-225">请参阅**使用开发人员异常页**中[处理错误](../fundamentals/error-handling.md)有关详细信息。</span><span class="sxs-lookup"><span data-stu-id="b883a-225">See **Using the Developer Exception Page** in [Handle errors](../fundamentals/error-handling.md) for more information.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="b883a-226">其他资源</span><span class="sxs-lookup"><span data-stu-id="b883a-226">Additional resources</span></span>

* <xref:razor-components/index>
* <xref:mvc/views/tag-helpers/intro>
