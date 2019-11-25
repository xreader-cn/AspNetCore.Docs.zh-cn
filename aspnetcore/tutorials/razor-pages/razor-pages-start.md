---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 11/12/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: a8381dee05f267077a29999f3d8bbe6327c2b863
ms.sourcegitcommit: 231780c8d7848943e5e9fd55e93f437f7e5a371d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/15/2019
ms.locfileid: "74116146"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="1ec22-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1ec22-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1ec22-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1ec22-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1ec22-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="1ec22-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1ec22-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1ec22-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1ec22-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1ec22-109">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1ec22-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-110">Run the app.</span></span>
> * <span data-ttu-id="1ec22-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-111">Examine the project files.</span></span>

<span data-ttu-id="1ec22-112">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="1ec22-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1ec22-114">系统必备</span><span class="sxs-lookup"><span data-stu-id="1ec22-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1ec22-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ec22-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1ec22-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ec22-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1ec22-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1ec22-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1ec22-118">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="1ec22-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1ec22-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ec22-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1ec22-120">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="1ec22-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1ec22-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1ec22-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1ec22-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1ec22-123">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1ec22-124">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1ec22-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1ec22-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1ec22-126">在下拉列表中选择“ASP.NET Core 3.0”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="1ec22-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1ec22-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="1ec22-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1ec22-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ec22-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1ec22-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1ec22-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1ec22-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1ec22-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1ec22-134">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1ec22-135">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1ec22-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1ec22-136">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="1ec22-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1ec22-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="1ec22-137">Select **Yes**.</span></span>

  <span data-ttu-id="1ec22-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="1ec22-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1ec22-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1ec22-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="1ec22-140">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-140">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="1ec22-142">选择“.NET Core”  >“应用”  >“Web 应用程序”  >“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS“新建项目”对话框](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="1ec22-144">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架设置为“.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="1ec22-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![macOS .NET Core 3.0 选择](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="1ec22-146">将项目命名为“RazorPagesMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="1ec22-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="1ec22-148">打开项目</span><span class="sxs-lookup"><span data-stu-id="1ec22-148">Open the project</span></span>

<span data-ttu-id="1ec22-149">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1ec22-150">运行应用</span><span class="sxs-lookup"><span data-stu-id="1ec22-150">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="1ec22-151">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="1ec22-151">Examine the project files</span></span>

<span data-ttu-id="1ec22-152">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-152">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1ec22-153">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="1ec22-153">Pages folder</span></span>

<span data-ttu-id="1ec22-154">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-154">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1ec22-155">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="1ec22-155">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1ec22-156">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-156">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1ec22-157">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-157">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1ec22-158">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="1ec22-158">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1ec22-159">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-159">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1ec22-160">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="1ec22-160">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1ec22-161">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-161">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1ec22-162">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="1ec22-162">wwwroot folder</span></span>

<span data-ttu-id="1ec22-163">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-163">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1ec22-164">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-164">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1ec22-165">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1ec22-165">appSettings.json</span></span>

<span data-ttu-id="1ec22-166">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1ec22-166">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1ec22-167">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-167">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1ec22-168">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1ec22-168">Program.cs</span></span>

<span data-ttu-id="1ec22-169">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="1ec22-169">Contains the entry point for the program.</span></span> <span data-ttu-id="1ec22-170">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-170">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1ec22-171">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1ec22-171">Startup.cs</span></span>

<span data-ttu-id="1ec22-172">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="1ec22-172">Contains code that configures app behavior.</span></span> <span data-ttu-id="1ec22-173">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-173">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1ec22-174">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1ec22-174">Next steps</span></span>

<span data-ttu-id="1ec22-175">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="1ec22-175">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1ec22-176">添加模型</span><span class="sxs-lookup"><span data-stu-id="1ec22-176">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1ec22-177">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="1ec22-177">This is the first tutorial of a series.</span></span> <span data-ttu-id="1ec22-178">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="1ec22-178">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1ec22-179">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-179">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1ec22-180">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1ec22-180">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1ec22-181">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-181">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1ec22-182">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-182">Run the app.</span></span>
> * <span data-ttu-id="1ec22-183">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-183">Examine the project files.</span></span>

<span data-ttu-id="1ec22-184">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="1ec22-184">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1ec22-186">系统必备</span><span class="sxs-lookup"><span data-stu-id="1ec22-186">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1ec22-187">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ec22-187">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1ec22-188">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ec22-188">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1ec22-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1ec22-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1ec22-190">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="1ec22-190">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1ec22-191">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ec22-191">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1ec22-192">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="1ec22-192">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1ec22-193">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-193">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1ec22-195">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-195">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1ec22-196">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-196">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="1ec22-198">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="1ec22-198">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1ec22-200">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="1ec22-200">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1ec22-202">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ec22-202">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1ec22-203">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-203">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1ec22-204">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-204">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1ec22-205">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1ec22-205">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1ec22-206">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-206">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1ec22-207">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1ec22-207">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1ec22-208">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="1ec22-208">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1ec22-209">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="1ec22-209">Select **Yes**.</span></span>

  <span data-ttu-id="1ec22-210">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="1ec22-210">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1ec22-211">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1ec22-211">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1ec22-212">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1ec22-212">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```dotnetcli
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="1ec22-213">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="1ec22-213">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="1ec22-214">打开项目</span><span class="sxs-lookup"><span data-stu-id="1ec22-214">Open the project</span></span>

<span data-ttu-id="1ec22-215">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-215">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1ec22-216">运行应用</span><span class="sxs-lookup"><span data-stu-id="1ec22-216">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1ec22-217">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1ec22-217">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1ec22-218">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1ec22-218">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1ec22-219">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-219">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1ec22-220">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="1ec22-220">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1ec22-221">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="1ec22-221">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1ec22-222">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1ec22-222">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1ec22-223">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="1ec22-223">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1ec22-224">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-224">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1ec22-225">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-225">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1ec22-227">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="1ec22-227">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1ec22-229">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1ec22-229">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1ec22-230">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1ec22-230">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1ec22-231">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="1ec22-231">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1ec22-232">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="1ec22-232">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1ec22-233">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="1ec22-233">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1ec22-234">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1ec22-234">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1ec22-235">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1ec22-236">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1ec22-238">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="1ec22-238">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1ec22-240">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1ec22-240">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1ec22-241">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1ec22-241">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1ec22-242">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="1ec22-242">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1ec22-243">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-243">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1ec22-244">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1ec22-244">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1ec22-246">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="1ec22-246">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1ec22-248">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="1ec22-248">Examine the project files</span></span>

<span data-ttu-id="1ec22-249">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="1ec22-249">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1ec22-250">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="1ec22-250">Pages folder</span></span>

<span data-ttu-id="1ec22-251">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-251">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1ec22-252">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="1ec22-252">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1ec22-253">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-253">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1ec22-254">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-254">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1ec22-255">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="1ec22-255">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1ec22-256">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="1ec22-256">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1ec22-257">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="1ec22-257">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1ec22-258">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-258">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1ec22-259">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="1ec22-259">wwwroot folder</span></span>

<span data-ttu-id="1ec22-260">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="1ec22-260">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1ec22-261">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-261">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1ec22-262">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1ec22-262">appSettings.json</span></span>

<span data-ttu-id="1ec22-263">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1ec22-263">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1ec22-264">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-264">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1ec22-265">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1ec22-265">Program.cs</span></span>

<span data-ttu-id="1ec22-266">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="1ec22-266">Contains the entry point for the program.</span></span> <span data-ttu-id="1ec22-267">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-267">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1ec22-268">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1ec22-268">Startup.cs</span></span>

<span data-ttu-id="1ec22-269">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="1ec22-269">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1ec22-270">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="1ec22-270">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1ec22-271">其他资源</span><span class="sxs-lookup"><span data-stu-id="1ec22-271">Additional resources</span></span>

* [<span data-ttu-id="1ec22-272">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="1ec22-272">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1ec22-273">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1ec22-273">Next steps</span></span>

<span data-ttu-id="1ec22-274">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="1ec22-274">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1ec22-275">添加模型</span><span class="sxs-lookup"><span data-stu-id="1ec22-275">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
