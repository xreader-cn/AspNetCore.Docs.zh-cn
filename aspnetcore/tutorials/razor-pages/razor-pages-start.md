---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 81a2a76fc1cecc78b69226fe714d7c9272b04bf7
ms.sourcegitcommit: 24b1f6decbb17bb22a45166e5fdb0845c65af498
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/27/2019
ms.locfileid: "56899185"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="5a0f7-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="5a0f7-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="5a0f7-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="5a0f7-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="5a0f7-106">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="5a0f7-107">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="5a0f7-108">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="5a0f7-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a0f7-110">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="5a0f7-111">运行应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-111">Run the app.</span></span>
> * <span data-ttu-id="5a0f7-112">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-112">Examine the project files.</span></span>

<span data-ttu-id="5a0f7-113">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="5a0f7-115">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="5a0f7-115">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a0f7-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f7-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f7-117">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="5a0f7-118">创建新的 ASP.NET Core Web 应用呈现。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="5a0f7-119">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="5a0f7-120">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="5a0f7-122">在下拉列表中选择“ASP.NET Core 2.2”，然后选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="5a0f7-124">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-124">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a0f7-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f7-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f7-127">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="5a0f7-128">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="5a0f7-129">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="5a0f7-130">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="5a0f7-131">`code` 命令将在 Visual Studio Code 的新实例中打开 RazorPagesMovie 文件夹。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="5a0f7-132">一个对话框随即出现，其中包含“"RazorPagesMovie" 中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="5a0f7-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="5a0f7-133">选择“是”</span><span class="sxs-lookup"><span data-stu-id="5a0f7-133">Select **Yes**</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a0f7-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5a0f7-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a0f7-135">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-135">From a terminal, run the following commands:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
cd RazorPagesMovie
```

<span data-ttu-id="5a0f7-136">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="5a0f7-137">打开项目</span><span class="sxs-lookup"><span data-stu-id="5a0f7-137">Open the project</span></span>

<span data-ttu-id="5a0f7-138">在 Visual Studio 中，选择“文件”>“打开”，然后选择“RazorPagesMovie.csproj”文件。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="5a0f7-139">运行应用</span><span class="sxs-lookup"><span data-stu-id="5a0f7-139">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="5a0f7-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="5a0f7-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="5a0f7-141">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="5a0f7-142">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="5a0f7-143">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a0f7-144">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="5a0f7-145">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="5a0f7-146">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="5a0f7-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="5a0f7-147">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="5a0f7-148">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-148">Press **Ctrl-F5** to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="5a0f7-149">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-149">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="5a0f7-150">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-150">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="5a0f7-151">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-151">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="5a0f7-152">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-152">Localhost only serves web requests from the local computer.</span></span>
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="5a0f7-153">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="5a0f7-153">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="5a0f7-154">选择“运行”>“开始执行(不调试)”以启动应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-154">Select **Run > Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="5a0f7-155">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-155">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

<!-- End of VS tabs -->

---

* <span data-ttu-id="5a0f7-156">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-156">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="5a0f7-157">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-157">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="5a0f7-159">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-159">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="examine-the-project-files"></a><span data-ttu-id="5a0f7-161">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="5a0f7-161">Examine the project files</span></span>

<span data-ttu-id="5a0f7-162">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-162">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="5a0f7-163">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="5a0f7-163">Pages folder</span></span>

<span data-ttu-id="5a0f7-164">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-164">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="5a0f7-165">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-165">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="5a0f7-166">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-166">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="5a0f7-167">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-167">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="5a0f7-168">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-168">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="5a0f7-169">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-169">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="5a0f7-170">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-170">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="5a0f7-171">有关更多信息，请参见<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-171">For more information, see <xref:mvc/views/layout>.</span></span>


### <a name="wwwroot-folder"></a><span data-ttu-id="5a0f7-172">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="5a0f7-172">wwwroot folder</span></span>

<span data-ttu-id="5a0f7-173">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-173">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="5a0f7-174">有关更多信息，请参见<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-174">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="5a0f7-175">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="5a0f7-175">appSettings.json</span></span>

<span data-ttu-id="5a0f7-176">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-176">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="5a0f7-177">有关更多信息，请参见<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-177">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="5a0f7-178">Program.cs</span><span class="sxs-lookup"><span data-stu-id="5a0f7-178">Program.cs</span></span>

<span data-ttu-id="5a0f7-179">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-179">Contains the entry point for the program.</span></span> <span data-ttu-id="5a0f7-180">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-180">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="5a0f7-181">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="5a0f7-181">Startup.cs</span></span>

<span data-ttu-id="5a0f7-182">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-182">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="5a0f7-183">有关更多信息，请参见<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-183">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="next-steps"></a><span data-ttu-id="5a0f7-184">后续步骤</span><span class="sxs-lookup"><span data-stu-id="5a0f7-184">Next steps</span></span>

<span data-ttu-id="5a0f7-185">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-185">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="5a0f7-186">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-186">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="5a0f7-187">运行应用。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-187">Ran the app.</span></span>
> * <span data-ttu-id="5a0f7-188">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="5a0f7-188">Examined the project files.</span></span>

<span data-ttu-id="5a0f7-189">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="5a0f7-189">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="5a0f7-190">添加模型</span><span class="sxs-lookup"><span data-stu-id="5a0f7-190">Add a model</span></span>](xref:tutorials/razor-pages/model)
