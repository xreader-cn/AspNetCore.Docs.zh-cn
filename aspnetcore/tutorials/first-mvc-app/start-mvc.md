---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: c2b76b59ae775b9268fa77019bf8420e5e4108b6
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452266"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="709e5-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="709e5-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="709e5-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="709e5-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="709e5-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="709e5-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="709e5-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="709e5-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="709e5-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="709e5-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="709e5-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-108">Create a web app.</span></span>
> * <span data-ttu-id="709e5-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="709e5-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="709e5-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="709e5-110">Work with a database.</span></span>
> * <span data-ttu-id="709e5-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="709e5-111">Add search and validation.</span></span>

<span data-ttu-id="709e5-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="709e5-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="709e5-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="709e5-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="709e5-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="709e5-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="709e5-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="709e5-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="709e5-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="709e5-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="709e5-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="709e5-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="709e5-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="709e5-119">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="709e5-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="709e5-120">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="709e5-122">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="709e5-123">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="709e5-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="709e5-125">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="709e5-126">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="709e5-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="709e5-127">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="709e5-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="709e5-128">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="709e5-129">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="709e5-129">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="709e5-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="709e5-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="709e5-131">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="709e5-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="709e5-132">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="709e5-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="709e5-133">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="709e5-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="709e5-134">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="709e5-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="709e5-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="709e5-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="709e5-136">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="709e5-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="709e5-137">选择“是”</span><span class="sxs-lookup"><span data-stu-id="709e5-137">Select **Yes**</span></span>

  * <span data-ttu-id="709e5-138">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="709e5-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="709e5-139">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="709e5-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="709e5-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="709e5-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="709e5-141">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-141">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="709e5-143">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="709e5-143">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="709e5-144">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="709e5-144">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="709e5-146">确认以下配置：</span><span class="sxs-lookup"><span data-stu-id="709e5-146">Confirm the following configurations:</span></span>

  * <span data-ttu-id="709e5-147">“目标框架”设置为“.NET Core 3.1” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-147">**Target Framework** set to **.NET Core 3.1**.</span></span>
  * <span data-ttu-id="709e5-148">“身份验证”设置为“无身份验证” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-148">**Authentication** set to **No Authentication**.</span></span>
   
  <span data-ttu-id="709e5-149">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="709e5-149">Select **Next**.</span></span>

  ![macOS .NET Core 3.1 选择](start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="709e5-151">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="709e5-151">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="709e5-153">运行应用</span><span class="sxs-lookup"><span data-stu-id="709e5-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="709e5-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="709e5-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="709e5-155">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="709e5-156">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="709e5-157">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="709e5-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="709e5-158">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="709e5-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="709e5-159">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="709e5-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="709e5-160">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="709e5-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="709e5-161">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="709e5-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="709e5-162">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="709e5-164">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="709e5-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="709e5-166">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-166">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="709e5-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="709e5-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="709e5-169">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="709e5-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="709e5-170">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="709e5-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="709e5-171">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="709e5-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="709e5-172">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="709e5-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="709e5-173">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="709e5-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="709e5-174">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="709e5-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="709e5-175">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="709e5-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="709e5-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="709e5-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="709e5-178">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="709e5-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="709e5-179">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="709e5-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="709e5-180">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="709e5-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="709e5-181">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="709e5-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="709e5-182">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="709e5-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="709e5-183">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="709e5-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="709e5-184">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="709e5-185">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-185">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="709e5-187">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="709e5-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="709e5-188">下一页</span><span class="sxs-lookup"><span data-stu-id="709e5-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="709e5-189">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="709e5-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="709e5-190">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="709e5-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="709e5-191">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="709e5-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="709e5-192">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-192">Create a web app.</span></span>
> * <span data-ttu-id="709e5-193">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="709e5-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="709e5-194">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="709e5-194">Work with a database.</span></span>
> * <span data-ttu-id="709e5-195">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="709e5-195">Add search and validation.</span></span>

<span data-ttu-id="709e5-196">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="709e5-197">先决条件</span><span class="sxs-lookup"><span data-stu-id="709e5-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="709e5-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="709e5-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="709e5-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="709e5-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="709e5-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="709e5-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="709e5-201">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="709e5-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="709e5-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="709e5-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="709e5-203">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="709e5-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="709e5-204">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="709e5-206">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="709e5-207">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="709e5-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="709e5-209">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-209">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="709e5-210">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="709e5-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="709e5-211">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="709e5-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="709e5-212">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="709e5-213">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="709e5-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="709e5-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="709e5-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="709e5-215">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="709e5-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="709e5-216">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="709e5-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="709e5-217">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="709e5-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="709e5-218">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="709e5-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="709e5-219">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="709e5-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="709e5-220">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="709e5-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="709e5-221">选择“是”</span><span class="sxs-lookup"><span data-stu-id="709e5-221">Select **Yes**</span></span>

  * <span data-ttu-id="709e5-222">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="709e5-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="709e5-223">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="709e5-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="709e5-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="709e5-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="709e5-225">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="709e5-225">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="709e5-227">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="709e5-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="709e5-228">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="709e5-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="709e5-229">在“配置新的 ASP.NET Core Web API”对话框中，接受默认的 .NET Core 2.2“目标框架”  。</span><span class="sxs-lookup"><span data-stu-id="709e5-229">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 选择](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="709e5-231">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="709e5-231">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="709e5-232">运行应用</span><span class="sxs-lookup"><span data-stu-id="709e5-232">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="709e5-233">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="709e5-233">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="709e5-234">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-234">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="709e5-235">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-235">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="709e5-236">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="709e5-236">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="709e5-237">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="709e5-237">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="709e5-238">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="709e5-238">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="709e5-239">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="709e5-239">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="709e5-240">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="709e5-240">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="709e5-241">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-241">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="709e5-243">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="709e5-243">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="709e5-245">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="709e5-245">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="709e5-246">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="709e5-246">This app doesn't track personal information.</span></span> <span data-ttu-id="709e5-247">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="709e5-247">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="709e5-249">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-249">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="709e5-251">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="709e5-251">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="709e5-252">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="709e5-252">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="709e5-253">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="709e5-253">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="709e5-254">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="709e5-254">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="709e5-255">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="709e5-255">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="709e5-256">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="709e5-256">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="709e5-257">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="709e5-257">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="709e5-258">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="709e5-258">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="709e5-259">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="709e5-259">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="709e5-260">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="709e5-260">This app doesn't track personal information.</span></span> <span data-ttu-id="709e5-261">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="709e5-261">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="709e5-263">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-263">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="709e5-265">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="709e5-265">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="709e5-266">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="709e5-266">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="709e5-267">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="709e5-267">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="709e5-268">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="709e5-268">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="709e5-269">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="709e5-269">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="709e5-270">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="709e5-270">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="709e5-271">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="709e5-271">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="709e5-272">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="709e5-272">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="709e5-273">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="709e5-273">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="709e5-274">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="709e5-274">This app doesn't track personal information.</span></span> <span data-ttu-id="709e5-275">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="709e5-275">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="709e5-277">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="709e5-277">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="709e5-279">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="709e5-279">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="709e5-280">下一页</span><span class="sxs-lookup"><span data-stu-id="709e5-280">Next</span></span>](adding-controller.md)

::: moniker-end
