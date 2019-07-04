---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 6/3/2019
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 207f5a5ab5e21d8cd8f6cf2f63641b94d8077ae5
ms.sourcegitcommit: d6e51c60439f03a8992bda70cc982ddb15d3f100
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/03/2019
ms.locfileid: "67555803"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="af822-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="af822-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="af822-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="af822-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="af822-106">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="af822-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="af822-107">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="af822-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="af822-108">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="af822-108">At the end of the series, you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="af822-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="af822-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="af822-110">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="af822-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="af822-111">运行应用。</span><span class="sxs-lookup"><span data-stu-id="af822-111">Run the app.</span></span>
> * <span data-ttu-id="af822-112">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="af822-112">Examine the project files.</span></span>

<span data-ttu-id="af822-113">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="af822-113">At the end of this tutorial, you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

## <a name="prerequisites"></a><span data-ttu-id="af822-115">系统必备</span><span class="sxs-lookup"><span data-stu-id="af822-115">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="af822-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af822-116">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="af822-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af822-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="af822-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="af822-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="af822-119">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="af822-119">Create a Razor Pages web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="af822-120">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af822-120">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af822-121">从 Visual Studio“文件”菜单中选择“新建” > “项目”    。</span><span class="sxs-lookup"><span data-stu-id="af822-121">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="af822-122">创建新的 ASP.NET Core Web 应用程序，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="af822-122">Create a new ASP.NET Core Web Application and select **Next**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="af822-124">将项目命名为“RazorPagesMovie”  。</span><span class="sxs-lookup"><span data-stu-id="af822-124">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="af822-125">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配  。</span><span class="sxs-lookup"><span data-stu-id="af822-125">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/config.png)

* <span data-ttu-id="af822-127">在下拉列表中选择“ASP.NET Core 2.2”，然后依次选择“Web 应用程序”和“创建”    。</span><span class="sxs-lookup"><span data-stu-id="af822-127">Select **ASP.NET Core 2.2** in the dropdown, **Web Application**, and then select **Create**.</span></span>

![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="af822-129">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="af822-129">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="af822-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af822-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="af822-132">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="af822-132">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="af822-133">更改为将包含项目的目录 (`cd`)。</span><span class="sxs-lookup"><span data-stu-id="af822-133">Change to the directory (`cd`) which will contain the project.</span></span>

* <span data-ttu-id="af822-134">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="af822-134">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="af822-135">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目  。</span><span class="sxs-lookup"><span data-stu-id="af822-135">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="af822-136">`code` 命令在 Visual Studio Code 的当前实例中打开 RazorPagesMovie  文件夹。</span><span class="sxs-lookup"><span data-stu-id="af822-136">The `code` command opens the *RazorPagesMovie* folder in the current instance of Visual Studio Code.</span></span>

* <span data-ttu-id="af822-137">状态栏的 OmniSharp 火焰图标变绿后，对话框将询问“'RazorPagesMovie' 缺少生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="af822-137">After the status bar's OmniSharp flame icon turns green, a dialog asks **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span> <span data-ttu-id="af822-138">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="af822-138">Select **Yes**.</span></span>

  <span data-ttu-id="af822-139">将向项目的根目录添加包含 launch.json 和 tasks.json 文件的 .vscode 目录。   </span><span class="sxs-lookup"><span data-stu-id="af822-139">A *.vscode* directory, containing *launch.json* and *tasks.json* files, is added to the project's root directory.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="af822-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="af822-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="af822-141">从终端运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="af822-141">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="af822-142">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="af822-142">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="af822-143">打开项目</span><span class="sxs-lookup"><span data-stu-id="af822-143">Open the project</span></span>

<span data-ttu-id="af822-144">在 Visual Studio 中，选择“文件”>“打开”  ，然后选择“RazorPagesMovie.csproj”  文件。</span><span class="sxs-lookup"><span data-stu-id="af822-144">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="af822-145">运行应用</span><span class="sxs-lookup"><span data-stu-id="af822-145">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="af822-146">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="af822-146">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="af822-147">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="af822-147">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="af822-148">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="af822-148">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="af822-149">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="af822-149">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="af822-150">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="af822-150">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="af822-151">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="af822-151">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="af822-152">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="af822-152">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="af822-153">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="af822-153">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="af822-154">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="af822-154">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="af822-156">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="af822-156">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="af822-158">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="af822-158">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="af822-159">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="af822-159">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="af822-160">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="af822-160">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="af822-161">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="af822-161">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="af822-162">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="af822-162">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="af822-163">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="af822-163">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="af822-164">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="af822-164">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="af822-165">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="af822-165">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="af822-167">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="af822-167">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="af822-169">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="af822-169">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="af822-170">按 Cmd-Opt-F5  ，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="af822-170">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="af822-171">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="af822-171">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="af822-172">在应用的主页上，选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="af822-172">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="af822-173">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="af822-173">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="af822-175">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="af822-175">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="af822-177">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="af822-177">Examine the project files</span></span>

<span data-ttu-id="af822-178">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="af822-178">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="af822-179">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="af822-179">Pages folder</span></span>

<span data-ttu-id="af822-180">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="af822-180">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="af822-181">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="af822-181">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="af822-182">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记  。</span><span class="sxs-lookup"><span data-stu-id="af822-182">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="af822-183">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码  。</span><span class="sxs-lookup"><span data-stu-id="af822-183">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="af822-184">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="af822-184">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="af822-185">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素  。</span><span class="sxs-lookup"><span data-stu-id="af822-185">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="af822-186">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="af822-186">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="af822-187">有关详细信息，请参阅 <xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="af822-187">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="af822-188">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="af822-188">wwwroot folder</span></span>

<span data-ttu-id="af822-189">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="af822-189">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="af822-190">有关详细信息，请参阅 <xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="af822-190">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="af822-191">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="af822-191">appSettings.json</span></span>

<span data-ttu-id="af822-192">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="af822-192">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="af822-193">有关详细信息，请参阅 <xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="af822-193">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="af822-194">Program.cs</span><span class="sxs-lookup"><span data-stu-id="af822-194">Program.cs</span></span>

<span data-ttu-id="af822-195">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="af822-195">Contains the entry point for the program.</span></span> <span data-ttu-id="af822-196">有关详细信息，请参阅 <xref:fundamentals/host/generic-host>。</span><span class="sxs-lookup"><span data-stu-id="af822-196">For more information, see <xref:fundamentals/host/generic-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="af822-197">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="af822-197">Startup.cs</span></span>

<span data-ttu-id="af822-198">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="af822-198">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="af822-199">有关详细信息，请参阅 <xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="af822-199">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="af822-200">其他资源</span><span class="sxs-lookup"><span data-stu-id="af822-200">Additional resources</span></span>

* [<span data-ttu-id="af822-201">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="af822-201">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="af822-202">后续步骤</span><span class="sxs-lookup"><span data-stu-id="af822-202">Next steps</span></span>

<span data-ttu-id="af822-203">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="af822-203">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="af822-204">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="af822-204">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="af822-205">运行应用。</span><span class="sxs-lookup"><span data-stu-id="af822-205">Ran the app.</span></span>
> * <span data-ttu-id="af822-206">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="af822-206">Examined the project files.</span></span>

<span data-ttu-id="af822-207">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="af822-207">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="af822-208">添加模型</span><span class="sxs-lookup"><span data-stu-id="af822-208">Add a model</span></span>](xref:tutorials/razor-pages/model)
