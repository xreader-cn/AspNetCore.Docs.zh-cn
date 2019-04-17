---
title: 教程：开始使用ASP.NET Core中的Razor Pages
author: rick-anderson
description: 此系列教程演示了如何在 ASP.NET Core 中使用 Razor Pages。 了解如何创建模型、为 Razor Pages 生成代码、将 Entity Framework Core 和 SQL Server 用于数据访问、添加搜索功能、添加输入验证及使用迁移更新模型。
ms.author: riande
ms.date: 12/5/2018
uid: tutorials/razor-pages/razor-pages-start
ms.openlocfilehash: 1d264ca4a605d8291e273a8f054c92e7eefa5548
ms.sourcegitcommit: 948e533e02c2a7cb6175ada20b2c9cabb7786d0b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/10/2019
ms.locfileid: "59468842"
---
# <a name="tutorial-get-started-with-razor-pages-in-aspnet-core"></a><span data-ttu-id="0c6d0-104">教程：开始使用ASP.NET Core中的Razor Pages</span><span class="sxs-lookup"><span data-stu-id="0c6d0-104">Tutorial: Get started with Razor Pages in ASP.NET Core</span></span>

<span data-ttu-id="0c6d0-105">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="0c6d0-105">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

<span data-ttu-id="0c6d0-106">这是系列中的第一个教程。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-106">This is the first tutorial of a series.</span></span> <span data-ttu-id="0c6d0-107">[本系列](xref:tutorials/razor-pages/index)介绍构建 ASP.NET Core Razor Pages Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-107">[The series](xref:tutorials/razor-pages/index) teaches the basics of building an ASP.NET Core Razor Pages web app.</span></span>

[!INCLUDE[](~/includes/advancedRP.md)]

<span data-ttu-id="0c6d0-108">在本系列结束时，你将拥有一个管理电影数据库的应用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-108">At the end of the series you'll have an app that manages a database of movies.</span></span>  

[!INCLUDE[View or download sample code](~/includes/rp/download.md)]

<span data-ttu-id="0c6d0-109">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-109">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0c6d0-110">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-110">Create a Razor Pages web app.</span></span>
> * <span data-ttu-id="0c6d0-111">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-111">Run the app.</span></span>
> * <span data-ttu-id="0c6d0-112">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-112">Examine the project files.</span></span>

<span data-ttu-id="0c6d0-113">在本教程结束时，你将有一个工作的 Razor Pages Web 应用。在后续教程中，你可以在其基础上进行构建。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-113">At the end of this tutorial you'll have a working Razor Pages web app that you'll build on in later tutorials.</span></span>

![主页或索引页](razor-pages-start/_static/home2.2.png)

[!INCLUDE[](~/includes/net-core-prereqs-all-2.2.md)]

## <a name="create-a-razor-pages-web-app"></a><span data-ttu-id="0c6d0-115">创建 Razor Pages Web 应用</span><span class="sxs-lookup"><span data-stu-id="0c6d0-115">Create a Razor Pages web app</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="0c6d0-116">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c6d0-116">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c6d0-117">从 Visual Studio“文件”菜单中选择“新建” > “项目”。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-117">From the Visual Studio **File** menu, select **New** > **Project**.</span></span>

* <span data-ttu-id="0c6d0-118">创建新的 ASP.NET Core Web 应用呈现。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-118">Create a new ASP.NET Core Web Application.</span></span> <span data-ttu-id="0c6d0-119">将项目命名为“RazorPagesMovie”。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-119">Name the project **RazorPagesMovie**.</span></span> <span data-ttu-id="0c6d0-120">将项目命名为“RazorPagesMovie”非常重要，这样在复制和粘贴代码时命名空间就会匹配。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-120">It's important to name the project *RazorPagesMovie* so the namespaces will match when you copy and paste code.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2.1.png)

* <span data-ttu-id="0c6d0-122">在下拉列表中选择“ASP.NET Core 2.2”，然后选择“Web 应用程序”。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-122">Select **ASP.NET Core 2.2** in the dropdown, and then select **Web Application**.</span></span>

  ![新建 ASP.NET Core Web 应用程序](razor-pages-start/_static/np_2_2.2.png)

  <span data-ttu-id="0c6d0-124">创建以下初学者项目：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-124">The following starter project is created:</span></span>

  ![“解决方案资源管理器”](razor-pages-start/_static/se2.2.png)

# [<a name="visual-studio-code"></a><span data-ttu-id="0c6d0-126">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c6d0-126">Visual Studio Code</span></span>](#tab/visual-studio-code)

* <span data-ttu-id="0c6d0-127">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-127">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>

* <span data-ttu-id="0c6d0-128">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-128">Change directories (`cd`) to a folder which will contain the project.</span></span>

* <span data-ttu-id="0c6d0-129">运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-129">Run the following commands:</span></span>

  ```console
  dotnet new webapp -o RazorPagesMovie
  code -r RazorPagesMovie
  ```

  * <span data-ttu-id="0c6d0-130">`dotnet new` 命令可在 RazorPagesMovie 文件夹中创建新的 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-130">The `dotnet new` command creates a new Razor Pages project in the *RazorPagesMovie* folder.</span></span>
  * <span data-ttu-id="0c6d0-131">`code` 命令将在 Visual Studio Code 的新实例中打开 RazorPagesMovie 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-131">The `code` command opens the *RazorPagesMovie* folder in a new instance of Visual Studio Code.</span></span>

  <span data-ttu-id="0c6d0-132">一个对话框随即出现，其中包含“"RazorPagesMovie" 中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="0c6d0-132">A dialog box appears with **Required assets to build and debug are missing from 'RazorPagesMovie'. Add them?**</span></span>

* <span data-ttu-id="0c6d0-133">选择“是”</span><span class="sxs-lookup"><span data-stu-id="0c6d0-133">Select **Yes**</span></span>

# [<a name="visual-studio-for-mac"></a><span data-ttu-id="0c6d0-134">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0c6d0-134">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="0c6d0-135">在终端中运行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-135">From a terminal, run the following command:</span></span>

<!-- TODO: update these instruction once mac support 2.2 projects -->

```console
dotnet new webapp -o RazorPagesMovie
```

<span data-ttu-id="0c6d0-136">上述命令使用 [.NET Core CLI](/dotnet/core/tools/dotnet) 创建 Razor Pages 项目。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-136">The preceding commands use the [.NET Core CLI](/dotnet/core/tools/dotnet) to create a Razor Pages project.</span></span>

## <a name="open-the-project"></a><span data-ttu-id="0c6d0-137">打开项目</span><span class="sxs-lookup"><span data-stu-id="0c6d0-137">Open the project</span></span>

<span data-ttu-id="0c6d0-138">在 Visual Studio 中，选择“文件”>“打开”，然后选择“RazorPagesMovie.csproj”文件。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-138">From Visual Studio, select **File > Open**, and then select the *RazorPagesMovie.csproj* file.</span></span>

<!-- End of VS tabs -->

---

## <a name="run-the-app"></a><span data-ttu-id="0c6d0-139">运行应用</span><span class="sxs-lookup"><span data-stu-id="0c6d0-139">Run the app</span></span>

# [<a name="visual-studio"></a><span data-ttu-id="0c6d0-140">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c6d0-140">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="0c6d0-141">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-141">Press Ctrl+F5 to run without the debugger.</span></span>

  [!INCLUDE[](~/includes/trustCertVS.md)]

  <span data-ttu-id="0c6d0-142">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-142">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="0c6d0-143">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-143">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0c6d0-144">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-144">That's because `localhost` is the standard hostname for the local computer.</span></span> <span data-ttu-id="0c6d0-145">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-145">Localhost only serves web requests from the local computer.</span></span> <span data-ttu-id="0c6d0-146">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-146">When Visual Studio creates a web project, a random port is used for the web server.</span></span>

* <span data-ttu-id="0c6d0-147">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-147">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0c6d0-148">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-148">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="0c6d0-150">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-150">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# [<a name="visual-studio-code"></a><span data-ttu-id="0c6d0-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c6d0-152">Visual Studio Code</span></span>](#tab/visual-studio-code)

  [!INCLUDE[](~/includes/trustCertVSC.md)]

* <span data-ttu-id="0c6d0-153">按 **Ctrl-F5** 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-153">Press **Ctrl-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0c6d0-154">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-154">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span> <span data-ttu-id="0c6d0-155">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-155">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="0c6d0-156">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-156">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="0c6d0-157">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-157">Localhost only serves web requests from the local computer.</span></span>

* <span data-ttu-id="0c6d0-158">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-158">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0c6d0-159">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-159">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2.png)

  <span data-ttu-id="0c6d0-161">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-161">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2.png)
  
# [<a name="visual-studio-for-mac"></a><span data-ttu-id="0c6d0-163">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0c6d0-163">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

  [!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="0c6d0-164">按 Cmd-Opt-F5，以在不使用调试器的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-164">Press **Cmd-Opt-F5** to run without the debugger.</span></span>

  <span data-ttu-id="0c6d0-165">Visual Studio 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `http://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-165">Visual Studio starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `http://localhost:5001`.</span></span>

* <span data-ttu-id="0c6d0-166">在应用的主页上，选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-166">On the app's home page, select **Accept** to consent to tracking.</span></span>

  <span data-ttu-id="0c6d0-167">此应用不会跟踪个人信息，但项目模板包括许可功能，以防需要它来符合欧盟的[一般数据保护条例 (GDPR)](xref:security/gdpr)。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-167">This app doesn't track personal information, but the project template includes the consent feature in case you need it to comply with the European Union's [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](razor-pages-start/_static/homeGDPR2.2_safari.png)

  <span data-ttu-id="0c6d0-169">下图展示了同意跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-169">The following image shows the app after you give consent to tracking:</span></span>

  ![主页或索引页](razor-pages-start/_static/home2.2_safari.png)

<!-- End of VS tabs -->

---

## <a name="examine-the-project-files"></a><span data-ttu-id="0c6d0-171">检查项目文件</span><span class="sxs-lookup"><span data-stu-id="0c6d0-171">Examine the project files</span></span>

<span data-ttu-id="0c6d0-172">下面是主项目文件夹和文件的概述，将在后续教程中使用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-172">Here's an overview of the main project folders and files that you'll work with in later tutorials.</span></span>

### <a name="pages-folder"></a><span data-ttu-id="0c6d0-173">Pages 文件夹</span><span class="sxs-lookup"><span data-stu-id="0c6d0-173">Pages folder</span></span>

<span data-ttu-id="0c6d0-174">包含 Razor 页面和支持文件。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-174">Contains Razor pages and supporting files.</span></span> <span data-ttu-id="0c6d0-175">每个 Razor 页面都是一对文件：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-175">Each Razor page is a pair of files:</span></span>

* <span data-ttu-id="0c6d0-176">一个 .cshtml 文件，其中包含使用 Razor 语法的 C＃ 代码的 HTML 标记。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-176">A *.cshtml* file that contains HTML markup with C# code using Razor syntax.</span></span>
* <span data-ttu-id="0c6d0-177">一个 .cshtml.cs 文件，其中包含处理页面事件的 C# 代码。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-177">A *.cshtml.cs* file that contains C# code that handles page events.</span></span>

<span data-ttu-id="0c6d0-178">支持文件的名称以下划线开头。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-178">Supporting files have names that begin with an underscore.</span></span> <span data-ttu-id="0c6d0-179">例如，_Layout.cshtml 文件可配置所有页面通用的 UI 元素。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-179">For example, the *_Layout.cshtml* file configures UI elements common to all pages.</span></span> <span data-ttu-id="0c6d0-180">此文件设置页面顶部的导航菜单和页面底部的版权声明。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-180">This file sets up the navigation menu at the top of the page and the copyright notice at the bottom of the page.</span></span> <span data-ttu-id="0c6d0-181">有关更多信息，请参见<xref:mvc/views/layout>。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-181">For more information, see <xref:mvc/views/layout>.</span></span>

### <a name="wwwroot-folder"></a><span data-ttu-id="0c6d0-182">wwwroot 文件夹</span><span class="sxs-lookup"><span data-stu-id="0c6d0-182">wwwroot folder</span></span>

<span data-ttu-id="0c6d0-183">包含静态文件，如 HTML 文件、JavaScript 文件和 CSS 文件。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-183">Contains static files, such as HTML files, JavaScript files, and CSS files.</span></span> <span data-ttu-id="0c6d0-184">有关更多信息，请参见<xref:fundamentals/static-files>。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-184">For more information, see <xref:fundamentals/static-files>.</span></span>

### <a name="appsettingsjson"></a><span data-ttu-id="0c6d0-185">appSettings.json</span><span class="sxs-lookup"><span data-stu-id="0c6d0-185">appSettings.json</span></span>

<span data-ttu-id="0c6d0-186">包含配置数据，如连接字符串。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-186">Contains configuration data, such as connection strings.</span></span> <span data-ttu-id="0c6d0-187">有关更多信息，请参见<xref:fundamentals/configuration/index>。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-187">For more information, see <xref:fundamentals/configuration/index>.</span></span>

### <a name="programcs"></a><span data-ttu-id="0c6d0-188">Program.cs</span><span class="sxs-lookup"><span data-stu-id="0c6d0-188">Program.cs</span></span>

<span data-ttu-id="0c6d0-189">包含程序的入口点。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-189">Contains the entry point for the program.</span></span> <span data-ttu-id="0c6d0-190">有关更多信息，请参见<xref:fundamentals/host/web-host>。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-190">For more information, see <xref:fundamentals/host/web-host>.</span></span>

### <a name="startupcs"></a><span data-ttu-id="0c6d0-191">Startup.cs</span><span class="sxs-lookup"><span data-stu-id="0c6d0-191">Startup.cs</span></span>

<span data-ttu-id="0c6d0-192">包含配置应用行为的代码，例如，是否需要同意 cookie。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-192">Contains code that configures app behavior, such as whether it requires consent for cookies.</span></span> <span data-ttu-id="0c6d0-193">有关更多信息，请参见<xref:fundamentals/startup>。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-193">For more information, see <xref:fundamentals/startup>.</span></span>

## <a name="additional-resources"></a><span data-ttu-id="0c6d0-194">其他资源</span><span class="sxs-lookup"><span data-stu-id="0c6d0-194">Additional resources</span></span>

* [<span data-ttu-id="0c6d0-195">本教程的 YouTube 版本</span><span class="sxs-lookup"><span data-stu-id="0c6d0-195">Youtube version of this tutorial</span></span>](https://www.youtube.com/watch?v=F0SP7Ry4flQ&feature=youtu.be)

## <a name="next-steps"></a><span data-ttu-id="0c6d0-196">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0c6d0-196">Next steps</span></span>

<span data-ttu-id="0c6d0-197">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-197">In this tutorial, you:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="0c6d0-198">创建 Razor Pages Web 应用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-198">Created a Razor Pages web app.</span></span>
> * <span data-ttu-id="0c6d0-199">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-199">Ran the app.</span></span>
> * <span data-ttu-id="0c6d0-200">检查项目文件。</span><span class="sxs-lookup"><span data-stu-id="0c6d0-200">Examined the project files.</span></span>

<span data-ttu-id="0c6d0-201">进入系列的下一教程：</span><span class="sxs-lookup"><span data-stu-id="0c6d0-201">Advance to the next tutorial in the series:</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="0c6d0-202">添加模型</span><span class="sxs-lookup"><span data-stu-id="0c6d0-202">Add a model</span></span>](xref:tutorials/razor-pages/model)
