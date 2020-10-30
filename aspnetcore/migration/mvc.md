---
title: 了解如何从 ASP.NET MVC 迁移到 ASP.NET Core MVC
author: wadepickett
description: 了解如何开始将 ASP.NET MVC 项目迁移到 ASP.NET Core MVC。
ms.author: wpickett
ms.date: 06/18/2020
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
uid: migration/mvc
ms.openlocfilehash: 226ac6da508378c7b3c81779d38dd2e0840f1fed
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93051508"
---
# <a name="migrate-from-aspnet-mvc-to-aspnet-core-mvc"></a><span data-ttu-id="9ac19-103">从 ASP.NET MVC 迁移到 ASP.NET Core MVC</span><span class="sxs-lookup"><span data-stu-id="9ac19-103">Migrate from ASP.NET MVC to ASP.NET Core MVC</span></span>

::: moniker range=">= aspnetcore-3.0"

<span data-ttu-id="9ac19-104">本文介绍如何开始将 ASP.NET MVC 项目迁移到 [ASP.NET CORE mvc](xref:mvc/overview)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-104">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview).</span></span> <span data-ttu-id="9ac19-105">在此过程中，它突出显示了 ASP.NET MVC 中的相关更改。</span><span class="sxs-lookup"><span data-stu-id="9ac19-105">In the process, it highlights related changes from ASP.NET MVC.</span></span>

<span data-ttu-id="9ac19-106">从 ASP.NET MVC 迁移是一个多步骤过程。</span><span class="sxs-lookup"><span data-stu-id="9ac19-106">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="9ac19-107">本文介绍：</span><span class="sxs-lookup"><span data-stu-id="9ac19-107">This article covers:</span></span>

* <span data-ttu-id="9ac19-108">初始设置。</span><span class="sxs-lookup"><span data-stu-id="9ac19-108">Initial setup.</span></span>
* <span data-ttu-id="9ac19-109">基本控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-109">Basic controllers and views.</span></span>
* <span data-ttu-id="9ac19-110">静态内容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-110">Static content.</span></span>
* <span data-ttu-id="9ac19-111">客户端依赖关系。</span><span class="sxs-lookup"><span data-stu-id="9ac19-111">Client-side dependencies.</span></span>

<span data-ttu-id="9ac19-112">若要迁移配置和 :::no-loc(Identity)::: 代码，请参阅将 [配置迁移到 ASP.NET Core](xref:migration/configuration) 并 [迁移身份验证和 :::no-loc(Identity)::: ASP.NET Core](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-112">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

## <a name="prerequisites"></a><span data-ttu-id="9ac19-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="9ac19-113">Prerequisites</span></span>

[!INCLUDE [prerequisites](../includes/net-core-prereqs-vs-3.1.md)]

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="9ac19-114">创建 starter ASP.NET MVC 项目</span><span class="sxs-lookup"><span data-stu-id="9ac19-114">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="9ac19-115">在 Visual Studio 中创建一个示例 ASP.NET MVC 项目以进行迁移：</span><span class="sxs-lookup"><span data-stu-id="9ac19-115">Create an example ASP.NET MVC project in Visual Studio to migrate:</span></span>

1. <span data-ttu-id="9ac19-116">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="9ac19-116">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="9ac19-117">选择 " **ASP.NET Web 应用程序 ( .NET Framework")** ，然后选择 " **下一步** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-117">Select **ASP.NET Web Application (.NET Framework)** and then select **Next** .</span></span>
1. <span data-ttu-id="9ac19-118">将项目命名为 *WebApp1* ，使命名空间与下一步中创建的 ASP.NET Core 项目相匹配。</span><span class="sxs-lookup"><span data-stu-id="9ac19-118">Name the project *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span> <span data-ttu-id="9ac19-119">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="9ac19-119">Select **Create** .</span></span>
1. <span data-ttu-id="9ac19-120">选择 " **MVC** "，然后选择 " **创建** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-120">Select **MVC** , and then select **Create** .</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="9ac19-121">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="9ac19-121">Create the ASP.NET Core project</span></span>

<span data-ttu-id="9ac19-122">使用要迁移到的新 ASP.NET Core 项目创建新的解决方案：</span><span class="sxs-lookup"><span data-stu-id="9ac19-122">Create a new solution with a new ASP.NET Core project to migrate to:</span></span>

1. <span data-ttu-id="9ac19-123">启动 Visual Studio 的第二个实例。</span><span class="sxs-lookup"><span data-stu-id="9ac19-123">Launch a second instance of Visual Studio.</span></span>
1. <span data-ttu-id="9ac19-124">从“文件”菜单中选择“新建”>“项目”  。</span><span class="sxs-lookup"><span data-stu-id="9ac19-124">From the **File** menu, select **New** > **Project** .</span></span>
1. <span data-ttu-id="9ac19-125">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-125">Select **ASP.NET Core Web Application** and then select **Next** .</span></span>
1. <span data-ttu-id="9ac19-126">在 " **配置新项目** " 对话框中，将项目命名为 " *WebApp1* "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-126">In the **Configure your new project** dialog, Name the project *WebApp1* .</span></span>
1. <span data-ttu-id="9ac19-127">将位置设置为与上一个项目不同的目录，以使用相同的项目名称。</span><span class="sxs-lookup"><span data-stu-id="9ac19-127">Set the location to a different directory than the previous project to use the same project name.</span></span> <span data-ttu-id="9ac19-128">使用同一个命名空间可以更轻松地在两个项目之间复制代码。</span><span class="sxs-lookup"><span data-stu-id="9ac19-128">Using the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="9ac19-129">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="9ac19-129">Select **Create** .</span></span>
1. <span data-ttu-id="9ac19-130">在“创建新的 ASP.NET Core Web 应用程序”对话框中，确认选择“.NET Core”和“ASP.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="9ac19-130">In the **Create a new ASP.NET Core Web Application** dialog, confirm that **.NET Core** and **ASP.NET Core 3.1** are selected.</span></span> <span data-ttu-id="9ac19-131">选择 " **Web 应用程序 (模型-视图-控制器)** " 项目模板，然后选择 " **创建** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-131">Select the **Web Application (Model-View-Controller)** project template, and select **Create** .</span></span>

## <a name="configure-the-aspnet-core-site-to-use-mvc"></a><span data-ttu-id="9ac19-132">将 ASP.NET Core 网站配置为使用 MVC</span><span class="sxs-lookup"><span data-stu-id="9ac19-132">Configure the ASP.NET Core site to use MVC</span></span>

<span data-ttu-id="9ac19-133">在 ASP.NET Core 3.0 及更高版本的项目中，.NET Framework 不再是受支持的目标框架。</span><span class="sxs-lookup"><span data-stu-id="9ac19-133">In ASP.NET Core 3.0 and later projects, .NET Framework is no longer a supported target framework.</span></span> <span data-ttu-id="9ac19-134">你的项目必须面向 .NET Core。</span><span class="sxs-lookup"><span data-stu-id="9ac19-134">Your project must target .NET Core.</span></span> <span data-ttu-id="9ac19-135">包含 MVC 的 ASP.NET Core 共享框架是 .NET Core 运行时安装的一部分。</span><span class="sxs-lookup"><span data-stu-id="9ac19-135">The ASP.NET Core shared framework, which includes MVC, is part of the .NET Core runtime installation.</span></span> <span data-ttu-id="9ac19-136">使用项目文件中的 `Microsoft.NET.Sdk.Web` SDK 时，会自动引用共享框架：</span><span class="sxs-lookup"><span data-stu-id="9ac19-136">The shared framework is automatically referenced when using the `Microsoft.NET.Sdk.Web` SDK in the project file:</span></span>

```xml
<Project Sdk="Microsoft.NET.Sdk.Web">
```

<span data-ttu-id="9ac19-137">有关详细信息，请参阅 [框架引用](xref:migration/22-to-30#framework-reference)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-137">For more information, see [Framework reference](xref:migration/22-to-30#framework-reference).</span></span>

<span data-ttu-id="9ac19-138">在 ASP.NET Core 中， `Startup` 类：</span><span class="sxs-lookup"><span data-stu-id="9ac19-138">In ASP.NET Core, the `Startup` class:</span></span>

* <span data-ttu-id="9ac19-139">替换 *global.asax* 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-139">Replaces *Global.asax* .</span></span>
* <span data-ttu-id="9ac19-140">处理所有应用启动任务。</span><span class="sxs-lookup"><span data-stu-id="9ac19-140">Handles all app startup tasks.</span></span>

<span data-ttu-id="9ac19-141">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="9ac19-141">For more information, see <xref:fundamentals/startup>.</span></span>

<span data-ttu-id="9ac19-142">在 ASP.NET Core 项目中，打开 *Startup.cs* 文件：</span><span class="sxs-lookup"><span data-stu-id="9ac19-142">In the ASP.NET Core project, open the *Startup.cs* file:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=13,30,32&name=snippet)]

<span data-ttu-id="9ac19-143">ASP.NET Core 应用必须选择包含中间件的框架功能。</span><span class="sxs-lookup"><span data-stu-id="9ac19-143">ASP.NET Core apps must opt in to framework features with middleware.</span></span> <span data-ttu-id="9ac19-144">上一个模板生成的代码添加以下服务和中间件：</span><span class="sxs-lookup"><span data-stu-id="9ac19-144">The previous template-generated code adds the following services and middleware:</span></span>

* <span data-ttu-id="9ac19-145"><xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A>扩展方法为控制器、API 相关的功能和视图注册 MVC 服务支持。</span><span class="sxs-lookup"><span data-stu-id="9ac19-145">The <xref:Microsoft.Extensions.DependencyInjection.MvcServiceCollectionExtensions.AddControllersWithViews%2A> extension method registers MVC service support for controllers, API-related features, and views.</span></span> <span data-ttu-id="9ac19-146">有关 MVC 服务注册选项的详细信息，请参阅 [mvc 服务注册](xref:migration/22-to-30#mvc-service-registration)</span><span class="sxs-lookup"><span data-stu-id="9ac19-146">For more information on MVC service registration options, see [MVC service registration](xref:migration/22-to-30#mvc-service-registration)</span></span>
* <span data-ttu-id="9ac19-147"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A>扩展方法添加静态文件处理程序 `Microsoft.AspNetCore.StaticFiles` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-147">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles%2A> extension method adds the static file handler `Microsoft.AspNetCore.StaticFiles`.</span></span> <span data-ttu-id="9ac19-148">`UseStaticFiles`必须先调用扩展方法 `UseRouting` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-148">The `UseStaticFiles` extension method must be called before `UseRouting`.</span></span> <span data-ttu-id="9ac19-149">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="9ac19-149">For more information, see <xref:fundamentals/static-files>.</span></span>
* <span data-ttu-id="9ac19-150"><xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A>扩展方法将添加路由。</span><span class="sxs-lookup"><span data-stu-id="9ac19-150">The <xref:Microsoft.AspNetCore.Builder.EndpointRoutingApplicationBuilderExtensions.UseRouting%2A> extension method adds routing.</span></span> <span data-ttu-id="9ac19-151">有关详细信息，请参阅 <xref:fundamentals/routing>。</span><span class="sxs-lookup"><span data-stu-id="9ac19-151">For more information, see <xref:fundamentals/routing>.</span></span>

<span data-ttu-id="9ac19-152">此现有配置包括迁移示例 ASP.NET MVC 项目所需的内容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-152">This existing configuration includes what is needed to migrate the example ASP.NET MVC project.</span></span> <span data-ttu-id="9ac19-153">有关 ASP.NET Core 中间件选项的详细信息，请参阅 <xref:fundamentals/startup> 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-153">For more information on ASP.NET Core middleware options, see <xref:fundamentals/startup>.</span></span>

## <a name="migrate-controllers-and-views"></a><span data-ttu-id="9ac19-154">迁移控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-154">Migrate controllers and views</span></span>

<span data-ttu-id="9ac19-155">在 ASP.NET Core 项目中，将添加新的空控制器类和视图类作为占位符，使用与要从中进行迁移的任何 ASP.NET MVC 项目中的控制器和视图类相同的名称。</span><span class="sxs-lookup"><span data-stu-id="9ac19-155">In the ASP.NET Core project, a new empty controller class and view class would be added to serve as placeholders using the same names as the controller and view classes in any ASP.NET MVC project to migrate from.</span></span>

<span data-ttu-id="9ac19-156">ASP.NET Core *WebApp1* 项目已包含与 ASP.NET MVC 项目相同的名称的最小示例控制器和视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-156">The ASP.NET Core *WebApp1* project already includes a minimal example controller and view by the same name as the ASP.NET MVC project.</span></span> <span data-ttu-id="9ac19-157">因此，这些占位符将用作 ASP.NET MVC 控制器的占位符，以及要从 ASP.NET MVC *WebApp1* 项目迁移的视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-157">So those will serve as placeholders for the ASP.NET MVC controller and views to be migrated from the ASP.NET MVC *WebApp1* project.</span></span>

1. <span data-ttu-id="9ac19-158">复制 ASP.NET MVC 中的方法 `HomeController` 以替换新的 ASP.NET Core `HomeController` 方法。</span><span class="sxs-lookup"><span data-stu-id="9ac19-158">Copy the methods from the ASP.NET MVC `HomeController` to replace the new ASP.NET Core `HomeController` methods.</span></span> <span data-ttu-id="9ac19-159">无需更改操作方法的返回类型。</span><span class="sxs-lookup"><span data-stu-id="9ac19-159">There's no need to change the return type of the action methods.</span></span> <span data-ttu-id="9ac19-160">ASP.NET MVC 内置模板的控制器操作方法返回类型为 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; 在 ASP.NET CORE mvc 中，操作方法将改为返回 `IActionResult` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-160">The ASP.NET MVC built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="9ac19-161">`ActionResult` 可实现 `IActionResult`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-161">`ActionResult` implements `IActionResult`.</span></span>
1. <span data-ttu-id="9ac19-162">在 ASP.NET Core 项目中，右键单击 " *视图"/"主* " 目录，选择 " **添加** > **现有项** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-162">In the ASP.NET Core project, right-click the *Views/Home* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="9ac19-163">在 " **添加现有项** " 对话框中，导航到 ASP.NET MVC *WebApp1* 项目的 " *视图"/"主* 目录"。</span><span class="sxs-lookup"><span data-stu-id="9ac19-163">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Home* directory.</span></span>
1. <span data-ttu-id="9ac19-164">选择 " *关于* *"，然后依次选择 "* *Index.cshtml* :::no-loc(Razor)::: **添加** "、"替换现有文件"。</span><span class="sxs-lookup"><span data-stu-id="9ac19-164">Select the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files, then select **Add** , replacing the existing files.</span></span>

<span data-ttu-id="9ac19-165">有关详细信息，请参阅 <xref:mvc/controllers/actions> 和 <xref:mvc/views/overview>。</span><span class="sxs-lookup"><span data-stu-id="9ac19-165">For more information, see <xref:mvc/controllers/actions> and <xref:mvc/views/overview>.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="9ac19-166">测试每个方法</span><span class="sxs-lookup"><span data-stu-id="9ac19-166">Test each method</span></span>

<span data-ttu-id="9ac19-167">可以测试每个控制器终结点，但在本文档的后面部分介绍了布局和样式。</span><span class="sxs-lookup"><span data-stu-id="9ac19-167">Each controller endpoint can be tested, however, layout and styles are covered later in the document.</span></span>

1. <span data-ttu-id="9ac19-168">运行 ASP.NET Core 应用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-168">Run the ASP.NET Core app.</span></span>
1. <span data-ttu-id="9ac19-169">通过将当前端口号替换为 ASP.NET Core 项目中使用的端口号，在运行 ASP.NET Core 应用程序的浏览器中调用呈现的视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-169">Invoke the rendered views from the browser on the running ASP.NET Core app by replacing the current port number with the port number used in the ASP.NET Core project.</span></span> <span data-ttu-id="9ac19-170">例如 `https://localhost:44375/home/about`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-170">For example, `https://localhost:44375/home/about`.</span></span>

## <a name="migrate-static-content"></a><span data-ttu-id="9ac19-171">迁移静态内容</span><span class="sxs-lookup"><span data-stu-id="9ac19-171">Migrate static content</span></span>

<span data-ttu-id="9ac19-172">在 ASP.NET MVC 5 及更早版本中，静态内容是从 web 项目的根目录承载的，与服务器端文件混合。</span><span class="sxs-lookup"><span data-stu-id="9ac19-172">In ASP.NET MVC 5 and earlier, static content was hosted from the web project's root directory and was intermixed with server-side files.</span></span> <span data-ttu-id="9ac19-173">在 ASP.NET Core 中，静态文件存储在项目的 [web 根目录](xref:fundamentals/index#web-root) 中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-173">In ASP.NET Core, static files are stored within the project's [web root](xref:fundamentals/index#web-root) directory.</span></span> <span data-ttu-id="9ac19-174">默认目录为 *{content root}/wwwroot* ，但可以对其进行更改。</span><span class="sxs-lookup"><span data-stu-id="9ac19-174">The default directory is *{content root}/wwwroot* , but it can be changed.</span></span> <span data-ttu-id="9ac19-175">有关详细信息，请参阅 [ASP.NET Core 中的静态文件](xref:fundamentals/static-files#serve-static-files)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-175">For more information, see [Static files in ASP.NET Core](xref:fundamentals/static-files#serve-static-files).</span></span>

<span data-ttu-id="9ac19-176">将 ASP.NET MVC *WebApp1* 项目中的静态内容复制到 ASP.NET Core *WebApp1* 项目中的 *wwwroot* 目录：</span><span class="sxs-lookup"><span data-stu-id="9ac19-176">Copy the static content from the ASP.NET MVC *WebApp1* project to the *wwwroot* directory in the ASP.NET Core *WebApp1* project:</span></span>

1. <span data-ttu-id="9ac19-177">在 ASP.NET Core 项目中，右键单击 *wwwroot* 目录，选择 " **添加** > **现有项** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-177">In the ASP.NET Core project, right-click the *wwwroot* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="9ac19-178">在 " **添加现有项** " 对话框中，导航到 "ASP.NET MVC *WebApp1* " 项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-178">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project.</span></span>
1. <span data-ttu-id="9ac19-179">选择 *favicon* 文件，然后选择 " **添加** "，替换现有文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-179">Select the *favicon.ico* file, then select **Add** , replacing the existing file.</span></span>

## <a name="migrate-the-layout-files"></a><span data-ttu-id="9ac19-180">迁移布局文件</span><span class="sxs-lookup"><span data-stu-id="9ac19-180">Migrate the layout files</span></span>

<span data-ttu-id="9ac19-181">将 ASP.NET MVC 项目布局文件复制到 ASP.NET Core 项目：</span><span class="sxs-lookup"><span data-stu-id="9ac19-181">Copy the ASP.NET MVC project layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="9ac19-182">在 ASP.NET Core 项目中，右键单击 " *视图* " 目录，选择 " **添加** > **现有项** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-182">In the ASP.NET Core project, right-click the *Views* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="9ac19-183">在 " **添加现有项** " 对话框中，导航到 "ASP.NET MVC *WebApp1* " 项目的 " *视图* " 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-183">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views* directory.</span></span>
1. <span data-ttu-id="9ac19-184">选择 *_ViewStart* 的文件，然后选择 " **添加** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-184">Select the *_ViewStart.cshtml* file then select **Add** .</span></span>

<span data-ttu-id="9ac19-185">将 ASP.NET MVC 项目共享布局文件复制到 ASP.NET Core 项目：</span><span class="sxs-lookup"><span data-stu-id="9ac19-185">Copy the ASP.NET MVC project shared layout files to the ASP.NET Core project:</span></span>

1. <span data-ttu-id="9ac19-186">在 ASP.NET Core 项目中，右键单击 " *视图"/"共享* " 目录，选择 " **添加** > **现有项** "。</span><span class="sxs-lookup"><span data-stu-id="9ac19-186">In the ASP.NET Core project, right-click the *Views/Shared* directory, select **Add** > **Existing Item** .</span></span>
1. <span data-ttu-id="9ac19-187">在 " **添加现有项** " 对话框中，导航到 "ASP.NET MVC *WebApp1* " 项目的 " *视图"/"共享* 目录"。</span><span class="sxs-lookup"><span data-stu-id="9ac19-187">In the **Add Existing Item** dialog, navigate to the ASP.NET MVC *WebApp1* project's *Views/Shared* directory.</span></span>
1. <span data-ttu-id="9ac19-188">选择 *_Layout* 的文件，然后选择 " **添加** "，替换现有文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-188">Select the *_Layout.cshtml* file, then select **Add** , replacing the existing file.</span></span>

<span data-ttu-id="9ac19-189">在 ASP.NET Core 项目中，打开 *_Layout。*</span><span class="sxs-lookup"><span data-stu-id="9ac19-189">In the ASP.NET Core project, open the *_Layout.cshtml* file.</span></span> <span data-ttu-id="9ac19-190">进行以下更改，使其与下面显示的已完成代码相匹配：</span><span class="sxs-lookup"><span data-stu-id="9ac19-190">Make the following changes to match the completed code shown below:</span></span>

<span data-ttu-id="9ac19-191">更新启动 CSS 包含项以匹配以下已完成的代码：</span><span class="sxs-lookup"><span data-stu-id="9ac19-191">Update the Bootstrap CSS inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="9ac19-192">替换为 `@Styles.Render("~/Content/css")` `<link>` 加载 *启动 .css* 的元素 (参阅下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-192">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>
1. <span data-ttu-id="9ac19-193">删除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-193">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

<span data-ttu-id="9ac19-194">已完成的启动 CSS 包含的替换标记：</span><span class="sxs-lookup"><span data-stu-id="9ac19-194">The completed replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="9ac19-195">更新 jQuery 和启动 JavaScript 包含项以匹配以下已完成的代码：</span><span class="sxs-lookup"><span data-stu-id="9ac19-195">Update the jQuery and Bootstrap JavaScript inclusion to match the completed code below:</span></span>

1. <span data-ttu-id="9ac19-196">替换 `@Scripts.Render("~/bundles/jquery")` 为 `<script>` 元素 (参见下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-196">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>
1. <span data-ttu-id="9ac19-197">替换 `@Scripts.Render("~/bundles/bootstrap")` 为 `<script>` 元素 (参见下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-197">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="9ac19-198">JQuery 和启动 JavaScript 包含的已完成替换标记：</span><span class="sxs-lookup"><span data-stu-id="9ac19-198">The completed replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="9ac19-199">更新后的 *_Layout cshtml* 文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ac19-199">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/3.x/Views/Shared/_Layout.cshtml?highlight=7-10,40-42)]

<span data-ttu-id="9ac19-200">在浏览器中查看站点。</span><span class="sxs-lookup"><span data-stu-id="9ac19-200">View the site in the browser.</span></span> <span data-ttu-id="9ac19-201">它应采用所需的样式进行呈现。</span><span class="sxs-lookup"><span data-stu-id="9ac19-201">It should render with the expected styles in place.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="9ac19-202">配置捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="9ac19-202">Configure bundling and minification</span></span>

<span data-ttu-id="9ac19-203">ASP.NET Core 与若干开源绑定和缩减解决方案（例如 [WebOptimizer](https://github.com/ligershark/WebOptimizer) 和其他类似库）兼容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-203">ASP.NET Core is compatible with several open-source bundling and minification solutions such as [WebOptimizer](https://github.com/ligershark/WebOptimizer) and other similar libraries.</span></span> <span data-ttu-id="9ac19-204">ASP.NET Core 不提供本机绑定和缩减解决方案。</span><span class="sxs-lookup"><span data-stu-id="9ac19-204">ASP.NET Core doesn't provide a native bundling and minification solution.</span></span> <span data-ttu-id="9ac19-205">有关配置绑定和缩减的信息，请参阅 [捆绑和缩减](xref:client-side/bundling-and-minification)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-205">For information on configuring bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="9ac19-206">解决 HTTP 500 错误</span><span class="sxs-lookup"><span data-stu-id="9ac19-206">Solve HTTP 500 errors</span></span>

<span data-ttu-id="9ac19-207">有许多问题可能会导致 HTTP 500 错误消息，其中不包含问题根源的相关信息。</span><span class="sxs-lookup"><span data-stu-id="9ac19-207">There are many problems that can cause an HTTP 500 error message that contains no information on the source of the problem.</span></span> <span data-ttu-id="9ac19-208">例如，如果 *Views/_ViewImports cshtml* 文件包含项目中不存在的命名空间，则会生成 HTTP 500 错误。</span><span class="sxs-lookup"><span data-stu-id="9ac19-208">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, an HTTP 500 error is generated.</span></span> <span data-ttu-id="9ac19-209">默认情况下，在 ASP.NET Core 应用中， `UseDeveloperExceptionPage` 会将扩展添加到， `IApplicationBuilder` 并在 *开发* 环境时执行。</span><span class="sxs-lookup"><span data-stu-id="9ac19-209">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the environment is *Development* .</span></span> <span data-ttu-id="9ac19-210">下面的代码对此进行了详细说明：</span><span class="sxs-lookup"><span data-stu-id="9ac19-210">This is detailed in the following code:</span></span>

[!code-csharp[](mvc/samples/3.x/Startup.cs?highlight=17-21&name=snippet)]

<span data-ttu-id="9ac19-211">ASP.NET Core 将未经处理的异常转换为 HTTP 500 错误响应。</span><span class="sxs-lookup"><span data-stu-id="9ac19-211">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="9ac19-212">通常，这些响应中不包含错误详细信息，以防止泄露有关服务器的可能敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9ac19-212">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="9ac19-213">有关详细信息，请参阅 [开发者异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-213">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="next-steps"></a><span data-ttu-id="9ac19-214">后续步骤</span><span class="sxs-lookup"><span data-stu-id="9ac19-214">Next steps</span></span>

* <xref:migration/identity>

## <a name="additional-resources"></a><span data-ttu-id="9ac19-215">其他资源</span><span class="sxs-lookup"><span data-stu-id="9ac19-215">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="= aspnetcore-2.2"

<span data-ttu-id="9ac19-216">本文介绍如何开始将 ASP.NET MVC 项目迁移到 [ASP.NET CORE mvc](xref:mvc/overview) 2.2。</span><span class="sxs-lookup"><span data-stu-id="9ac19-216">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.2.</span></span> <span data-ttu-id="9ac19-217">在此过程中，它突出显示了从 ASP.NET MVC 中更改的许多内容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-217">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="9ac19-218">从 ASP.NET MVC 迁移是一个多步骤过程。</span><span class="sxs-lookup"><span data-stu-id="9ac19-218">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="9ac19-219">本文介绍：</span><span class="sxs-lookup"><span data-stu-id="9ac19-219">This article covers:</span></span>

* <span data-ttu-id="9ac19-220">初始设置</span><span class="sxs-lookup"><span data-stu-id="9ac19-220">Initial setup</span></span>
* <span data-ttu-id="9ac19-221">基本控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-221">Basic controllers and views</span></span>
* <span data-ttu-id="9ac19-222">静态内容</span><span class="sxs-lookup"><span data-stu-id="9ac19-222">Static content</span></span>
* <span data-ttu-id="9ac19-223">客户端依赖关系。</span><span class="sxs-lookup"><span data-stu-id="9ac19-223">Client-side dependencies.</span></span>

<span data-ttu-id="9ac19-224">有关迁移配置和 :::no-loc(Identity)::: 代码的详细，请参阅 <xref:migration/configuration> 和 <xref:migration/identity> 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-224">For migrating configuration and :::no-loc(Identity)::: code, see <xref:migration/configuration> and <xref:migration/identity>.</span></span>

> [!NOTE]
> <span data-ttu-id="9ac19-225">示例中的版本号可能不是最新的，请相应地更新项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-225">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="9ac19-226">创建 starter ASP.NET MVC 项目</span><span class="sxs-lookup"><span data-stu-id="9ac19-226">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="9ac19-227">为了演示升级，我们首先创建一个 ASP.NET MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-227">To demonstrate the upgrade, we'll start by creating an ASP.NET MVC app.</span></span> <span data-ttu-id="9ac19-228">创建名称为 *WebApp1* 的命名空间，使命名空间与下一步中创建的 ASP.NET Core 项目匹配。</span><span class="sxs-lookup"><span data-stu-id="9ac19-228">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio“新建项目”对话框](mvc/_static/new-project.png)

!["新建 Web 应用程序" 对话框：在 "ASP.NET 模板" 面板中选择的 MVC 项目模板](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="9ac19-231">*可选：* 将解决方案的名称从 *WebApp1* 更改为 *Mvc5* 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-231">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="9ac19-232">Visual Studio 将显示新的解决方案名称 ( *Mvc5* ) ，这使你可以更轻松地从下一个项目中通知此项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-232">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="9ac19-233">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="9ac19-233">Create the ASP.NET Core project</span></span>

<span data-ttu-id="9ac19-234">使用与上一个项目相同的名称创建新的 *空* ASP.NET Core web 应用 ( *WebApp1* ) ，使这两个项目中的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="9ac19-234">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="9ac19-235">通过具有相同的命名空间，可以更轻松地在两个项目之间复制代码。</span><span class="sxs-lookup"><span data-stu-id="9ac19-235">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="9ac19-236">在与前一个项目相同的目录中创建此项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-236">Create this project in a different directory than the previous project to use the same name.</span></span>

![“新建项目”对话框](mvc/_static/new_core.png)

![New ASP.NET Web 应用程序对话框：在 ASP.NET Core 模板 "面板中选择的空项目模板](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="9ac19-239">*可选：* 使用 " *Web 应用程序* " 项目模板创建新的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-239">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="9ac19-240">将项目命名为 " *WebApp1* "，并选择 **单个用户帐户** 的身份验证选项。</span><span class="sxs-lookup"><span data-stu-id="9ac19-240">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="9ac19-241">将此应用重命名为 *FullAspNetCore* 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-241">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="9ac19-242">创建此项目将在转换时节省时间。</span><span class="sxs-lookup"><span data-stu-id="9ac19-242">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="9ac19-243">可在模板生成的代码中查看最终结果，可以将代码复制到转换项目，或者将代码与模板生成的项目进行比较。</span><span class="sxs-lookup"><span data-stu-id="9ac19-243">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="9ac19-244">将站点配置为使用 MVC</span><span class="sxs-lookup"><span data-stu-id="9ac19-244">Configure the site to use MVC</span></span>

* <span data-ttu-id="9ac19-245">面向 .NET Core 时，默认情况下将引用 [AspNetCore 元包](xref:fundamentals/metapackage-app) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-245">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="9ac19-246">此包包含 MVC 应用通常使用的包。</span><span class="sxs-lookup"><span data-stu-id="9ac19-246">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="9ac19-247">如果目标 .NET Framework，则包引用必须单独列出在项目文件中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-247">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="9ac19-248">`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core MVC 框架。</span><span class="sxs-lookup"><span data-stu-id="9ac19-248">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="9ac19-249">`Microsoft.AspNetCore.StaticFiles` 为静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-249">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="9ac19-250">ASP.NET Core 应用明确选择用于中间件，如用于提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-250">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="9ac19-251">有关详细信息，请参阅[静态文件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-251">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="9ac19-252">打开 *Startup.cs* 文件并更改代码，使其与以下内容匹配：</span><span class="sxs-lookup"><span data-stu-id="9ac19-252">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="9ac19-253"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>扩展方法添加静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-253">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="9ac19-254">有关详细信息，请参阅 [应用程序启动](xref:fundamentals/startup) 和 [路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-254">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="9ac19-255">添加控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-255">Add a controller and view</span></span>

<span data-ttu-id="9ac19-256">在本部分中，将添加最小控制器和视图，作为 ASP.NET MVC 控制器的占位符和下一部分中迁移的视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-256">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="9ac19-257">添加 *控制器* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-257">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="9ac19-258">将名为 *HomeController.cs* 的 **控制器类** 添加到 *控制器* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-258">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![“添加新项”对话框](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="9ac19-260">添加 *Views* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-260">Add a *Views* directory.</span></span>

* <span data-ttu-id="9ac19-261">添加 *视图/主* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-261">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="9ac19-262">将名为 *Index* 的 **:::no-loc(Razor)::: 视图** 添加到 *Views/Home* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-262">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![“添加新项”对话框](mvc/_static/view.png)

<span data-ttu-id="9ac19-264">项目结构如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ac19-264">The project structure is shown below:</span></span>

![显示 WebApp1 的文件和目录的解决方案资源管理器](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="9ac19-266">将 Views/Home/Index.cshtml 文件的内容  替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="9ac19-266">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="9ac19-267">运行应用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-267">Run the app.</span></span>

![在 Microsoft Edge 中打开的 Web 应用](mvc/_static/hello-world.png)

<span data-ttu-id="9ac19-269">有关详细信息，请参阅 [控制器](xref:mvc/controllers/actions) 和 [视图](xref:mvc/views/overview)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-269">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="9ac19-270">以下功能需要从示例 ASP.NET MVC 项目迁移到 ASP.NET Core 项目：</span><span class="sxs-lookup"><span data-stu-id="9ac19-270">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="9ac19-271"> (CSS、字体和脚本的客户端内容) </span><span class="sxs-lookup"><span data-stu-id="9ac19-271">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="9ac19-272">控制器</span><span class="sxs-lookup"><span data-stu-id="9ac19-272">controllers</span></span>

* <span data-ttu-id="9ac19-273">视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-273">views</span></span>

* <span data-ttu-id="9ac19-274">模型</span><span class="sxs-lookup"><span data-stu-id="9ac19-274">models</span></span>

* <span data-ttu-id="9ac19-275">销售</span><span class="sxs-lookup"><span data-stu-id="9ac19-275">bundling</span></span>

* <span data-ttu-id="9ac19-276">筛选器</span><span class="sxs-lookup"><span data-stu-id="9ac19-276">filters</span></span>

* <span data-ttu-id="9ac19-277">登录/注销， :::no-loc(Identity)::: (在下一教程中完成此操作。 ) </span><span class="sxs-lookup"><span data-stu-id="9ac19-277">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="9ac19-278">控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-278">Controllers and views</span></span>

* <span data-ttu-id="9ac19-279">将 ASP.NET MVC 中的每个方法复制 `HomeController` 到新的 `HomeController` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-279">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="9ac19-280">在 ASP.NET MVC 中，内置模板的控制器操作方法返回类型为 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; 在 ASP.NET CORE mvc 中，操作方法将改为返回 `IActionResult` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-280">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="9ac19-281">`ActionResult` 实现 `IActionResult` ，因此无需更改操作方法的返回类型。</span><span class="sxs-lookup"><span data-stu-id="9ac19-281">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="9ac19-282">将 ASP.NET MVC 项目中的 *About* 、 *Contact\*\*和* :::no-loc(Razor)::: 视图文件复制到 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-282">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="9ac19-283">测试每个方法</span><span class="sxs-lookup"><span data-stu-id="9ac19-283">Test each method</span></span>

<span data-ttu-id="9ac19-284">尚未迁移布局文件和样式，因此呈现的视图仅包含视图文件中的内容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-284">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="9ac19-285">和视图的布局文件生成的 `About` 链接 `Contact` 尚不可用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-285">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

<span data-ttu-id="9ac19-286">通过将当前端口号替换为 ASP.NET core 项目中使用的端口号，从正在运行的 ASP.NET core 应用上的浏览器中调用呈现的视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-286">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="9ac19-287">例如：`https://localhost:44375/home/about`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-287">For example: `https://localhost:44375/home/about`.</span></span>

![联系人页](mvc/_static/contact-page.png)

<span data-ttu-id="9ac19-289">请注意缺少样式和菜单项。</span><span class="sxs-lookup"><span data-stu-id="9ac19-289">Note the lack of styling and menu items.</span></span> <span data-ttu-id="9ac19-290">下一节将对样式进行固定。</span><span class="sxs-lookup"><span data-stu-id="9ac19-290">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="9ac19-291">静态内容</span><span class="sxs-lookup"><span data-stu-id="9ac19-291">Static content</span></span>

<span data-ttu-id="9ac19-292">在 ASP.NET MVC 5 及更早版本中，静态内容是从 web 项目的根托管的，与服务器端文件混合。</span><span class="sxs-lookup"><span data-stu-id="9ac19-292">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="9ac19-293">在 ASP.NET Core 中，静态内容托管在 *wwwroot* 目录中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-293">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="9ac19-294">将 ASP.NET MVC 应用的静态内容复制到 ASP.NET Core 项目中的 *wwwroot* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-294">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="9ac19-295">在此示例转换中：</span><span class="sxs-lookup"><span data-stu-id="9ac19-295">In this sample conversion:</span></span>

* <span data-ttu-id="9ac19-296">将 *favicon* 文件从 ASP.NET MVC 项目复制到 ASP.NET Core 项目中的 *wwwroot* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-296">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="9ac19-297">ASP.NET MVC 项目使用 [启动](https://getbootstrap.com/) 来设置其样式，并将启动文件存储在 *Content* 和 *Scripts* 目录中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-297">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="9ac19-298">生成 ASP.NET MVC 项目的模板引用布局文件中的启动 ( *Views/Shared/_Layout* ) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-298">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="9ac19-299">*bootstrap.js* 和 *启动 .css* 文件可从 ASP.NET MVC 项目复制到新项目中的 *wwwroot* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-299">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="9ac19-300">本文档改为使用 Cdn 在下一节中添加对) 使用的启动 (和其他客户端库的支持。</span><span class="sxs-lookup"><span data-stu-id="9ac19-300">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="9ac19-301">迁移布局文件</span><span class="sxs-lookup"><span data-stu-id="9ac19-301">Migrate the layout file</span></span>

* <span data-ttu-id="9ac19-302">将 *_ViewStart* 的 ASP.NET 文件从 MVC 项目的 " *视图* " 目录复制到 ASP.NET Core 项目的 " *视图* " 目录中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-302">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="9ac19-303">未在 ASP.NET Core MVC 中更改 *_ViewStart cshtml* 文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-303">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="9ac19-304">创建 *视图/共享* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-304">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="9ac19-305">*可选：* 将 *FullAspNetCore* MVC 项目的 *views* 目录中 *_ViewImports* 为 ASP.NET Core 项目的 *视图* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-305">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="9ac19-306">删除 *_ViewImports cshtml* 文件中的任何命名空间声明。</span><span class="sxs-lookup"><span data-stu-id="9ac19-306">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9ac19-307">*_ViewImports 的 cshtml* 文件提供所有视图文件的命名空间，并提供 [标记帮助](xref:mvc/views/tag-helpers/intro)程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-307">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="9ac19-308">标记帮助程序在新的布局文件中使用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-308">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="9ac19-309">*_ViewImports* ASP.NET Core 的新文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-309">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="9ac19-310">将 *_Layout* 的 ASP.NET 文件从 MVC 项目的 *视图/共享* 目录复制到 ASP.NET Core 项目的 " *视图"/"共享* 目录" 中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-310">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="9ac19-311">打开 *_Layout.* # 文件，并进行以下更改 (完成的代码如下所示) ：</span><span class="sxs-lookup"><span data-stu-id="9ac19-311">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="9ac19-312">替换为 `@Styles.Render("~/Content/css")` `<link>` 加载 *启动 .css* 的元素 (参阅下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-312">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="9ac19-313">删除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-313">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="9ac19-314">注释掉 `@Html.Partial("_LoginPartial")`) 行 (环绕行的行 `@*...*@` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-314">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="9ac19-315">有关详细信息，请参阅 [迁移身份验证和 :::no-loc(Identity)::: ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="9ac19-315">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="9ac19-316">替换 `@Scripts.Render("~/bundles/jquery")` 为 `<script>` 元素 (参见下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-316">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="9ac19-317">替换 `@Scripts.Render("~/bundles/bootstrap")` 为 `<script>` 元素 (参见下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-317">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="9ac19-318">用于启动 CSS 的替换标记包含：</span><span class="sxs-lookup"><span data-stu-id="9ac19-318">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="9ac19-319">JQuery 和启动 JavaScript 的替换标记包含：</span><span class="sxs-lookup"><span data-stu-id="9ac19-319">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="9ac19-320">更新后的 *_Layout cshtml* 文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ac19-320">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="9ac19-321">在浏览器中查看站点。</span><span class="sxs-lookup"><span data-stu-id="9ac19-321">View the site in the browser.</span></span> <span data-ttu-id="9ac19-322">它现在应正确加载，并具有所需的样式。</span><span class="sxs-lookup"><span data-stu-id="9ac19-322">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="9ac19-323">*可选：* 尝试使用新的布局文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-323">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="9ac19-324">复制 *FullAspNetCore* 项目中的布局文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-324">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="9ac19-325">新的布局文件使用 [标记帮助](xref:mvc/views/tag-helpers/intro) 程序，并具有其他改进功能。</span><span class="sxs-lookup"><span data-stu-id="9ac19-325">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="9ac19-326">配置捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="9ac19-326">Configure bundling and minification</span></span>

<span data-ttu-id="9ac19-327">有关如何配置绑定和缩减的信息，请参阅 [捆绑和缩减](xref:client-side/bundling-and-minification)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-327">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="9ac19-328">解决 HTTP 500 错误</span><span class="sxs-lookup"><span data-stu-id="9ac19-328">Solve HTTP 500 errors</span></span>

<span data-ttu-id="9ac19-329">有许多问题可能会导致 HTTP 500 错误消息，这些错误消息不包含问题根源的相关信息。</span><span class="sxs-lookup"><span data-stu-id="9ac19-329">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="9ac19-330">例如，如果 *Views/_ViewImports cshtml* 文件包含项目中不存在的命名空间，则会生成 HTTP 500 错误。</span><span class="sxs-lookup"><span data-stu-id="9ac19-330">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="9ac19-331">默认情况下，在 ASP.NET Core 应用中，会将 `UseDeveloperExceptionPage` 扩展添加到， `IApplicationBuilder` 并在配置为 *开发* 时执行。</span><span class="sxs-lookup"><span data-stu-id="9ac19-331">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="9ac19-332">请参阅以下代码中的示例：</span><span class="sxs-lookup"><span data-stu-id="9ac19-332">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="9ac19-333">ASP.NET Core 将未经处理的异常转换为 HTTP 500 错误响应。</span><span class="sxs-lookup"><span data-stu-id="9ac19-333">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="9ac19-334">通常，这些响应中不包含错误详细信息，以防止泄露有关服务器的可能敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9ac19-334">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="9ac19-335">有关详细信息，请参阅 [开发者异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-335">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ac19-336">其他资源</span><span class="sxs-lookup"><span data-stu-id="9ac19-336">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end

::: moniker range="<= aspnetcore-2.1"

<span data-ttu-id="9ac19-337">本文介绍如何开始将 ASP.NET MVC 项目迁移到 [ASP.NET CORE mvc](xref:mvc/overview) 2.1。</span><span class="sxs-lookup"><span data-stu-id="9ac19-337">This article shows how to start migrating an ASP.NET MVC project to [ASP.NET Core MVC](xref:mvc/overview) 2.1.</span></span> <span data-ttu-id="9ac19-338">在此过程中，它突出显示了从 ASP.NET MVC 中更改的许多内容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-338">In the process, it highlights many of the things that have changed from ASP.NET MVC.</span></span> <span data-ttu-id="9ac19-339">从 ASP.NET MVC 迁移是一个多步骤过程。</span><span class="sxs-lookup"><span data-stu-id="9ac19-339">Migrating from ASP.NET MVC is a multi-step process.</span></span> <span data-ttu-id="9ac19-340">本文介绍：</span><span class="sxs-lookup"><span data-stu-id="9ac19-340">This article covers:</span></span>

* <span data-ttu-id="9ac19-341">初始设置</span><span class="sxs-lookup"><span data-stu-id="9ac19-341">Initial setup</span></span>
* <span data-ttu-id="9ac19-342">基本控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-342">Basic controllers and views</span></span>
* <span data-ttu-id="9ac19-343">静态内容</span><span class="sxs-lookup"><span data-stu-id="9ac19-343">Static content</span></span>
* <span data-ttu-id="9ac19-344">客户端依赖关系。</span><span class="sxs-lookup"><span data-stu-id="9ac19-344">Client-side dependencies.</span></span>

<span data-ttu-id="9ac19-345">若要迁移配置和 :::no-loc(Identity)::: 代码，请参阅将 [配置迁移到 ASP.NET Core](xref:migration/configuration) 并 [迁移身份验证和 :::no-loc(Identity)::: ASP.NET Core](xref:migration/identity)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-345">For migrating configuration and :::no-loc(Identity)::: code, see [Migrate configuration to ASP.NET Core](xref:migration/configuration) and [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity).</span></span>

> [!NOTE]
> <span data-ttu-id="9ac19-346">示例中的版本号可能不是最新的，请相应地更新项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-346">The version numbers in the samples might not be current, update the projects accordingly.</span></span>

## <a name="create-the-starter-aspnet-mvc-project"></a><span data-ttu-id="9ac19-347">创建 starter ASP.NET MVC 项目</span><span class="sxs-lookup"><span data-stu-id="9ac19-347">Create the starter ASP.NET MVC project</span></span>

<span data-ttu-id="9ac19-348">为了演示升级，我们首先创建一个 ASP.NET MVC 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-348">To demonstrate the upgrade, we'll start by creating a ASP.NET MVC app.</span></span> <span data-ttu-id="9ac19-349">创建名称为 *WebApp1* 的命名空间，使命名空间与下一步中创建的 ASP.NET Core 项目匹配。</span><span class="sxs-lookup"><span data-stu-id="9ac19-349">Create it with the name *WebApp1* so the namespace matches the ASP.NET Core project created in the next step.</span></span>

![Visual Studio“新建项目”对话框](mvc/_static/new-project.png)

!["新建 Web 应用程序" 对话框：在 "ASP.NET 模板" 面板中选择的 MVC 项目模板](mvc/_static/new-project-select-mvc-template.png)

<span data-ttu-id="9ac19-352">*可选：* 将解决方案的名称从 *WebApp1* 更改为 *Mvc5* 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-352">*Optional:* Change the name of the Solution from *WebApp1* to *Mvc5* .</span></span> <span data-ttu-id="9ac19-353">Visual Studio 将显示新的解决方案名称 ( *Mvc5* ) ，这使你可以更轻松地从下一个项目中通知此项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-353">Visual Studio displays the new solution name ( *Mvc5* ), which makes it easier to tell this project from the next project.</span></span>

## <a name="create-the-aspnet-core-project"></a><span data-ttu-id="9ac19-354">创建 ASP.NET Core 项目</span><span class="sxs-lookup"><span data-stu-id="9ac19-354">Create the ASP.NET Core project</span></span>

<span data-ttu-id="9ac19-355">使用与上一个项目相同的名称创建新的 *空* ASP.NET Core web 应用 ( *WebApp1* ) ，使这两个项目中的命名空间匹配。</span><span class="sxs-lookup"><span data-stu-id="9ac19-355">Create a new *empty* ASP.NET Core web app with the same name as the previous project ( *WebApp1* ) so the namespaces in the two projects match.</span></span> <span data-ttu-id="9ac19-356">通过具有相同的命名空间，可以更轻松地在两个项目之间复制代码。</span><span class="sxs-lookup"><span data-stu-id="9ac19-356">Having the same namespace makes it easier to copy code between the two projects.</span></span> <span data-ttu-id="9ac19-357">在与前一个项目相同的目录中创建此项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-357">Create this project in a different directory than the previous project to use the same name.</span></span>

![“新建项目”对话框](mvc/_static/new_core.png)

![New ASP.NET Web 应用程序对话框：在 ASP.NET Core 模板 "面板中选择的空项目模板](mvc/_static/new-project-select-empty-aspnet5-template.png)

* <span data-ttu-id="9ac19-360">*可选：* 使用 " *Web 应用程序* " 项目模板创建新的 ASP.NET Core 应用程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-360">*Optional:* Create a new ASP.NET Core app using the *Web Application* project template.</span></span> <span data-ttu-id="9ac19-361">将项目命名为 " *WebApp1* "，并选择 **单个用户帐户** 的身份验证选项。</span><span class="sxs-lookup"><span data-stu-id="9ac19-361">Name the project *WebApp1* , and select an authentication option of **Individual User Accounts** .</span></span> <span data-ttu-id="9ac19-362">将此应用重命名为 *FullAspNetCore* 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-362">Rename this app to *FullAspNetCore* .</span></span> <span data-ttu-id="9ac19-363">创建此项目将在转换时节省时间。</span><span class="sxs-lookup"><span data-stu-id="9ac19-363">Creating this project saves time in the conversion.</span></span> <span data-ttu-id="9ac19-364">可在模板生成的代码中查看最终结果，可以将代码复制到转换项目，或者将代码与模板生成的项目进行比较。</span><span class="sxs-lookup"><span data-stu-id="9ac19-364">The end result can be viewed in the template-generated code, code can be copied to the conversion project, or compared with the template-generated project.</span></span>

## <a name="configure-the-site-to-use-mvc"></a><span data-ttu-id="9ac19-365">将站点配置为使用 MVC</span><span class="sxs-lookup"><span data-stu-id="9ac19-365">Configure the site to use MVC</span></span>

* <span data-ttu-id="9ac19-366">面向 .NET Core 时，默认情况下将引用 [AspNetCore 元包](xref:fundamentals/metapackage-app) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-366">When targeting .NET Core, the [Microsoft.AspNetCore.App metapackage](xref:fundamentals/metapackage-app) is referenced by default.</span></span> <span data-ttu-id="9ac19-367">此包包含 MVC 应用通常使用的包。</span><span class="sxs-lookup"><span data-stu-id="9ac19-367">This package contains packages commonly used by MVC apps.</span></span> <span data-ttu-id="9ac19-368">如果目标 .NET Framework，则包引用必须单独列出在项目文件中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-368">If targeting .NET Framework, package references must be listed individually in the project file.</span></span>

<span data-ttu-id="9ac19-369">`Microsoft.AspNetCore.Mvc` 是 ASP.NET Core MVC 框架。</span><span class="sxs-lookup"><span data-stu-id="9ac19-369">`Microsoft.AspNetCore.Mvc` is the ASP.NET Core MVC framework.</span></span> <span data-ttu-id="9ac19-370">`Microsoft.AspNetCore.StaticFiles` 为静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-370">`Microsoft.AspNetCore.StaticFiles` is the static file handler.</span></span> <span data-ttu-id="9ac19-371">ASP.NET Core 应用明确选择用于中间件，如用于提供静态文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-371">ASP.NET Core apps explicitly opt in for middleware, such as for serving static files.</span></span> <span data-ttu-id="9ac19-372">有关详细信息，请参阅[静态文件](xref:fundamentals/static-files)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-372">For more information, see [Static files](xref:fundamentals/static-files).</span></span>

* <span data-ttu-id="9ac19-373">打开 *Startup.cs* 文件并更改代码，使其与以下内容匹配：</span><span class="sxs-lookup"><span data-stu-id="9ac19-373">Open the *Startup.cs* file and change the code to match the following:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=7,20-25&name=snippet)]

<span data-ttu-id="9ac19-374"><xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*>扩展方法添加静态文件处理程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-374">The <xref:Microsoft.AspNetCore.Builder.StaticFileExtensions.UseStaticFiles*> extension method adds the static file handler.</span></span> <span data-ttu-id="9ac19-375">`UseMvc`扩展方法将添加路由。</span><span class="sxs-lookup"><span data-stu-id="9ac19-375">The `UseMvc` extension method adds routing.</span></span> <span data-ttu-id="9ac19-376">有关详细信息，请参阅 [应用程序启动](xref:fundamentals/startup) 和 [路由](xref:fundamentals/routing)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-376">For more information, see [Application Startup](xref:fundamentals/startup) and [Routing](xref:fundamentals/routing).</span></span>

## <a name="add-a-controller-and-view"></a><span data-ttu-id="9ac19-377">添加控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-377">Add a controller and view</span></span>

<span data-ttu-id="9ac19-378">在本部分中，将添加最小控制器和视图，作为 ASP.NET MVC 控制器的占位符和下一部分中迁移的视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-378">In this section, a minimal controller and view are added to serve as placeholders for the ASP.NET MVC controller and views migrated in the next section.</span></span>

* <span data-ttu-id="9ac19-379">添加 *控制器* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-379">Add a *Controllers* directory.</span></span>

* <span data-ttu-id="9ac19-380">将名为 *HomeController.cs* 的 **控制器类** 添加到 *控制器* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-380">Add a **Controller Class** named *HomeController.cs* to the *Controllers* directory.</span></span>

![“添加新项”对话框](mvc/_static/add_mvc_ctl.png)

* <span data-ttu-id="9ac19-382">添加 *Views* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-382">Add a *Views* directory.</span></span>

* <span data-ttu-id="9ac19-383">添加 *视图/主* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-383">Add a *Views/Home* directory.</span></span>

* <span data-ttu-id="9ac19-384">将名为 *Index* 的 **:::no-loc(Razor)::: 视图** 添加到 *Views/Home* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-384">Add a **:::no-loc(Razor)::: View** named *Index.cshtml* to the *Views/Home* directory.</span></span>

![“添加新项”对话框](mvc/_static/view.png)

<span data-ttu-id="9ac19-386">项目结构如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ac19-386">The project structure is shown below:</span></span>

![显示 WebApp1 的文件和目录的解决方案资源管理器](mvc/_static/project-structure-controller-view.png)

<span data-ttu-id="9ac19-388">将 Views/Home/Index.cshtml 文件的内容  替换为以下标记：</span><span class="sxs-lookup"><span data-stu-id="9ac19-388">Replace the contents of the *Views/Home/Index.cshtml* file with the following markup:</span></span>

```html
<h1>Hello world!</h1>
```

<span data-ttu-id="9ac19-389">运行应用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-389">Run the app.</span></span>

![在 Microsoft Edge 中打开的 Web 应用](mvc/_static/hello-world.png)

<span data-ttu-id="9ac19-391">有关详细信息，请参阅 [控制器](xref:mvc/controllers/actions) 和 [视图](xref:mvc/views/overview)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-391">For more information, see [Controllers](xref:mvc/controllers/actions) and [Views](xref:mvc/views/overview).</span></span>

<span data-ttu-id="9ac19-392">以下功能需要从示例 ASP.NET MVC 项目迁移到 ASP.NET Core 项目：</span><span class="sxs-lookup"><span data-stu-id="9ac19-392">The following functionality requires migration from the example ASP.NET MVC project to the ASP.NET Core project:</span></span>

* <span data-ttu-id="9ac19-393"> (CSS、字体和脚本的客户端内容) </span><span class="sxs-lookup"><span data-stu-id="9ac19-393">client-side content (CSS, fonts, and scripts)</span></span>

* <span data-ttu-id="9ac19-394">控制器</span><span class="sxs-lookup"><span data-stu-id="9ac19-394">controllers</span></span>

* <span data-ttu-id="9ac19-395">视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-395">views</span></span>

* <span data-ttu-id="9ac19-396">模型</span><span class="sxs-lookup"><span data-stu-id="9ac19-396">models</span></span>

* <span data-ttu-id="9ac19-397">销售</span><span class="sxs-lookup"><span data-stu-id="9ac19-397">bundling</span></span>

* <span data-ttu-id="9ac19-398">筛选器</span><span class="sxs-lookup"><span data-stu-id="9ac19-398">filters</span></span>

* <span data-ttu-id="9ac19-399">登录/注销， :::no-loc(Identity)::: (在下一教程中完成此操作。 ) </span><span class="sxs-lookup"><span data-stu-id="9ac19-399">Log in/out, :::no-loc(Identity)::: (This is done in the next tutorial.)</span></span>

## <a name="controllers-and-views"></a><span data-ttu-id="9ac19-400">控制器和视图</span><span class="sxs-lookup"><span data-stu-id="9ac19-400">Controllers and views</span></span>

* <span data-ttu-id="9ac19-401">将 ASP.NET MVC 中的每个方法复制 `HomeController` 到新的 `HomeController` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-401">Copy each of the methods from the ASP.NET MVC `HomeController` to the new `HomeController`.</span></span> <span data-ttu-id="9ac19-402">在 ASP.NET MVC 中，内置模板的控制器操作方法返回类型为 <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2> ; 在 ASP.NET CORE mvc 中，操作方法将改为返回 `IActionResult` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-402">In ASP.NET MVC, the built-in template's controller action method return type is <https://docs.microsoft.com/dotnet/api/system.web.mvc.actionresult?view=aspnet-mvc-5.2>; in ASP.NET Core MVC, the action methods return `IActionResult` instead.</span></span> <span data-ttu-id="9ac19-403">`ActionResult` 实现 `IActionResult` ，因此无需更改操作方法的返回类型。</span><span class="sxs-lookup"><span data-stu-id="9ac19-403">`ActionResult` implements `IActionResult`, so there's no need to change the return type of the action methods.</span></span>

* <span data-ttu-id="9ac19-404">将 ASP.NET MVC 项目中的 *About* 、 *Contact\*\*和* :::no-loc(Razor)::: 视图文件复制到 ASP.NET Core 项目。</span><span class="sxs-lookup"><span data-stu-id="9ac19-404">Copy the *About.cshtml* , *Contact.cshtml* , and *Index.cshtml* :::no-loc(Razor)::: view files from the ASP.NET MVC project to the ASP.NET Core project.</span></span>

## <a name="test-each-method"></a><span data-ttu-id="9ac19-405">测试每个方法</span><span class="sxs-lookup"><span data-stu-id="9ac19-405">Test each method</span></span>

<span data-ttu-id="9ac19-406">尚未迁移布局文件和样式，因此呈现的视图仅包含视图文件中的内容。</span><span class="sxs-lookup"><span data-stu-id="9ac19-406">The layout file and styles have not been migrated yet, so the rendered views only contain the content in the view files.</span></span> <span data-ttu-id="9ac19-407">和视图的布局文件生成的 `About` 链接 `Contact` 尚不可用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-407">The layout file generated links for the `About` and `Contact` views will not be available yet.</span></span>

* <span data-ttu-id="9ac19-408">通过将当前端口号替换为 ASP.NET core 项目中使用的端口号，从正在运行的 ASP.NET core 应用上的浏览器中调用呈现的视图。</span><span class="sxs-lookup"><span data-stu-id="9ac19-408">Invoke the rendered views from the browser on the running ASP.NET core app by replacing the current port number with the port number used in the ASP.NET core project.</span></span> <span data-ttu-id="9ac19-409">例如：`https://localhost:44375/home/about`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-409">For example: `https://localhost:44375/home/about`.</span></span>

![联系人页](mvc/_static/contact-page.png)

<span data-ttu-id="9ac19-411">请注意缺少样式和菜单项。</span><span class="sxs-lookup"><span data-stu-id="9ac19-411">Note the lack of styling and menu items.</span></span> <span data-ttu-id="9ac19-412">下一节将对样式进行固定。</span><span class="sxs-lookup"><span data-stu-id="9ac19-412">The styling will be fixed in the next section.</span></span>

## <a name="static-content"></a><span data-ttu-id="9ac19-413">静态内容</span><span class="sxs-lookup"><span data-stu-id="9ac19-413">Static content</span></span>

<span data-ttu-id="9ac19-414">在 ASP.NET MVC 5 及更早版本中，静态内容是从 web 项目的根托管的，与服务器端文件混合。</span><span class="sxs-lookup"><span data-stu-id="9ac19-414">In ASP.NET MVC 5 and earlier, static content was hosted from the root of the web project and was intermixed with server-side files.</span></span> <span data-ttu-id="9ac19-415">在 ASP.NET Core 中，静态内容托管在 *wwwroot* 目录中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-415">In ASP.NET Core, static content is hosted in the *wwwroot* directory.</span></span> <span data-ttu-id="9ac19-416">将 ASP.NET MVC 应用的静态内容复制到 ASP.NET Core 项目中的 *wwwroot* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-416">Copy the static content from the ASP.NET MVC app to the *wwwroot* directory in the ASP.NET Core project.</span></span> <span data-ttu-id="9ac19-417">在此示例转换中：</span><span class="sxs-lookup"><span data-stu-id="9ac19-417">In this sample conversion:</span></span>

* <span data-ttu-id="9ac19-418">将 *favicon* 文件从 ASP.NET MVC 项目复制到 ASP.NET Core 项目中的 *wwwroot* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-418">Copy the *favicon.ico* file from the ASP.NET MVC project to the *wwwroot* directory in the ASP.NET Core project.</span></span>

<span data-ttu-id="9ac19-419">ASP.NET MVC 项目使用 [启动](https://getbootstrap.com/) 来设置其样式，并将启动文件存储在 *Content* 和 *Scripts* 目录中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-419">The ASP.NET MVC project uses [Bootstrap](https://getbootstrap.com/) for its styling and stores the Bootstrap files in the *Content* and *Scripts* directories.</span></span> <span data-ttu-id="9ac19-420">生成 ASP.NET MVC 项目的模板引用布局文件中的启动 ( *Views/Shared/_Layout* ) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-420">The template, which generated the ASP.NET MVC project, references Bootstrap in the layout file ( *Views/Shared/_Layout.cshtml* ).</span></span> <span data-ttu-id="9ac19-421">*bootstrap.js* 和 *启动 .css* 文件可从 ASP.NET MVC 项目复制到新项目中的 *wwwroot* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-421">The *bootstrap.js* and *bootstrap.css* files could be copied from the ASP.NET MVC project to the *wwwroot* directory in the new project.</span></span> <span data-ttu-id="9ac19-422">本文档改为使用 Cdn 在下一节中添加对) 使用的启动 (和其他客户端库的支持。</span><span class="sxs-lookup"><span data-stu-id="9ac19-422">Instead, this document adds support for Bootstrap (and other client-side libraries) using CDNs, in the next section.</span></span>

## <a name="migrate-the-layout-file"></a><span data-ttu-id="9ac19-423">迁移布局文件</span><span class="sxs-lookup"><span data-stu-id="9ac19-423">Migrate the layout file</span></span>

* <span data-ttu-id="9ac19-424">将 *_ViewStart* 的 ASP.NET 文件从 MVC 项目的 " *视图* " 目录复制到 ASP.NET Core 项目的 " *视图* " 目录中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-424">Copy the *_ViewStart.cshtml* file from the ASP.NET MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="9ac19-425">未在 ASP.NET Core MVC 中更改 *_ViewStart cshtml* 文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-425">The *_ViewStart.cshtml* file has not changed in ASP.NET Core MVC.</span></span>

* <span data-ttu-id="9ac19-426">创建 *视图/共享* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-426">Create a *Views/Shared* directory.</span></span>

* <span data-ttu-id="9ac19-427">*可选：* 将 *FullAspNetCore* MVC 项目的 *views* 目录中 *_ViewImports* 为 ASP.NET Core 项目的 *视图* 目录。</span><span class="sxs-lookup"><span data-stu-id="9ac19-427">*Optional:* Copy *_ViewImports.cshtml* from the *FullAspNetCore* MVC project's *Views* directory into the ASP.NET Core project's *Views* directory.</span></span> <span data-ttu-id="9ac19-428">删除 *_ViewImports cshtml* 文件中的任何命名空间声明。</span><span class="sxs-lookup"><span data-stu-id="9ac19-428">Remove any namespace declaration in the *_ViewImports.cshtml* file.</span></span> <span data-ttu-id="9ac19-429">*_ViewImports 的 cshtml* 文件提供所有视图文件的命名空间，并提供 [标记帮助](xref:mvc/views/tag-helpers/intro)程序。</span><span class="sxs-lookup"><span data-stu-id="9ac19-429">The *_ViewImports.cshtml* file provides namespaces for all the view files and brings in [Tag Helpers](xref:mvc/views/tag-helpers/intro).</span></span> <span data-ttu-id="9ac19-430">标记帮助程序在新的布局文件中使用。</span><span class="sxs-lookup"><span data-stu-id="9ac19-430">Tag Helpers are used in the new layout file.</span></span> <span data-ttu-id="9ac19-431">*_ViewImports* ASP.NET Core 的新文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-431">The *_ViewImports.cshtml* file is new for ASP.NET Core.</span></span>

* <span data-ttu-id="9ac19-432">将 *_Layout* 的 ASP.NET 文件从 MVC 项目的 *视图/共享* 目录复制到 ASP.NET Core 项目的 " *视图"/"共享* 目录" 中。</span><span class="sxs-lookup"><span data-stu-id="9ac19-432">Copy the *_Layout.cshtml* file from the ASP.NET MVC project's *Views/Shared* directory into the ASP.NET Core project's *Views/Shared* directory.</span></span>

<span data-ttu-id="9ac19-433">打开 *_Layout.* # 文件，并进行以下更改 (完成的代码如下所示) ：</span><span class="sxs-lookup"><span data-stu-id="9ac19-433">Open *_Layout.cshtml* file and make the following changes (the completed code is shown below):</span></span>

* <span data-ttu-id="9ac19-434">替换为 `@Styles.Render("~/Content/css")` `<link>` 加载 *启动 .css* 的元素 (参阅下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-434">Replace `@Styles.Render("~/Content/css")` with a `<link>` element to load *bootstrap.css* (see below).</span></span>

* <span data-ttu-id="9ac19-435">删除 `@Scripts.Render("~/bundles/modernizr")`。</span><span class="sxs-lookup"><span data-stu-id="9ac19-435">Remove `@Scripts.Render("~/bundles/modernizr")`.</span></span>

* <span data-ttu-id="9ac19-436">注释掉 `@Html.Partial("_LoginPartial")`) 行 (环绕行的行 `@*...*@` 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-436">Comment out the `@Html.Partial("_LoginPartial")` line (surround the line with `@*...*@`).</span></span> <span data-ttu-id="9ac19-437">有关详细信息，请参阅 [迁移身份验证和 :::no-loc(Identity)::: ASP.NET Core](xref:migration/identity)</span><span class="sxs-lookup"><span data-stu-id="9ac19-437">For more information, see [Migrate Authentication and :::no-loc(Identity)::: to ASP.NET Core](xref:migration/identity)</span></span>

* <span data-ttu-id="9ac19-438">替换 `@Scripts.Render("~/bundles/jquery")` 为 `<script>` 元素 (参见下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-438">Replace `@Scripts.Render("~/bundles/jquery")` with a `<script>` element (see below).</span></span>

* <span data-ttu-id="9ac19-439">替换 `@Scripts.Render("~/bundles/bootstrap")` 为 `<script>` 元素 (参见下面的) 。</span><span class="sxs-lookup"><span data-stu-id="9ac19-439">Replace `@Scripts.Render("~/bundles/bootstrap")` with a `<script>` element (see below).</span></span>

<span data-ttu-id="9ac19-440">用于启动 CSS 的替换标记包含：</span><span class="sxs-lookup"><span data-stu-id="9ac19-440">The replacement markup for Bootstrap CSS inclusion:</span></span>

```html
<link rel="stylesheet"
    href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css"
    integrity="sha384-BVYiiSIFeK1dGmJRAkycuHAHRg32OmUcww7on3RYdg4Va+PmSTsz/K68vbdEjh4u"
    crossorigin="anonymous">
```

<span data-ttu-id="9ac19-441">JQuery 和启动 JavaScript 的替换标记包含：</span><span class="sxs-lookup"><span data-stu-id="9ac19-441">The replacement markup for jQuery and Bootstrap JavaScript inclusion:</span></span>

```html
<script src="https://code.jquery.com/jquery-3.3.1.min.js"></script>
<script src="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/js/bootstrap.min.js"
    integrity="sha384-Tc5IQib027qvyjSMfHjOMaLkfuWVxZxUPnCJA7l2mCWNIpG9mGCD8wGNIcPD7Txa" crossorigin="anonymous"></script>
```

<span data-ttu-id="9ac19-442">更新后的 *_Layout cshtml* 文件如下所示：</span><span class="sxs-lookup"><span data-stu-id="9ac19-442">The updated *_Layout.cshtml* file is shown below:</span></span>

[!code-cshtml[](mvc/samples/2.x/Views/Shared/_Layout.cshtml?highlight=7-10,29,41-44)]

<span data-ttu-id="9ac19-443">在浏览器中查看站点。</span><span class="sxs-lookup"><span data-stu-id="9ac19-443">View the site in the browser.</span></span> <span data-ttu-id="9ac19-444">它现在应正确加载，并具有所需的样式。</span><span class="sxs-lookup"><span data-stu-id="9ac19-444">It should now load correctly, with the expected styles in place.</span></span>

* <span data-ttu-id="9ac19-445">*可选：* 尝试使用新的布局文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-445">*Optional:* Try using the new layout file.</span></span> <span data-ttu-id="9ac19-446">复制 *FullAspNetCore* 项目中的布局文件。</span><span class="sxs-lookup"><span data-stu-id="9ac19-446">Copy the layout file from the *FullAspNetCore* project.</span></span> <span data-ttu-id="9ac19-447">新的布局文件使用 [标记帮助](xref:mvc/views/tag-helpers/intro) 程序，并具有其他改进功能。</span><span class="sxs-lookup"><span data-stu-id="9ac19-447">The new layout file uses [Tag Helpers](xref:mvc/views/tag-helpers/intro) and has other improvements.</span></span>

## <a name="configure-bundling-and-minification"></a><span data-ttu-id="9ac19-448">配置捆绑和缩小</span><span class="sxs-lookup"><span data-stu-id="9ac19-448">Configure bundling and minification</span></span>

<span data-ttu-id="9ac19-449">有关如何配置绑定和缩减的信息，请参阅 [捆绑和缩减](xref:client-side/bundling-and-minification)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-449">For information about how to configure bundling and minification, see [Bundling and Minification](xref:client-side/bundling-and-minification).</span></span>

## <a name="solve-http-500-errors"></a><span data-ttu-id="9ac19-450">解决 HTTP 500 错误</span><span class="sxs-lookup"><span data-stu-id="9ac19-450">Solve HTTP 500 errors</span></span>

<span data-ttu-id="9ac19-451">有许多问题可能会导致 HTTP 500 错误消息，这些错误消息不包含问题根源的相关信息。</span><span class="sxs-lookup"><span data-stu-id="9ac19-451">There are many problems that can cause an HTTP 500 error messages that contain no information on the source of the problem.</span></span> <span data-ttu-id="9ac19-452">例如，如果 *Views/_ViewImports cshtml* 文件包含项目中不存在的命名空间，则会生成 HTTP 500 错误。</span><span class="sxs-lookup"><span data-stu-id="9ac19-452">For example, if the *Views/_ViewImports.cshtml* file contains a namespace that doesn't exist in the project, a HTTP 500 error is generated.</span></span> <span data-ttu-id="9ac19-453">默认情况下，在 ASP.NET Core 应用中，会将 `UseDeveloperExceptionPage` 扩展添加到， `IApplicationBuilder` 并在配置为 *开发* 时执行。</span><span class="sxs-lookup"><span data-stu-id="9ac19-453">By default in ASP.NET Core apps, the `UseDeveloperExceptionPage` extension is added to the `IApplicationBuilder` and executed when the configuration is *Development* .</span></span> <span data-ttu-id="9ac19-454">请参阅以下代码中的示例：</span><span class="sxs-lookup"><span data-stu-id="9ac19-454">See an example in the following code:</span></span>

[!code-csharp[](mvc/samples/2.x/Startup.cs?highlight=11-15&name=snippet)]

<span data-ttu-id="9ac19-455">ASP.NET Core 将未经处理的异常转换为 HTTP 500 错误响应。</span><span class="sxs-lookup"><span data-stu-id="9ac19-455">ASP.NET Core converts unhandled exceptions into HTTP 500 error responses.</span></span> <span data-ttu-id="9ac19-456">通常，这些响应中不包含错误详细信息，以防止泄露有关服务器的可能敏感信息。</span><span class="sxs-lookup"><span data-stu-id="9ac19-456">Normally, error details aren't included in these responses to prevent disclosure of potentially sensitive information about the server.</span></span> <span data-ttu-id="9ac19-457">有关详细信息，请参阅 [开发者异常页](xref:fundamentals/error-handling#developer-exception-page)。</span><span class="sxs-lookup"><span data-stu-id="9ac19-457">For more information, see [Developer Exception Page](xref:fundamentals/error-handling#developer-exception-page).</span></span>

## <a name="additional-resources"></a><span data-ttu-id="9ac19-458">其他资源</span><span class="sxs-lookup"><span data-stu-id="9ac19-458">Additional resources</span></span>

* <xref:blazor/index>
* <xref:mvc/views/tag-helpers/intro>

::: moniker-end
