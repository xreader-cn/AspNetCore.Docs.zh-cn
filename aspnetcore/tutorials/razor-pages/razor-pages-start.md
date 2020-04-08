---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 6e1d58ccd83f7d7c1083dc2bf9ce7476650812a1
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "78646902"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="7083c-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="7083c-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="7083c-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7083c-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="7083c-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="7083c-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="7083c-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="7083c-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7083c-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7083c-109">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="7083c-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-110">Run the app.</span></span>
> * <span data-ttu-id="7083c-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="7083c-111">Examine the project files.</span></span>

<span data-ttu-id="7083c-112">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="7083c-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="7083c-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="7083c-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7083c-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7083c-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7083c-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7083c-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7083c-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7083c-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="7083c-118">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="7083c-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7083c-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7083c-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7083c-120">从 Visual Studio“文件”  菜单中选择“新建”  >“项目”  。</span><span class="sxs-lookup"><span data-stu-id="7083c-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="7083c-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="7083c-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="7083c-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="7083c-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="7083c-123">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="7083c-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="7083c-124">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="7083c-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="7083c-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="7083c-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="7083c-126">在下拉列表中选择“ASP.NET Core 3.1”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="7083c-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="7083c-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="7083c-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7083c-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7083c-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7083c-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="7083c-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="7083c-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="7083c-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="7083c-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7083c-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="7083c-134">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="7083c-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="7083c-135">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="7083c-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="7083c-136">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="7083c-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="7083c-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="7083c-137">Select **Yes**.</span></span>

  <span data-ttu-id="7083c-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="7083c-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7083c-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7083c-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7083c-140">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="7083c-140">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="7083c-142">选择“.NET Core”  >“应用”  >“Web 应用程序”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="7083c-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS“新建项目”对话框](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="7083c-144">在“配置新的 Web 应用程序”对话框中，将目标框架设置为“.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="7083c-144">In the **Configure your new Web Application** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 选择](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="7083c-146">将项目命名为“RazorPagesMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="7083c-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="7083c-148">运行应用</span><span class="sxs-lookup"><span data-stu-id="7083c-148">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="7083c-149">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="7083c-149">Examine the project files</span></span>

<span data-ttu-id="7083c-150">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="7083c-150">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="7083c-151">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="7083c-151">Pages folder</span></span>

<span data-ttu-id="7083c-152">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="7083c-152">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="7083c-153">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="7083c-153">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="7083c-154">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="7083c-154">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="7083c-155">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="7083c-155">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="7083c-156">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="7083c-156">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="7083c-157">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="7083c-157">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="7083c-158">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="7083c-158">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="7083c-159">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="7083c-159">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="7083c-160">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="7083c-160">wwwroot folder</span></span>

<span data-ttu-id="7083c-161">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="7083c-161">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="7083c-162">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="7083c-162">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="7083c-163">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="7083c-163">appSettings.json</span></span>

<span data-ttu-id="7083c-164">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7083c-164">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="7083c-165">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="7083c-165">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="7083c-166">Program.cs</span><span class="sxs-lookup"><span data-stu-id="7083c-166">Program.cs</span></span>

<span data-ttu-id="7083c-167">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="7083c-167">Contains the entry point for the program.</span></span> <span data-ttu-id="7083c-168">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="7083c-168">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="7083c-169">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7083c-169">Startup.cs</span></span>

<span data-ttu-id="7083c-170">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="7083c-170">Contains code that configures app behavior.</span></span> <span data-ttu-id="7083c-171">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="7083c-171">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="7083c-172">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7083c-172">Next steps</span></span>

<span data-ttu-id="7083c-173">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="7083c-173">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7083c-174">添加模型</span><span class="sxs-lookup"><span data-stu-id="7083c-174">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="7083c-175">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="7083c-175">This is the first tutorial of a series.</span></span> <span data-ttu-id="7083c-176">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="7083c-176">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="7083c-177">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-177">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="7083c-178">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="7083c-178">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7083c-179">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-179">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="7083c-180">运行应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-180">Run the app.</span></span>
> * <span data-ttu-id="7083c-181">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="7083c-181">Examine the project files.</span></span>

<span data-ttu-id="7083c-182">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="7083c-182">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="7083c-184">先决条件</span><span class="sxs-lookup"><span data-stu-id="7083c-184">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7083c-185">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7083c-185">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="7083c-186">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7083c-186">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7083c-187">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7083c-187">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="7083c-188">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="7083c-188">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7083c-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7083c-189">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7083c-190">从 Visual Studio“文件”  菜单中选择“新建”  >“项目”  。</span><span class="sxs-lookup"><span data-stu-id="7083c-190">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="7083c-191">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="7083c-191">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="7083c-193">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="7083c-193">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="7083c-194">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="7083c-194">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="7083c-196">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="7083c-196">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="7083c-198">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="7083c-198">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="7083c-200">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7083c-200">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="7083c-201">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="7083c-201">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="7083c-202">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="7083c-202">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="7083c-203">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="7083c-203">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="7083c-204">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="7083c-204">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="7083c-205">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="7083c-205">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="7083c-206">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="7083c-206">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="7083c-207">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="7083c-207">Select **Yes**.</span></span>

  <span data-ttu-id="7083c-208">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="7083c-208">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7083c-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7083c-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7083c-210">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="7083c-210">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="7083c-212">选择“.NET Core”  >“应用”  >“Web 应用程序”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="7083c-212">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS“新建项目”对话框](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="7083c-214">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架设置为“.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="7083c-214">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 选择](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="7083c-216">将项目命名为“RazorPagesMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="7083c-216">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="7083c-218">运行应用</span><span class="sxs-lookup"><span data-stu-id="7083c-218">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="7083c-219">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7083c-219">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7083c-220">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7083c-220">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="7083c-221">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="7083c-221">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="7083c-222">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="7083c-222">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="7083c-223">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7083c-223">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="7083c-224">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="7083c-224">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="7083c-225">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="7083c-225">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="7083c-226">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="7083c-226">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="7083c-227">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="7083c-227">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="7083c-229">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="7083c-229">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="7083c-231">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7083c-231">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="7083c-232">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7083c-232">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="7083c-233">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="7083c-233">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="7083c-234">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="7083c-234">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="7083c-235">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7083c-235">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="7083c-236">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="7083c-236">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="7083c-237">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="7083c-237">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="7083c-238">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="7083c-238">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="7083c-240">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="7083c-240">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="7083c-242">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7083c-242">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="7083c-243">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7083c-243">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="7083c-244">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="7083c-244">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="7083c-245">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="7083c-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="7083c-246">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="7083c-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="7083c-248">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="7083c-248">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="7083c-250">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="7083c-250">Examine the project files</span></span>

<span data-ttu-id="7083c-251">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="7083c-251">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="7083c-252">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="7083c-252">Pages folder</span></span>

<span data-ttu-id="7083c-253">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="7083c-253">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="7083c-254">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="7083c-254">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="7083c-255">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="7083c-255">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="7083c-256">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="7083c-256">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="7083c-257">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="7083c-257">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="7083c-258">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="7083c-258">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="7083c-259">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="7083c-259">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="7083c-260">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="7083c-260">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="7083c-261">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="7083c-261">wwwroot folder</span></span>

<span data-ttu-id="7083c-262">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="7083c-262">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="7083c-263">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="7083c-263">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="7083c-264">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="7083c-264">appSettings.json</span></span>

<span data-ttu-id="7083c-265">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="7083c-265">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="7083c-266">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="7083c-266">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="7083c-267">Program.cs</span><span class="sxs-lookup"><span data-stu-id="7083c-267">Program.cs</span></span>

<span data-ttu-id="7083c-268">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="7083c-268">Contains the entry point for the program.</span></span> <span data-ttu-id="7083c-269">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="7083c-269">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="7083c-270">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="7083c-270">Startup.cs</span></span>

<span data-ttu-id="7083c-271">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="7083c-271">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="7083c-272">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="7083c-272">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="7083c-273">其他资源</span><span class="sxs-lookup"><span data-stu-id="7083c-273">Additional resources</span></span>

* [<span data-ttu-id="7083c-274">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="7083c-274">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="7083c-275">后续步骤</span><span class="sxs-lookup"><span data-stu-id="7083c-275">Next steps</span></span>

<span data-ttu-id="7083c-276">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="7083c-276">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7083c-277">添加模型</span><span class="sxs-lookup"><span data-stu-id="7083c-277">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
