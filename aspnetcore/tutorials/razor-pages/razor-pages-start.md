---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 67a5fcee0a37861fd39a018443edbc0b9e513213
ms.sourcegitcommit: 7a46973998623aead757ad386fe33602b1658793
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/15/2019
ms.locfileid: "69487674"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="487af-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="487af-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="487af-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="487af-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="487af-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="487af-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="487af-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="487af-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="487af-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="487af-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="487af-109">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="487af-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="487af-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="487af-110">Run the app.</span></span>
> * <span data-ttu-id="487af-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="487af-111">Examine the project files.</span></span>

<span data-ttu-id="487af-112">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="487af-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="487af-114">系统必备</span><span class="sxs-lookup"><span data-stu-id="487af-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="487af-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="487af-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="487af-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="487af-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="487af-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="487af-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="487af-118">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="487af-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="487af-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="487af-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="487af-120">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="487af-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="487af-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="487af-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="487af-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="487af-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="487af-123">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="487af-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="487af-124">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="487af-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="487af-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="487af-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="487af-126">在下拉列表中选择“ASP.NET Core 3.0”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="487af-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="487af-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="487af-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="487af-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="487af-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="487af-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="487af-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="487af-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="487af-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="487af-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="487af-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="487af-134">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="487af-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="487af-135">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="487af-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="487af-136">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="487af-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="487af-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="487af-137">Select **Yes**.</span></span>

  <span data-ttu-id="487af-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="487af-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="487af-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="487af-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="487af-140">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="487af-140">Select **File** > **New Solution**.</span></span>

![macOS 新建解决方案](../first-mvc-app/start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="487af-142">选择“.NET Core”  >“应用”  >“Web 应用程序”  >“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="487af-142">Select **.NET Core** > **App** > **Web Application** > **Next**.</span></span>

  ![macOS“新建项目”对话框](razor-pages-start/_static/webapp.png)

* <span data-ttu-id="487af-144">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架设置为“.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="487af-144">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** to **.NET Core 3.0**.</span></span>

  ![macOS .NET Core 3.0 选择](razor-pages-start/_static/targetframework3.png)

* <span data-ttu-id="487af-146">将项目命名为“RazorPagesMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="487af-146">Name the project **RazorPagesMovie**, and then select **Create**.</span></span>

  ![nameproj](razor-pages-start/_static/RazorPagesMovie.png)


## <a name="open-the-project"></a><span data-ttu-id="487af-148">打开项目</span><span class="sxs-lookup"><span data-stu-id="487af-148">Open the project</span></span>

<span data-ttu-id="487af-149">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="487af-149">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="487af-150">运行应用</span><span class="sxs-lookup"><span data-stu-id="487af-150">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="487af-151">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="487af-151">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="487af-152">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="487af-152">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="487af-153">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="487af-153">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="487af-154">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="487af-154">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="487af-155">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="487af-155">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="487af-156">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="487af-156">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="487af-157">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="487af-157">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="487af-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="487af-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="487af-159">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="487af-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="487af-160">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="487af-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="487af-161">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="487af-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="487af-162">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="487af-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="487af-163">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="487af-163">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="487af-164">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="487af-164">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="487af-165">按 Alt-Cmd-Enter 可在不使用调试器的情况下运行  。</span><span class="sxs-lookup"><span data-stu-id="487af-165">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="487af-166">或者，导航到菜单栏，转到“运行”>“在不调试的情况下启动”。</span><span class="sxs-lookup"><span data-stu-id="487af-166">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="487af-167">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="487af-167">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="487af-168">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="487af-168">Examine the project files</span></span>

<span data-ttu-id="487af-169">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="487af-169">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="487af-170">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="487af-170">Pages folder</span></span>

<span data-ttu-id="487af-171">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="487af-171">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="487af-172">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="487af-172">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="487af-173">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="487af-173">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="487af-174">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="487af-174">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="487af-175">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="487af-175">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="487af-176">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="487af-176">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="487af-177">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="487af-177">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="487af-178">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="487af-178">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="487af-179">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="487af-179">wwwroot folder</span></span>

<span data-ttu-id="487af-180">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="487af-180">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="487af-181">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="487af-181">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="487af-182">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="487af-182">appSettings.json</span></span>

<span data-ttu-id="487af-183">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="487af-183">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="487af-184">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="487af-184">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="487af-185">Program.cs</span><span class="sxs-lookup"><span data-stu-id="487af-185">Program.cs</span></span>

<span data-ttu-id="487af-186">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="487af-186">Contains the entry point for the program.</span></span> <span data-ttu-id="487af-187">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="487af-187">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="487af-188">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="487af-188">Startup.cs</span></span>

<span data-ttu-id="487af-189">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="487af-189">Contains code that configures app behavior.</span></span> <span data-ttu-id="487af-190">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="487af-190">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="487af-191">后续步骤</span><span class="sxs-lookup"><span data-stu-id="487af-191">Next steps</span></span>

<span data-ttu-id="487af-192">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="487af-192">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="487af-193">添加模型</span><span class="sxs-lookup"><span data-stu-id="487af-193">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="487af-194">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="487af-194">This is the first tutorial of a series.</span></span> <span data-ttu-id="487af-195">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="487af-195">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="487af-196">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="487af-196">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="487af-197">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="487af-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="487af-198">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="487af-198">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="487af-199">运行应用。</span><span class="sxs-lookup"><span data-stu-id="487af-199">Run the app.</span></span>
> * <span data-ttu-id="487af-200">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="487af-200">Examine the project files.</span></span>

<span data-ttu-id="487af-201">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="487af-201">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="487af-203">系统必备</span><span class="sxs-lookup"><span data-stu-id="487af-203">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="487af-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="487af-204">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="487af-205">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="487af-205">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="487af-206">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="487af-206">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="487af-207">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="487af-207">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="487af-208">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="487af-208">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="487af-209">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="487af-209">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="487af-210">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="487af-210">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="487af-212">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="487af-212">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="487af-213">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="487af-213">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="487af-215">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="487af-215">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="487af-217">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="487af-217">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="487af-219">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="487af-219">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="487af-220">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="487af-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="487af-221">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="487af-221">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="487af-222">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="487af-222">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="487af-223">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="487af-223">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="487af-224">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="487af-224">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="487af-225">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="487af-225">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="487af-226">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="487af-226">Select **Yes**.</span></span>

  <span data-ttu-id="487af-227">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="487af-227">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="487af-228">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="487af-228">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="487af-229">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="487af-229">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="487af-230">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="487af-230">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="487af-231">打开项目</span><span class="sxs-lookup"><span data-stu-id="487af-231">Open the project</span></span>

<span data-ttu-id="487af-232">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="487af-232">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="487af-233">运行应用</span><span class="sxs-lookup"><span data-stu-id="487af-233">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="487af-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="487af-234">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="487af-235">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="487af-235">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="487af-236">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="487af-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="487af-237">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="487af-237">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="487af-238">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="487af-238">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="487af-239">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="487af-239">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="487af-240">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="487af-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="487af-241">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="487af-241">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="487af-242">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="487af-242">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="487af-244">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="487af-244">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="487af-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="487af-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="487af-247">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="487af-247">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="487af-248">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="487af-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="487af-249">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="487af-249">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="487af-250">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="487af-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="487af-251">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="487af-251">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="487af-252">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="487af-252">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="487af-253">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="487af-253">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="487af-255">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="487af-255">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="487af-257">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="487af-257">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="487af-258">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="487af-258">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="487af-259">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="487af-259">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="487af-260">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="487af-260">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="487af-261">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="487af-261">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="487af-263">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="487af-263">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="487af-265">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="487af-265">Examine the project files</span></span>

<span data-ttu-id="487af-266">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="487af-266">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="487af-267">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="487af-267">Pages folder</span></span>

<span data-ttu-id="487af-268">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="487af-268">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="487af-269">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="487af-269">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="487af-270">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="487af-270">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="487af-271">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="487af-271">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="487af-272">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="487af-272">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="487af-273">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="487af-273">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="487af-274">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="487af-274">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="487af-275">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="487af-275">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="487af-276">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="487af-276">wwwroot folder</span></span>

<span data-ttu-id="487af-277">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="487af-277">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="487af-278">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="487af-278">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="487af-279">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="487af-279">appSettings.json</span></span>

<span data-ttu-id="487af-280">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="487af-280">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="487af-281">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="487af-281">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="487af-282">Program.cs</span><span class="sxs-lookup"><span data-stu-id="487af-282">Program.cs</span></span>

<span data-ttu-id="487af-283">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="487af-283">Contains the entry point for the program.</span></span> <span data-ttu-id="487af-284">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="487af-284">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="487af-285">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="487af-285">Startup.cs</span></span>

<span data-ttu-id="487af-286">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="487af-286">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="487af-287">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="487af-287">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="487af-288">其他资源</span><span class="sxs-lookup"><span data-stu-id="487af-288">Additional resources</span></span>

* [<span data-ttu-id="487af-289">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="487af-289">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="487af-290">后续步骤</span><span class="sxs-lookup"><span data-stu-id="487af-290">Next steps</span></span>

<span data-ttu-id="487af-291">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="487af-291">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="487af-292">添加模型</span><span class="sxs-lookup"><span data-stu-id="487af-292">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
