---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 07/25/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1605197188d97f27a884739a72400da2d5818b1a
ms.sourcegitcommit: 849af69ee3c94cdb9fd8fa1f1bb8f5a5dda7b9eb
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2019
ms.locfileid: "68371991"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="2c92e-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="2c92e-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="2c92e-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2c92e-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"
<span data-ttu-id="2c92e-106">本教程是系列教程中的第一个教程，介绍生成 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="2c92e-106">This is the first tutorial of a series that teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2c92e-107">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-107">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2c92e-108">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="2c92e-108">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c92e-109">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-109">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2c92e-110">运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-110">Run the app.</span></span>
> * <span data-ttu-id="2c92e-111">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-111">Examine the project files.</span></span>

<span data-ttu-id="2c92e-112">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="2c92e-112">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2c92e-114">系统必备</span><span class="sxs-lookup"><span data-stu-id="2c92e-114">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c92e-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c92e-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c92e-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c92e-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c92e-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c92e-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2c92e-118">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="2c92e-118">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c92e-119">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c92e-119">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c92e-120">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="2c92e-120">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>
* <span data-ttu-id="2c92e-121">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-121">Create a new ASP.NET Core Web Application and select **Next**.</span></span>
  <span data-ttu-id="2c92e-122">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)</span><span class="sxs-lookup"><span data-stu-id="2c92e-122">![new ASP.NET Core Web Application](razor-pages-start/_static/np_2.1.png)</span></span>
* <span data-ttu-id="2c92e-123">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-123">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2c92e-124">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-124">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>
  <span data-ttu-id="2c92e-125">![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)</span><span class="sxs-lookup"><span data-stu-id="2c92e-125">![new ASP.NET Core Web Application](razor-pages-start/_static/config.png)</span></span>

* <span data-ttu-id="2c92e-126">在下拉列表中选择“ASP.NET Core 3.0”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="2c92e-126">Select **ASP.NET Core 3.0** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/3/npx.png)

  <span data-ttu-id="2c92e-128">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="2c92e-128">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c92e-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c92e-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c92e-131">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-131">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2c92e-132">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-132">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2c92e-133">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c92e-133">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2c92e-134">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-134">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2c92e-135">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c92e-135">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2c92e-136">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="2c92e-136">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2c92e-137">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="2c92e-137">Select **Yes**.</span></span>

  <span data-ttu-id="2c92e-138">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="2c92e-138">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c92e-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c92e-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c92e-140">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c92e-140">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="2c92e-141">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="2c92e-141">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="2c92e-142">打开项目</span><span class="sxs-lookup"><span data-stu-id="2c92e-142">Open the project</span></span>

<span data-ttu-id="2c92e-143">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-143">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2c92e-144">运行应用</span><span class="sxs-lookup"><span data-stu-id="2c92e-144">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c92e-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c92e-145">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c92e-146">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c92e-146">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2c92e-147">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-147">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2c92e-148">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-148">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2c92e-149">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2c92e-149">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2c92e-150">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c92e-150">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2c92e-151">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="2c92e-151">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
 
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c92e-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c92e-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2c92e-153">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c92e-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2c92e-154">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2c92e-155">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2c92e-156">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2c92e-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2c92e-157">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c92e-157">Localhost only serves web requests from the local computer.</span></span>

  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c92e-158">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c92e-158">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2c92e-159">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c92e-159">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2c92e-160">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-160">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2c92e-161">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="2c92e-161">Examine the project files</span></span>

<span data-ttu-id="2c92e-162">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2c92e-163">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="2c92e-163">Pages folder</span></span>

<span data-ttu-id="2c92e-164">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2c92e-165">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="2c92e-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2c92e-166">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2c92e-167">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2c92e-168">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="2c92e-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2c92e-169">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2c92e-170">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="2c92e-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2c92e-171">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-171">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2c92e-172">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="2c92e-172">wwwroot folder</span></span>

<span data-ttu-id="2c92e-173">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2c92e-174">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2c92e-175">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2c92e-175">appSettings.json</span></span>

<span data-ttu-id="2c92e-176">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="2c92e-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2c92e-177">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2c92e-178">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2c92e-178">Program.cs</span></span>

<span data-ttu-id="2c92e-179">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="2c92e-179">Contains the entry point for the program.</span></span> <span data-ttu-id="2c92e-180">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-180">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2c92e-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2c92e-181">Startup.cs</span></span>

<span data-ttu-id="2c92e-182">包含配置应用行为的代码。</span><span class="sxs-lookup"><span data-stu-id="2c92e-182">Contains code that configures app behavior.</span></span> <span data-ttu-id="2c92e-183">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="2c92e-184">后续步骤</span><span class="sxs-lookup"><span data-stu-id="2c92e-184">Next steps</span></span>

<span data-ttu-id="2c92e-185">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="2c92e-185">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2c92e-186">添加模型</span><span class="sxs-lookup"><span data-stu-id="2c92e-186">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end

<!--::: moniker range=">= aspnetcore-3.0" -->

::: moniker range="< aspnetcore-3.0"

<span data-ttu-id="2c92e-187">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="2c92e-187">This is the first tutorial of a series.</span></span> <span data-ttu-id="2c92e-188">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="2c92e-188">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="2c92e-189">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-189">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="2c92e-190">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="2c92e-190">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2c92e-191">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-191">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="2c92e-192">运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-192">Run the app.</span></span>
> * <span data-ttu-id="2c92e-193">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-193">Examine the project files.</span></span>

<span data-ttu-id="2c92e-194">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="2c92e-194">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="2c92e-196">系统必备</span><span class="sxs-lookup"><span data-stu-id="2c92e-196">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c92e-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c92e-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c92e-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c92e-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c92e-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c92e-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="2c92e-200">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="2c92e-200">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c92e-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c92e-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c92e-202">从 Visual Studio“文件”菜单中，选择“新建”>“项目”    。</span><span class="sxs-lookup"><span data-stu-id="2c92e-202">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="2c92e-203">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-203">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="2c92e-205">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-205">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="2c92e-206">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-206">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="2c92e-208">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="2c92e-208">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="2c92e-210">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="2c92e-210">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c92e-212">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c92e-212">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="2c92e-213">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-213">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="2c92e-214">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-214">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="2c92e-215">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c92e-215">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="2c92e-216">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-216">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="2c92e-217">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="2c92e-217">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="2c92e-218">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="2c92e-218">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="2c92e-219">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="2c92e-219">Select **Yes**.</span></span>

  <span data-ttu-id="2c92e-220">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="2c92e-220">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c92e-221">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c92e-221">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2c92e-222">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="2c92e-222">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="2c92e-223">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="2c92e-223">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="2c92e-224">打开项目</span><span class="sxs-lookup"><span data-stu-id="2c92e-224">Open the project</span></span>

<span data-ttu-id="2c92e-225">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-225">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="2c92e-226">运行应用</span><span class="sxs-lookup"><span data-stu-id="2c92e-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2c92e-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2c92e-227">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2c92e-228">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c92e-228">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="2c92e-229">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2c92e-230">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-230">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2c92e-231">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2c92e-231">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="2c92e-232">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c92e-232">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="2c92e-233">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="2c92e-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="2c92e-234">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-234">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2c92e-235">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-235">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2c92e-237">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="2c92e-237">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2c92e-239">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2c92e-239">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="2c92e-240">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c92e-240">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2c92e-241">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-241">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="2c92e-242">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-242">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2c92e-243">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2c92e-243">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2c92e-244">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="2c92e-244">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="2c92e-245">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-245">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2c92e-246">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-246">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="2c92e-248">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="2c92e-248">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2c92e-250">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2c92e-250">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2c92e-251">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2c92e-251">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="2c92e-252">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="2c92e-252">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="2c92e-253">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-253">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="2c92e-254">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="2c92e-254">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="2c92e-256">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="2c92e-256">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="2c92e-258">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="2c92e-258">Examine the project files</span></span>

<span data-ttu-id="2c92e-259">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="2c92e-259">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="2c92e-260">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="2c92e-260">Pages folder</span></span>

<span data-ttu-id="2c92e-261">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-261">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="2c92e-262">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="2c92e-262">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="2c92e-263">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-263">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="2c92e-264">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-264">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="2c92e-265">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="2c92e-265">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="2c92e-266">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="2c92e-266">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="2c92e-267">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="2c92e-267">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="2c92e-268">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-268">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="2c92e-269">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="2c92e-269">wwwroot folder</span></span>

<span data-ttu-id="2c92e-270">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="2c92e-270">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="2c92e-271">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-271">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="2c92e-272">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="2c92e-272">appSettings.json</span></span>

<span data-ttu-id="2c92e-273">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="2c92e-273">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="2c92e-274">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-274">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="2c92e-275">Program.cs</span><span class="sxs-lookup"><span data-stu-id="2c92e-275">Program.cs</span></span>

<span data-ttu-id="2c92e-276">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="2c92e-276">Contains the entry point for the program.</span></span> <span data-ttu-id="2c92e-277">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-277">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="2c92e-278">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="2c92e-278">Startup.cs</span></span>

<span data-ttu-id="2c92e-279">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="2c92e-279">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="2c92e-280">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="2c92e-280">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="2c92e-281">其他资源</span><span class="sxs-lookup"><span data-stu-id="2c92e-281">Additional resources</span></span>

* [<span data-ttu-id="2c92e-282">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="2c92e-282">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="2c92e-283">后续步骤</span><span class="sxs-lookup"><span data-stu-id="2c92e-283">Next steps</span></span>

<span data-ttu-id="2c92e-284">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="2c92e-284">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2c92e-285">添加模型</span><span class="sxs-lookup"><span data-stu-id="2c92e-285">Add a model</span></span>](xref:tutorials/razor-pages/model)

::: moniker-end