---
title: '教程：在 ASP.NET Core 中开始使用 :::no-loc(Razor)::: Pages'
author: rick-anderson
description: '此系列教程演示了如何在 ASP.NET Core 中使用 :::no-loc(Razor)::: Pages。 了解如何创建模型、为 :::no-loc(Razor)::: Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。'
ms.author: riande
ms.date: 11/12/2019
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
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: ab890b956b1242f183054b7ab4575a59072b4f50
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93060230"
---
# <a name="tutorial-get-started-with-no-locrazor-pages-in-aspnet-core"></a><span data-ttu-id="a60ac-104">教程：在 ASP.NET Core 中开始使用 :::no-loc(Razor)::: Pages</span><span class="sxs-lookup"><span data-stu-id="a60ac-104">Tutorial: Get started with :::no-loc(Razor)::: Pages in ASP.NET Core</span></span>

<span data-ttu-id="a60ac-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a60ac-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="a60ac-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core :::no-loc(Razor)::: 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a60ac-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a60ac-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a60ac-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="a60ac-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a60ac-109">创建 :::no-loc(Razor)::: 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-109">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="a60ac-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-110">Run the app.</span></span>
> * <span data-ttu-id="a60ac-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="a60ac-111">Examine the project files.</span></span>

<span data-ttu-id="a60ac-112">在本教程结束时，你将有一个工作的 :::no-loc(Razor)::: 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="a60ac-112">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a60ac-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="a60ac-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a60ac-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a60ac-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a60ac-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a60ac-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a60ac-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a60ac-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="a60ac-118">创建 :::no-loc(Razor)::: 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="a60ac-118">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a60ac-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a60ac-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a60ac-120">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="a60ac-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="a60ac-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="a60ac-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="a60ac-123">将项目命名为“:::no-loc(Razor):::PagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-123">Name the project **:::no-loc(Razor):::PagesMovie**.</span></span> <span data-ttu-id="a60ac-124">请务必将项目命名为“:::no-loc(Razor):::PagesMovie”，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="a60ac-124">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="a60ac-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="a60ac-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="a60ac-126">在下拉列表中选择“ASP.NET Core 3.1”，然后依次选择“Web 应用程序”和“创建”  。</span><span class="sxs-lookup"><span data-stu-id="a60ac-126">Select **ASP.NET Core 3.1** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="a60ac-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="a60ac-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a60ac-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a60ac-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a60ac-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a60ac-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a60ac-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a60ac-133">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="a60ac-134">`dotnet new` 命令在“:::no-loc(Razor):::PagesMovie”文件夹中新建 :::no-loc(Razor)::: Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="a60ac-134">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="a60ac-135">`code` 命令在 Visual Studio Code 的当前实例中打开“:::no-loc(Razor):::PagesMovie”文件夹。</span><span class="sxs-lookup"><span data-stu-id="a60ac-135">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a60ac-136">在状态栏的 OmniSharp 火焰图标变绿后，对话框就会询问“':::no-loc(Razor):::PagesMovie' 缺少生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="a60ac-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="a60ac-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="a60ac-137">Select **Yes**.</span></span>

  <span data-ttu-id="a60ac-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  </span><span class="sxs-lookup"><span data-stu-id="a60ac-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a60ac-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a60ac-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a60ac-140">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="a60ac-140">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a60ac-142">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="a60ac-142">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a60ac-143">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="a60ac-143">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS Web 应用模板选择](razor-pages-start/_static/web_app_template_vsmac.png)

* <span data-ttu-id="a60ac-145">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="a60ac-145">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a60ac-146">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-146">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a60ac-147">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="a60ac-147">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="a60ac-148">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-148">Select **Next**.</span></span>

* <span data-ttu-id="a60ac-149">将项目命名为“:::no-loc(Razor):::PagesMovie”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-149">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create**.</span></span>

  ![macOS 命名项目](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a60ac-151">运行应用</span><span class="sxs-lookup"><span data-stu-id="a60ac-151">Run the app</span></span>

  [!INCLUDE[](~/includes/run-the-app.md)]

## <a name="examine-the-project-files"></a><span data-ttu-id="a60ac-152">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="a60ac-152">Examine the project files</span></span>

<span data-ttu-id="a60ac-153">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-153">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a60ac-154">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="a60ac-154">Pages folder</span></span>

<span data-ttu-id="a60ac-155">包含 :::no-loc(Razor)::: 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="a60ac-155">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="a60ac-156">每个 :::no-loc(Razor)::: 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="a60ac-156">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="a60ac-157">一个 .cshtml 文件，其中包含使用 :::no-loc(Razor)::: 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="a60ac-157">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="a60ac-158">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="a60ac-158">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a60ac-159">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="a60ac-159">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a60ac-160">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="a60ac-160">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a60ac-161">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="a60ac-161">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a60ac-162">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-162">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a60ac-163">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="a60ac-163">wwwroot folder</span></span>

<span data-ttu-id="a60ac-164">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="a60ac-164">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a60ac-165">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-165">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a60ac-166">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="a60ac-166">appSettings.json</span></span>

<span data-ttu-id="a60ac-167">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a60ac-167">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a60ac-168">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-168">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a60ac-169">Program.cs</span><span class="sxs-lookup"><span data-stu-id="a60ac-169">Program.cs</span></span>

<span data-ttu-id="a60ac-170">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="a60ac-170">Contains the entry point for the program.</span></span> <span data-ttu-id="a60ac-171">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-171">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a60ac-172">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a60ac-172">Startup.cs</span></span>

<span data-ttu-id="a60ac-173">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="a60ac-173">Contains code that configures app behavior.</span></span> <span data-ttu-id="a60ac-174">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-174">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="a60ac-175">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a60ac-175">Next steps</span></span>

<span data-ttu-id="a60ac-176">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="a60ac-176">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a60ac-177">添加模型</span><span class="sxs-lookup"><span data-stu-id="a60ac-177">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="a60ac-178">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="a60ac-178">This is the first tutorial of a series.</span></span> <span data-ttu-id="a60ac-179">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core :::no-loc(Razor)::: 页面 Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a60ac-179">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core :::no-loc(Razor)::: Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="a60ac-180">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-180">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="a60ac-181">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="a60ac-181">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a60ac-182">创建 :::no-loc(Razor)::: 页面 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-182">Create a :::no-loc(Razor)::: Pages web app.</span></span>
> * <span data-ttu-id="a60ac-183">运行应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-183">Run the app.</span></span>
> * <span data-ttu-id="a60ac-184">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="a60ac-184">Examine the project files.</span></span>

<span data-ttu-id="a60ac-185">在本教程结束时，你将有一个工作的 :::no-loc(Razor)::: 页面 Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="a60ac-185">At the end of this tutorial, you'll have a working :::no-loc(Razor)::: Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="a60ac-187">先决条件</span><span class="sxs-lookup"><span data-stu-id="a60ac-187">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a60ac-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a60ac-188">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a60ac-189">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a60ac-189">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a60ac-190">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a60ac-190">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-no-locrazor-pages-web-app"></a><span data-ttu-id="a60ac-191">创建 :::no-loc(Razor)::: 页面 Web 应用</span><span class="sxs-lookup"><span data-stu-id="a60ac-191">Create a :::no-loc(Razor)::: Pages web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a60ac-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a60ac-192">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a60ac-193">从 Visual Studio“文件”菜单中选择“新建”>“项目”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-193">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="a60ac-194">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-194">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="a60ac-196">将项目命名为“:::no-loc(Razor):::PagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-196">Name the project **:::no-loc(Razor):::PagesMovie**.</span></span> <span data-ttu-id="a60ac-197">请务必将项目命名为“:::no-loc(Razor):::PagesMovie”，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="a60ac-197">It's important to name the project *:::no-loc(Razor):::PagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="a60ac-199">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”  。</span><span class="sxs-lookup"><span data-stu-id="a60ac-199">Select **ASP.NET Core 2.2** in the dropdown, **Web Application** , and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="a60ac-201">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="a60ac-201">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a60ac-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a60ac-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="a60ac-204">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-204">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="a60ac-205">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-205">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="a60ac-206">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="a60ac-206">Run the following commands:</span></span>

  ```dotnetcli
  dotnet new webapp -o :::no-loc(Razor):::PagesMovie
  code -r :::no-loc(Razor):::PagesMovie
  ```

  * <span data-ttu-id="a60ac-207">`dotnet new` 命令在“:::no-loc(Razor):::PagesMovie”文件夹中新建 :::no-loc(Razor)::: Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="a60ac-207">The `dotnet new` command creates a new :::no-loc(Razor)::: Pages project in the *:::no-loc(Razor):::PagesMovie* folder.</span></span>
  * <span data-ttu-id="a60ac-208">`code` 命令在 Visual Studio Code 的当前实例中打开“:::no-loc(Razor):::PagesMovie”文件夹。</span><span class="sxs-lookup"><span data-stu-id="a60ac-208">The `code` command opens the *:::no-loc(Razor):::PagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="a60ac-209">在状态栏的 OmniSharp 火焰图标变绿后，对话框就会询问“':::no-loc(Razor):::PagesMovie' 缺少生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="a60ac-209">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from ':::no-loc(Razor):::PagesMovie'. Add them?**</span></span> <span data-ttu-id="a60ac-210">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="a60ac-210">Select **Yes**.</span></span>

  <span data-ttu-id="a60ac-211">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。  </span><span class="sxs-lookup"><span data-stu-id="a60ac-211">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a60ac-212">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a60ac-212">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a60ac-213">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="a60ac-213">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a60ac-215">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序” > “下一步”   。</span><span class="sxs-lookup"><span data-stu-id="a60ac-215">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application** > **Next**.</span></span> <span data-ttu-id="a60ac-216">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="a60ac-216">In version 8.6 or later, select **Web and Console** > **App** > **Web Application** > **Next**.</span></span>

* <span data-ttu-id="a60ac-217">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="a60ac-217">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a60ac-218">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-218">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a60ac-219">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="a60ac-219">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="a60ac-220">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-220">Select **Next**.</span></span>

* <span data-ttu-id="a60ac-221">将项目命名为“:::no-loc(Razor):::PagesMovie”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="a60ac-221">Name the project **:::no-loc(Razor):::PagesMovie** , and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/:::no-loc(Razor):::PagesMovie.png)

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="a60ac-223">运行应用</span><span class="sxs-lookup"><span data-stu-id="a60ac-223">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a60ac-224">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a60ac-224">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a60ac-225">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="a60ac-225">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="a60ac-226">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-226">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a60ac-227">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a60ac-227">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a60ac-228">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a60ac-228">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="a60ac-229">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="a60ac-229">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="a60ac-230">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="a60ac-230">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="a60ac-231">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="a60ac-231">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a60ac-232">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-232">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a60ac-234">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="a60ac-234">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-code"></a>[<span data-ttu-id="a60ac-236">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a60ac-236">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="a60ac-237">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="a60ac-237">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a60ac-238">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a60ac-238">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="a60ac-239">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a60ac-239">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a60ac-240">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a60ac-240">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a60ac-241">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="a60ac-241">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="a60ac-242">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="a60ac-242">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a60ac-243">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-243">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="a60ac-245">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="a60ac-245">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a60ac-247">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a60ac-247">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a60ac-248">按 Cmd-Opt-F5，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="a60ac-248">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="a60ac-249">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a60ac-249">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="a60ac-250">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="a60ac-250">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="a60ac-251">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="a60ac-251">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="a60ac-253">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="a60ac-253">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="a60ac-255">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="a60ac-255">Examine the project files</span></span>

<span data-ttu-id="a60ac-256">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="a60ac-256">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="a60ac-257">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="a60ac-257">Pages folder</span></span>

<span data-ttu-id="a60ac-258">包含 :::no-loc(Razor)::: 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="a60ac-258">Contains :::no-loc(Razor)::: pages and supporting files.</span></span> <span data-ttu-id="a60ac-259">每个 :::no-loc(Razor)::: 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="a60ac-259">Each :::no-loc(Razor)::: page is a pair of files:</span></span>

* <span data-ttu-id="a60ac-260">一个 .cshtml 文件，其中包含使用 :::no-loc(Razor)::: 语法的 C# 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="a60ac-260">A *.cshtml* file that contains HTML markup with C# code using :::no-loc(Razor)::: syntax.</span></span>
* <span data-ttu-id="a60ac-261">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="a60ac-261">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="a60ac-262">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="a60ac-262">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="a60ac-263">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="a60ac-263">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="a60ac-264">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="a60ac-264">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="a60ac-265">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-265">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="a60ac-266">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="a60ac-266">wwwroot folder</span></span>

<span data-ttu-id="a60ac-267">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="a60ac-267">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="a60ac-268">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-268">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="a60ac-269">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="a60ac-269">appSettings.json</span></span>

<span data-ttu-id="a60ac-270">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="a60ac-270">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="a60ac-271">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-271">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="a60ac-272">Program.cs</span><span class="sxs-lookup"><span data-stu-id="a60ac-272">Program.cs</span></span>

<span data-ttu-id="a60ac-273">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="a60ac-273">Contains the entry point for the program.</span></span> <span data-ttu-id="a60ac-274">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-274">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="a60ac-275">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="a60ac-275">Startup.cs</span></span>

<span data-ttu-id="a60ac-276">包含配置应用行为的代码，例如是否需要同意 :::no-loc(cookie):::。</span><span class="sxs-lookup"><span data-stu-id="a60ac-276">Contains code that configures app behavior, such as whether it requires consent for :::no-loc(cookie):::s.</span></span> <span data-ttu-id="a60ac-277">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="a60ac-277">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="a60ac-278">其他资源</span><span class="sxs-lookup"><span data-stu-id="a60ac-278">Additional resources</span></span>

* [<span data-ttu-id="a60ac-279">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="a60ac-279">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="a60ac-280">后续步骤</span><span class="sxs-lookup"><span data-stu-id="a60ac-280">Next steps</span></span>

<span data-ttu-id="a60ac-281">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="a60ac-281">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a60ac-282">添加模型</span><span class="sxs-lookup"><span data-stu-id="a60ac-282">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
