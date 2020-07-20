---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: d4eb1744b1186704603430584b3da0793f90ee49
ms.sourcegitcommit: 50e7c970f327dbe92d45eaf4c21caa001c9106d0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/10/2020
ms.locfileid: "86213091"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="95ad4-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="95ad4-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="95ad4-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="95ad4-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="95ad4-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="95ad4-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="95ad4-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="95ad4-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="95ad4-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="95ad4-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95ad4-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-108">Create a web app.</span></span>
> * <span data-ttu-id="95ad4-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="95ad4-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="95ad4-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="95ad4-110">Work with a database.</span></span>
> * <span data-ttu-id="95ad4-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="95ad4-111">Add search and validation.</span></span>

<span data-ttu-id="95ad4-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="95ad4-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="95ad4-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ad4-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ad4-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ad4-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ad4-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ad4-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="95ad4-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="95ad4-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="95ad4-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ad4-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ad4-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95ad4-119">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="95ad4-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="95ad4-120">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="95ad4-122">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="95ad4-123">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="95ad4-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="95ad4-125">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="95ad4-126">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="95ad4-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="95ad4-127">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="95ad4-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="95ad4-128">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="95ad4-129">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="95ad4-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ad4-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ad4-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95ad4-131">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="95ad4-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="95ad4-132">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="95ad4-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="95ad4-133">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="95ad4-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="95ad4-134">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="95ad4-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="95ad4-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="95ad4-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="95ad4-136">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="95ad4-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="95ad4-137">选择“是”</span><span class="sxs-lookup"><span data-stu-id="95ad4-137">Select **Yes**</span></span>

  * <span data-ttu-id="95ad4-138">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="95ad4-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="95ad4-139">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="95ad4-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ad4-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="95ad4-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="95ad4-141">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-141">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="95ad4-143">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="95ad4-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="95ad4-144">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="95ad4-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="95ad4-146">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="95ad4-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="95ad4-147">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="95ad4-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="95ad4-148">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="95ad4-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="95ad4-149">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="95ad4-149">Select **Next**.</span></span>

* <span data-ttu-id="95ad4-150">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="95ad4-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="95ad4-152">运行应用</span><span class="sxs-lookup"><span data-stu-id="95ad4-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ad4-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ad4-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95ad4-154">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="95ad4-155">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="95ad4-156">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="95ad4-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="95ad4-157">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="95ad4-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="95ad4-158">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="95ad4-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="95ad4-159">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="95ad4-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="95ad4-160">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="95ad4-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="95ad4-161">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="95ad4-163">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="95ad4-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="95ad4-165">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-165">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ad4-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ad4-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95ad4-168">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="95ad4-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="95ad4-169">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="95ad4-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="95ad4-170">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="95ad4-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="95ad4-171">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="95ad4-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="95ad4-172">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="95ad4-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="95ad4-173">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="95ad4-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="95ad4-174">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="95ad4-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ad4-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="95ad4-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="95ad4-177">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="95ad4-178">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="95ad4-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="95ad4-179">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="95ad4-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="95ad4-180">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="95ad4-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="95ad4-181">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="95ad4-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="95ad4-182">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="95ad4-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="95ad4-183">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="95ad4-184">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-184">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="95ad4-186">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="95ad4-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="95ad4-187">下一页</span><span class="sxs-lookup"><span data-stu-id="95ad4-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="95ad4-188">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="95ad4-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="95ad4-189">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="95ad4-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="95ad4-190">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="95ad4-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="95ad4-191">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-191">Create a web app.</span></span>
> * <span data-ttu-id="95ad4-192">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="95ad4-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="95ad4-193">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="95ad4-193">Work with a database.</span></span>
> * <span data-ttu-id="95ad4-194">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="95ad4-194">Add search and validation.</span></span>

<span data-ttu-id="95ad4-195">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="95ad4-196">先决条件</span><span class="sxs-lookup"><span data-stu-id="95ad4-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ad4-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ad4-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ad4-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ad4-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ad4-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="95ad4-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="95ad4-200">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="95ad4-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ad4-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ad4-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="95ad4-202">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="95ad4-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="95ad4-203">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="95ad4-205">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="95ad4-206">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="95ad4-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="95ad4-208">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="95ad4-209">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="95ad4-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="95ad4-210">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="95ad4-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="95ad4-211">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="95ad4-212">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ad4-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ad4-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95ad4-214">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="95ad4-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="95ad4-215">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="95ad4-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="95ad4-216">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="95ad4-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="95ad4-217">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="95ad4-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="95ad4-218">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="95ad4-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="95ad4-219">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="95ad4-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="95ad4-220">选择“是”</span><span class="sxs-lookup"><span data-stu-id="95ad4-220">Select **Yes**</span></span>

  * <span data-ttu-id="95ad4-221">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="95ad4-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="95ad4-222">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="95ad4-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ad4-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="95ad4-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="95ad4-224">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-224">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="95ad4-226">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="95ad4-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="95ad4-227">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="95ad4-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="95ad4-228">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="95ad4-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="95ad4-229">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="95ad4-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="95ad4-230">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="95ad4-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="95ad4-231">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="95ad4-231">Select **Next**.</span></span>

* <span data-ttu-id="95ad4-232">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="95ad4-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="95ad4-233">运行应用</span><span class="sxs-lookup"><span data-stu-id="95ad4-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="95ad4-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="95ad4-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="95ad4-235">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="95ad4-236">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="95ad4-237">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="95ad4-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="95ad4-238">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="95ad4-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="95ad4-239">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="95ad4-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="95ad4-240">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="95ad4-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="95ad4-241">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="95ad4-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="95ad4-242">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="95ad4-244">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="95ad4-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="95ad4-246">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="95ad4-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="95ad4-247">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="95ad4-247">This app doesn't track personal information.</span></span> <span data-ttu-id="95ad4-248">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="95ad4-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="95ad4-250">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-250">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="95ad4-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="95ad4-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="95ad4-253">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="95ad4-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="95ad4-254">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="95ad4-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="95ad4-255">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="95ad4-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="95ad4-256">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="95ad4-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="95ad4-257">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="95ad4-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="95ad4-258">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="95ad4-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="95ad4-259">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="95ad4-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="95ad4-260">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="95ad4-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="95ad4-261">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="95ad4-261">This app doesn't track personal information.</span></span> <span data-ttu-id="95ad4-262">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="95ad4-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="95ad4-264">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-264">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="95ad4-266">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="95ad4-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="95ad4-267">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="95ad4-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="95ad4-268">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="95ad4-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="95ad4-269">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="95ad4-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="95ad4-270">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="95ad4-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="95ad4-271">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="95ad4-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="95ad4-272">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="95ad4-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="95ad4-273">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="95ad4-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="95ad4-274">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="95ad4-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="95ad4-275">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="95ad4-275">This app doesn't track personal information.</span></span> <span data-ttu-id="95ad4-276">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="95ad4-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="95ad4-278">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="95ad4-278">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="95ad4-280">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="95ad4-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="95ad4-281">下一页</span><span class="sxs-lookup"><span data-stu-id="95ad4-281">Next</span></span>](adding-controller.md)

::: moniker-end
