---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
no-loc:
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 3df00af8e6bca0dbf2d7871f383dd67b465aa5da
ms.sourcegitcommit: 497be502426e9d90bb7d0401b1b9f74b6a384682
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/08/2020
ms.locfileid: "88021167"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="a38d8-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="a38d8-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="a38d8-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="a38d8-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="a38d8-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a38d8-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="a38d8-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="a38d8-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="a38d8-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="a38d8-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a38d8-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-108">Create a web app.</span></span>
> * <span data-ttu-id="a38d8-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="a38d8-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="a38d8-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="a38d8-110">Work with a database.</span></span>
> * <span data-ttu-id="a38d8-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="a38d8-111">Add search and validation.</span></span>

<span data-ttu-id="a38d8-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="a38d8-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="a38d8-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a38d8-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38d8-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a38d8-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a38d8-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a38d8-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a38d8-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="a38d8-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="a38d8-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a38d8-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38d8-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a38d8-119">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="a38d8-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="a38d8-120">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="a38d8-122">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="a38d8-123">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="a38d8-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="a38d8-125">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="a38d8-126">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="a38d8-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="a38d8-127">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="a38d8-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="a38d8-128">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="a38d8-129">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="a38d8-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a38d8-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a38d8-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a38d8-131">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="a38d8-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="a38d8-132">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="a38d8-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="a38d8-133">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="a38d8-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a38d8-134">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a38d8-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="a38d8-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="a38d8-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="a38d8-136">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="a38d8-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="a38d8-137">选择“是”</span><span class="sxs-lookup"><span data-stu-id="a38d8-137">Select **Yes**</span></span>

  * <span data-ttu-id="a38d8-138">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="a38d8-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="a38d8-139">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="a38d8-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a38d8-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a38d8-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a38d8-141">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-141">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a38d8-143">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="a38d8-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="a38d8-144">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="a38d8-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="a38d8-146">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="a38d8-146">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a38d8-147">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="a38d8-147">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a38d8-148">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="a38d8-148">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="a38d8-149">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a38d8-149">Select **Next**.</span></span>

* <span data-ttu-id="a38d8-150">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="a38d8-150">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="a38d8-152">运行应用</span><span class="sxs-lookup"><span data-stu-id="a38d8-152">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a38d8-153">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38d8-153">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a38d8-154">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-154">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="a38d8-155">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-155">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a38d8-156">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="a38d8-156">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a38d8-157">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a38d8-157">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a38d8-158">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="a38d8-158">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="a38d8-159">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="a38d8-159">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a38d8-160">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="a38d8-160">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="a38d8-161">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-161">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="a38d8-163">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="a38d8-163">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="a38d8-165">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-165">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a38d8-167">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a38d8-167">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a38d8-168">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="a38d8-168">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="a38d8-169">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a38d8-169">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="a38d8-170">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a38d8-170">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="a38d8-171">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a38d8-171">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a38d8-172">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="a38d8-172">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="a38d8-173">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="a38d8-173">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a38d8-174">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="a38d8-174">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a38d8-176">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a38d8-176">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a38d8-177">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-177">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="a38d8-178">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="a38d8-178">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a38d8-179">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a38d8-179">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a38d8-180">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a38d8-180">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a38d8-181">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="a38d8-181">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="a38d8-182">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="a38d8-182">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="a38d8-183">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-183">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="a38d8-184">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-184">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="a38d8-186">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="a38d8-186">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a38d8-187">下一页</span><span class="sxs-lookup"><span data-stu-id="a38d8-187">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="a38d8-188">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="a38d8-188">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="a38d8-189">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="a38d8-189">The app manages a database of movie titles.</span></span> <span data-ttu-id="a38d8-190">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="a38d8-190">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="a38d8-191">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-191">Create a web app.</span></span>
> * <span data-ttu-id="a38d8-192">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="a38d8-192">Add and scaffold a model.</span></span>
> * <span data-ttu-id="a38d8-193">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="a38d8-193">Work with a database.</span></span>
> * <span data-ttu-id="a38d8-194">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="a38d8-194">Add search and validation.</span></span>

<span data-ttu-id="a38d8-195">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-195">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="a38d8-196">先决条件</span><span class="sxs-lookup"><span data-stu-id="a38d8-196">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a38d8-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38d8-197">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="a38d8-198">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a38d8-198">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a38d8-199">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a38d8-199">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="a38d8-200">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="a38d8-200">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a38d8-201">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38d8-201">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="a38d8-202">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="a38d8-202">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="a38d8-203">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-203">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="a38d8-205">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-205">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="a38d8-206">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="a38d8-206">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="a38d8-208">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-208">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="a38d8-209">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="a38d8-209">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="a38d8-210">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="a38d8-210">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="a38d8-211">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-211">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="a38d8-212">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-212">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="a38d8-213">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a38d8-213">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a38d8-214">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="a38d8-214">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="a38d8-215">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="a38d8-215">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="a38d8-216">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="a38d8-216">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="a38d8-217">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="a38d8-217">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="a38d8-218">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="a38d8-218">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="a38d8-219">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="a38d8-219">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="a38d8-220">选择“是”</span><span class="sxs-lookup"><span data-stu-id="a38d8-220">Select **Yes**</span></span>

  * <span data-ttu-id="a38d8-221">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="a38d8-221">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="a38d8-222">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="a38d8-222">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a38d8-223">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a38d8-223">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="a38d8-224">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-224">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="a38d8-226">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="a38d8-226">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="a38d8-227">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="a38d8-227">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="a38d8-228">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="a38d8-228">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="a38d8-229">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="a38d8-229">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="a38d8-230">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="a38d8-230">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="a38d8-231">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="a38d8-231">Select **Next**.</span></span>

* <span data-ttu-id="a38d8-232">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="a38d8-232">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="a38d8-233">运行应用</span><span class="sxs-lookup"><span data-stu-id="a38d8-233">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="a38d8-234">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="a38d8-234">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="a38d8-235">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-235">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="a38d8-236">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-236">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="a38d8-237">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="a38d8-237">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a38d8-238">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a38d8-238">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a38d8-239">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="a38d8-239">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="a38d8-240">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="a38d8-240">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a38d8-241">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="a38d8-241">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="a38d8-242">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-242">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="a38d8-244">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="a38d8-244">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="a38d8-246">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="a38d8-246">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="a38d8-247">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="a38d8-247">This app doesn't track personal information.</span></span> <span data-ttu-id="a38d8-248">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="a38d8-248">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="a38d8-250">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-250">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="a38d8-252">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="a38d8-252">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="a38d8-253">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="a38d8-253">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="a38d8-254">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="a38d8-254">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="a38d8-255">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a38d8-255">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="a38d8-256">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a38d8-256">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="a38d8-257">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="a38d8-257">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="a38d8-258">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="a38d8-258">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="a38d8-259">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="a38d8-259">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="a38d8-260">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="a38d8-260">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="a38d8-261">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="a38d8-261">This app doesn't track personal information.</span></span> <span data-ttu-id="a38d8-262">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="a38d8-262">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="a38d8-264">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-264">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="a38d8-266">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="a38d8-266">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="a38d8-267">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="a38d8-267">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="a38d8-268">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="a38d8-268">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="a38d8-269">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="a38d8-269">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="a38d8-270">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="a38d8-270">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="a38d8-271">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="a38d8-271">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="a38d8-272">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="a38d8-272">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="a38d8-273">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="a38d8-273">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="a38d8-274">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="a38d8-274">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="a38d8-275">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="a38d8-275">This app doesn't track personal information.</span></span> <span data-ttu-id="a38d8-276">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="a38d8-276">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="a38d8-278">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="a38d8-278">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="a38d8-280">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="a38d8-280">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="a38d8-281">下一页</span><span class="sxs-lookup"><span data-stu-id="a38d8-281">Next</span></span>](adding-controller.md)

::: moniker-end
