---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 57a10895c718c539ece280afcb27cb4033c7fb45
ms.sourcegitcommit: 979dbfc5e9ce09b9470789989cddfcfb57079d94
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/31/2019
ms.locfileid: "68682797"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="1f083-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="1f083-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="1f083-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="1f083-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="1f083-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="1f083-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1f083-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1f083-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1f083-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1f083-109">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1f083-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-110">Run the app.</span></span>
> * <span data-ttu-id="1f083-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-111">Examine the project files.</span></span>

<span data-ttu-id="1f083-112">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="1f083-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1f083-114">系统必备</span><span class="sxs-lookup"><span data-stu-id="1f083-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f083-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f083-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f083-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f083-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f083-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f083-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1f083-118">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="1f083-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f083-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f083-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f083-120">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="1f083-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="1f083-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1f083-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="1f083-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="1f083-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="1f083-123">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="1f083-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1f083-124">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="1f083-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="1f083-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="1f083-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="1f083-126">在下拉列表中选择“ASP.NET Core 3.0”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="1f083-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="1f083-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="1f083-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f083-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f083-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f083-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="1f083-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1f083-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="1f083-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1f083-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f083-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1f083-134">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="1f083-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1f083-135">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f083-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1f083-136">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="1f083-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1f083-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="1f083-137">Select **Yes**.</span></span>

  <span data-ttu-id="1f083-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="1f083-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f083-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f083-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f083-140">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f083-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="1f083-141">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="1f083-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="1f083-142">打开项目</span><span class="sxs-lookup"><span data-stu-id="1f083-142">Open the project</span></span>

<span data-ttu-id="1f083-143">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1f083-144">运行应用</span><span class="sxs-lookup"><span data-stu-id="1f083-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f083-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f083-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f083-146">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1f083-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1f083-147">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1f083-148">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="1f083-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f083-149">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="1f083-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1f083-150">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1f083-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1f083-151">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="1f083-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f083-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f083-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1f083-153">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1f083-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f083-154">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="1f083-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1f083-155">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="1f083-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f083-156">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="1f083-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1f083-157">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1f083-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f083-158">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f083-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1f083-159">按 Alt-Cmd-Enter 可在不使用调试器的情况下运行  。</span><span class="sxs-lookup"><span data-stu-id="1f083-159">Press **Alt-Cmd-Enter** to run without the debugger.</span></span> <span data-ttu-id="1f083-160">或者，导航到菜单栏，转到“运行”>“在不调试的情况下启动”。</span><span class="sxs-lookup"><span data-stu-id="1f083-160">Alternatively, navigate to the menu bar and go to Run>Start Without Debugging.</span></span>

  <span data-ttu-id="1f083-161">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="1f083-161">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1f083-162">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="1f083-162">Examine the project files</span></span>

<span data-ttu-id="1f083-163">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="1f083-163">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1f083-164">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="1f083-164">Pages folder</span></span>

<span data-ttu-id="1f083-165">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-165">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1f083-166">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="1f083-166">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1f083-167">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="1f083-167">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1f083-168">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="1f083-168">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1f083-169">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="1f083-169">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1f083-170">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="1f083-170">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1f083-171">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="1f083-171">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1f083-172">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="1f083-172">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1f083-173">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="1f083-173">wwwroot folder</span></span>

<span data-ttu-id="1f083-174">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-174">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1f083-175">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="1f083-175">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1f083-176">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1f083-176">appSettings.json</span></span>

<span data-ttu-id="1f083-177">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f083-177">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1f083-178">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="1f083-178">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1f083-179">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1f083-179">Program.cs</span></span>

<span data-ttu-id="1f083-180">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="1f083-180">Contains the entry point for the program.</span></span> <span data-ttu-id="1f083-181">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="1f083-181">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1f083-182">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1f083-182">Startup.cs</span></span>

<span data-ttu-id="1f083-183">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="1f083-183">Contains code that configures app behavior.</span></span> <span data-ttu-id="1f083-184">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="1f083-184">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="1f083-185">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1f083-185">Next steps</span></span>

<span data-ttu-id="1f083-186">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="1f083-186">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1f083-187">添加模型</span><span class="sxs-lookup"><span data-stu-id="1f083-187">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="1f083-188">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="1f083-188">This is the first tutorial of a series.</span></span> <span data-ttu-id="1f083-189">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="1f083-189">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="1f083-190">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-190">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="1f083-191">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="1f083-191">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="1f083-192">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-192">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="1f083-193">运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-193">Run the app.</span></span>
> * <span data-ttu-id="1f083-194">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-194">Examine the project files.</span></span>

<span data-ttu-id="1f083-195">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="1f083-195">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="1f083-197">系统必备</span><span class="sxs-lookup"><span data-stu-id="1f083-197">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f083-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f083-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f083-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f083-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f083-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f083-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="1f083-201">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="1f083-201">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f083-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f083-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f083-203">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="1f083-203">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="1f083-204">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="1f083-204">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="1f083-206">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="1f083-206">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="1f083-207">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="1f083-207">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="1f083-209">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="1f083-209">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="1f083-211">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="1f083-211">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f083-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f083-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="1f083-214">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="1f083-214">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="1f083-215">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="1f083-215">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="1f083-216">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f083-216">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="1f083-217">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="1f083-217">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="1f083-218">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="1f083-218">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="1f083-219">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="1f083-219">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="1f083-220">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="1f083-220">Select **Yes**.</span></span>

  <span data-ttu-id="1f083-221">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="1f083-221">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f083-222">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f083-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="1f083-223">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="1f083-223">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="1f083-224">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="1f083-224">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="1f083-225">打开项目</span><span class="sxs-lookup"><span data-stu-id="1f083-225">Open the project</span></span>

<span data-ttu-id="1f083-226">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-226">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="1f083-227">运行应用</span><span class="sxs-lookup"><span data-stu-id="1f083-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="1f083-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="1f083-228">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="1f083-229">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1f083-229">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="1f083-230">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="1f083-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="1f083-231">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="1f083-231">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f083-232">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="1f083-232">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="1f083-233">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1f083-233">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="1f083-234">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="1f083-234">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="1f083-235">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="1f083-235">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1f083-236">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1f083-236">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1f083-238">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="1f083-238">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="1f083-240">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="1f083-240">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="1f083-241">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1f083-241">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f083-242">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="1f083-242">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="1f083-243">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="1f083-243">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="1f083-244">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="1f083-244">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="1f083-245">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="1f083-245">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="1f083-246">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="1f083-246">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1f083-247">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1f083-247">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="1f083-249">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="1f083-249">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="1f083-251">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="1f083-251">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="1f083-252">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="1f083-252">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="1f083-253">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="1f083-253">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="1f083-254">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="1f083-254">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="1f083-255">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="1f083-255">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="1f083-257">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="1f083-257">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="1f083-259">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="1f083-259">Examine the project files</span></span>

<span data-ttu-id="1f083-260">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="1f083-260">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="1f083-261">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="1f083-261">Pages folder</span></span>

<span data-ttu-id="1f083-262">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-262">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="1f083-263">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="1f083-263">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="1f083-264">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="1f083-264">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="1f083-265">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="1f083-265">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="1f083-266">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="1f083-266">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="1f083-267">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="1f083-267">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="1f083-268">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="1f083-268">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="1f083-269">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="1f083-269">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="1f083-270">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="1f083-270">wwwroot folder</span></span>

<span data-ttu-id="1f083-271">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="1f083-271">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="1f083-272">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="1f083-272">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="1f083-273">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="1f083-273">appSettings.json</span></span>

<span data-ttu-id="1f083-274">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="1f083-274">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="1f083-275">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="1f083-275">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="1f083-276">Program.cs</span><span class="sxs-lookup"><span data-stu-id="1f083-276">Program.cs</span></span>

<span data-ttu-id="1f083-277">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="1f083-277">Contains the entry point for the program.</span></span> <span data-ttu-id="1f083-278">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="1f083-278">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="1f083-279">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="1f083-279">Startup.cs</span></span>

<span data-ttu-id="1f083-280">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="1f083-280">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="1f083-281">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="1f083-281">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="1f083-282">其他资源</span><span class="sxs-lookup"><span data-stu-id="1f083-282">Additional resources</span></span>

* [<span data-ttu-id="1f083-283">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="1f083-283">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="1f083-284">后续步骤</span><span class="sxs-lookup"><span data-stu-id="1f083-284">Next steps</span></span>

<span data-ttu-id="1f083-285">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="1f083-285">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="1f083-286">添加模型</span><span class="sxs-lookup"><span data-stu-id="1f083-286">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end
