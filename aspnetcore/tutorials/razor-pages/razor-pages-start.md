---
title: “教程：在 ASP.NET Core 中开始使用 Razor 页面”
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 11/12/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 3b8ccf639bb91234f81c67750fffa170e52d636f
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452329"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="58322-104">教程：在 ASP.NET Core 中开始使用 Razor Pages</span><span class="sxs-lookup"><span data-stu-id="58322-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="58322-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="58322-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="58322-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="58322-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="58322-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="58322-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="58322-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="58322-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="58322-109">创建 Razor 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="58322-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="58322-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="58322-110">Run the app.</span></span>
> * <span data-ttu-id="58322-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="58322-111">Examine the project files.</span></span>

<span data-ttu-id="58322-112">在本教程结束时，你将有一个工作的 Razor 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="58322-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="58322-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="58322-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58322-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58322-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58322-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58322-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58322-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58322-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="58322-118">创建 Razor 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="58322-118">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58322-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58322-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58322-120">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="58322-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="58322-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="58322-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="58322-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="58322-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="58322-123">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="58322-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="58322-124">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="58322-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="58322-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="58322-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="58322-126">在下拉列表中选择“ASP.NET Core 3.1”，然后依次选择“Web 应用程序”和“创建”  。</span><span class="sxs-lookup"><span data-stu-id="58322-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="58322-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="58322-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="58322-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58322-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="58322-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="58322-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="58322-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="58322-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="58322-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="58322-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="58322-134">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor 页面项目。</span><span class="sxs-lookup"><span data-stu-id="58322-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="58322-135">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie 文件夹。</span><span class="sxs-lookup"><span data-stu-id="58322-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="58322-136">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="58322-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="58322-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="58322-137">Select **Yes**.</span></span>

  <span data-ttu-id="58322-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  </span><span class="sxs-lookup"><span data-stu-id="58322-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58322-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58322-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58322-140">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="58322-140">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="58322-142">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="58322-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="58322-143">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="58322-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web 应用模板选择](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="58322-145">确认以下配置：</span><span class="sxs-lookup"><span data-stu-id="58322-145">Confirm the following configurations:</span></span>

  * <span data-ttu-id="58322-146">“目标框架”设置为“.NET Core 3.1” 。</span><span class="sxs-lookup"><span data-stu-id="58322-146">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="58322-147">“身份验证”设置为“无身份验证” 。</span><span class="sxs-lookup"><span data-stu-id="58322-147">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="58322-148">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="58322-148">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 选择](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="58322-150">将项目命名为“RazorPagesMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="58322-150">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="58322-152">运行应用</span><span class="sxs-lookup"><span data-stu-id="58322-152">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="58322-153">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="58322-153">Examine the project files</span></span>

<span data-ttu-id="58322-154">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="58322-154">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="58322-155">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="58322-155">Pages folder</span></span>

<span data-ttu-id="58322-156">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="58322-156">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="58322-157">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="58322-157">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="58322-158">一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="58322-158">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="58322-159">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="58322-159">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="58322-160">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="58322-160">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="58322-161">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="58322-161">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="58322-162">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="58322-162">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="58322-163">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="58322-163">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="58322-164">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="58322-164">wwwroot folder</span></span>

<span data-ttu-id="58322-165">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="58322-165">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="58322-166">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="58322-166">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="58322-167">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="58322-167">appSettings.json</span></span>

<span data-ttu-id="58322-168">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="58322-168">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="58322-169">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="58322-169">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="58322-170">Program.cs</span><span class="sxs-lookup"><span data-stu-id="58322-170">Program.cs</span></span>

<span data-ttu-id="58322-171">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="58322-171">Contains the entry point for the program.</span></span> <span data-ttu-id="58322-172">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="58322-172">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="58322-173">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="58322-173">Startup.cs</span></span>

<span data-ttu-id="58322-174">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="58322-174">Contains code that configures app behavior.</span></span> <span data-ttu-id="58322-175">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="58322-175">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="58322-176">后续步骤</span><span class="sxs-lookup"><span data-stu-id="58322-176">Next steps</span></span>

<span data-ttu-id="58322-177">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="58322-177">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="58322-178">添加模型</span><span class="sxs-lookup"><span data-stu-id="58322-178">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="58322-179">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="58322-179">This is the first tutorial of a series.</span></span> <span data-ttu-id="58322-180">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="58322-180">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="58322-181">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="58322-181">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="58322-182">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="58322-182">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="58322-183">创建 Razor 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="58322-183">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="58322-184">运行应用。</span><span class="sxs-lookup"><span data-stu-id="58322-184">Run the app.</span></span>
> * <span data-ttu-id="58322-185">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="58322-185">Examine the project files.</span></span>

<span data-ttu-id="58322-186">在本教程结束时，你将有一个工作的 Razor 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="58322-186">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="58322-188">先决条件</span><span class="sxs-lookup"><span data-stu-id="58322-188">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58322-189">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58322-189">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="58322-190">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58322-190">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58322-191">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58322-191">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="58322-192">创建 Razor 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="58322-192">Create a Razor Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58322-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58322-193">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58322-194">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="58322-194">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="58322-195">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="58322-195">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="58322-197">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="58322-197">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="58322-198">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="58322-198">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="58322-200">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”  。</span><span class="sxs-lookup"><span data-stu-id="58322-200">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="58322-202">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="58322-202">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="58322-204">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58322-204">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="58322-205">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="58322-205">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="58322-206">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="58322-206">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="58322-207">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="58322-207">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="58322-208">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor 页面项目。</span><span class="sxs-lookup"><span data-stu-id="58322-208">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="58322-209">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie 文件夹。</span><span class="sxs-lookup"><span data-stu-id="58322-209">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="58322-210">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="58322-210">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="58322-211">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="58322-211">Select **Yes**.</span></span>

  <span data-ttu-id="58322-212">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  </span><span class="sxs-lookup"><span data-stu-id="58322-212">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58322-213">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58322-213">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="58322-214">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="58322-214">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="58322-216">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="58322-216">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="58322-217">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="58322-217">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="58322-218">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架设置为“.NET Core 3.1”  。</span><span class="sxs-lookup"><span data-stu-id="58322-218">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.0 选择](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="58322-220">将项目命名为“RazorPagesMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="58322-220">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="58322-222">运行应用</span><span class="sxs-lookup"><span data-stu-id="58322-222">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="58322-223">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="58322-223">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="58322-224">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="58322-224">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="58322-225">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="58322-225">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="58322-226">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="58322-226">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58322-227">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="58322-227">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="58322-228">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="58322-228">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="58322-229">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="58322-229">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="58322-230">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="58322-230">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="58322-231">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="58322-231">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="58322-233">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="58322-233">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="58322-235">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="58322-235">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="58322-236">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="58322-236">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="58322-237">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="58322-237">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="58322-238">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="58322-238">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="58322-239">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="58322-239">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="58322-240">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="58322-240">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="58322-241">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="58322-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="58322-242">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="58322-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="58322-244">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="58322-244">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="58322-246">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="58322-246">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="58322-247">按 Cmd-Opt-F5，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="58322-247">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="58322-248">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="58322-248">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="58322-249">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="58322-249">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="58322-250">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="58322-250">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="58322-252">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="58322-252">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="58322-254">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="58322-254">Examine the project files</span></span>

<span data-ttu-id="58322-255">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="58322-255">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="58322-256">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="58322-256">Pages folder</span></span>

<span data-ttu-id="58322-257">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="58322-257">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="58322-258">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="58322-258">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="58322-259">一个 .cshtml 文件，其中包含使用 Razor 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="58322-259">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="58322-260">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="58322-260">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="58322-261">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="58322-261">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="58322-262">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="58322-262">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="58322-263">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="58322-263">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="58322-264">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="58322-264">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="58322-265">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="58322-265">wwwroot folder</span></span>

<span data-ttu-id="58322-266">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="58322-266">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="58322-267">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="58322-267">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="58322-268">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="58322-268">appSettings.json</span></span>

<span data-ttu-id="58322-269">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="58322-269">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="58322-270">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="58322-270">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="58322-271">Program.cs</span><span class="sxs-lookup"><span data-stu-id="58322-271">Program.cs</span></span>

<span data-ttu-id="58322-272">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="58322-272">Contains the entry point for the program.</span></span> <span data-ttu-id="58322-273">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="58322-273">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="58322-274">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="58322-274">Startup.cs</span></span>

<span data-ttu-id="58322-275">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="58322-275">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="58322-276">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="58322-276">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="58322-277">其他资源</span><span class="sxs-lookup"><span data-stu-id="58322-277">Additional resources</span></span>

* [<span data-ttu-id="58322-278">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="58322-278">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="58322-279">后续步骤</span><span class="sxs-lookup"><span data-stu-id="58322-279">Next steps</span></span>

<span data-ttu-id="58322-280">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="58322-280">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="58322-281">添加模型</span><span class="sxs-lookup"><span data-stu-id="58322-281">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
