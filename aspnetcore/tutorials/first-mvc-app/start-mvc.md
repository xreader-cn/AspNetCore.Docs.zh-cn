---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 0c8c59a5c59c8a70985dc8463c80f9569a00621f
ms.sourcegitcommit: 6628cd23793b66e4ce88788db641a5bbf470c3c1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73761235"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="7be89-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="7be89-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="7be89-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="7be89-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="7be89-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="7be89-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="7be89-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="7be89-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="7be89-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="7be89-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7be89-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-108">Create a web app.</span></span>
> * <span data-ttu-id="7be89-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="7be89-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="7be89-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="7be89-110">Work with a database.</span></span>
> * <span data-ttu-id="7be89-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="7be89-111">Add search and validation.</span></span>

<span data-ttu-id="7be89-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="7be89-113">系统必备</span><span class="sxs-lookup"><span data-stu-id="7be89-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7be89-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7be89-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7be89-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7be89-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7be89-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7be89-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="7be89-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="7be89-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7be89-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7be89-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7be89-119">在 Visual Studio 中，选择“创建新项目”  。</span><span class="sxs-lookup"><span data-stu-id="7be89-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="7be89-120">选择“ASP.NET Core Web 应用程序”，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="7be89-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="7be89-122">将项目命名为“MvcMovie”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="7be89-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="7be89-123">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配  。</span><span class="sxs-lookup"><span data-stu-id="7be89-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="7be89-125">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="7be89-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="7be89-126">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="7be89-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="7be89-127">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="7be89-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="7be89-128">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="7be89-129">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="7be89-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7be89-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7be89-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7be89-131">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="7be89-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="7be89-132">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="7be89-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="7be89-133">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="7be89-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7be89-134">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7be89-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7be89-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7be89-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="7be89-136">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="7be89-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="7be89-137">选择“是” </span><span class="sxs-lookup"><span data-stu-id="7be89-137">Select **Yes**</span></span>

  * <span data-ttu-id="7be89-138">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目  。</span><span class="sxs-lookup"><span data-stu-id="7be89-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="7be89-139">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件  。</span><span class="sxs-lookup"><span data-stu-id="7be89-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7be89-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7be89-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7be89-141">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="7be89-141">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="7be89-143">选择“.NET Core”>“应用”>“Web 应用程序(模型-视图-控制器)”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="7be89-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS“新建项目”对话框](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="7be89-145">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架设置为“.NET Core 3.0”    。</span><span class="sxs-lookup"><span data-stu-id="7be89-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.0**.</span></span>

<!-- 
  ![macOS .NET Core 2.2 selection](./start-mvc/_static/new_project_22_vsmac.png)
-->

* <span data-ttu-id="7be89-146">将项目命名为“MvcMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="7be89-146">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="7be89-147">运行应用</span><span class="sxs-lookup"><span data-stu-id="7be89-147">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7be89-148">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7be89-148">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7be89-149">选择 Ctrl+F5 以在非调试模式下运行应用  。</span><span class="sxs-lookup"><span data-stu-id="7be89-149">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="7be89-150">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-150">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="7be89-151">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="7be89-151">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="7be89-152">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7be89-152">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="7be89-153">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="7be89-153">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="7be89-154">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="7be89-154">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="7be89-155">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="7be89-155">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="7be89-156">可以从“调试”  菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-156">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="7be89-158">可以通过选择“IIS Express”按钮来调试应用 </span><span class="sxs-lookup"><span data-stu-id="7be89-158">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="7be89-160">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-160">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7be89-162">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7be89-162">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7be89-163">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7be89-163">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="7be89-164">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="7be89-164">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="7be89-165">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="7be89-165">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="7be89-166">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7be89-166">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="7be89-167">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="7be89-167">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="7be89-168">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="7be89-168">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="7be89-169">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="7be89-169">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7be89-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7be89-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7be89-172">选择“运行” > “开始执行(不调试)”以启动应用   。</span><span class="sxs-lookup"><span data-stu-id="7be89-172">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="7be89-173">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号  。</span><span class="sxs-lookup"><span data-stu-id="7be89-173">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="7be89-174">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="7be89-174">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="7be89-175">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7be89-175">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="7be89-176">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="7be89-176">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="7be89-177">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="7be89-177">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="7be89-178">可以从“运行”菜单中以调试或非调试模式启动应用。 </span><span class="sxs-lookup"><span data-stu-id="7be89-178">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="7be89-179">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-179">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="7be89-181">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="7be89-181">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7be89-182">下一页</span><span class="sxs-lookup"><span data-stu-id="7be89-182">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="7be89-183">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="7be89-183">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="7be89-184">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="7be89-184">The app manages a database of movie titles.</span></span> <span data-ttu-id="7be89-185">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="7be89-185">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="7be89-186">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-186">Create a web app.</span></span>
> * <span data-ttu-id="7be89-187">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="7be89-187">Add and scaffold a model.</span></span>
> * <span data-ttu-id="7be89-188">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="7be89-188">Work with a database.</span></span>
> * <span data-ttu-id="7be89-189">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="7be89-189">Add search and validation.</span></span>

<span data-ttu-id="7be89-190">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-190">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="7be89-191">系统必备</span><span class="sxs-lookup"><span data-stu-id="7be89-191">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7be89-192">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7be89-192">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7be89-193">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7be89-193">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7be89-194">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7be89-194">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="7be89-195">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="7be89-195">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7be89-196">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7be89-196">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="7be89-197">在 Visual Studio 中，选择“创建新项目”  。</span><span class="sxs-lookup"><span data-stu-id="7be89-197">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="7be89-198">选择“ASP.NET Core Web 应用程序”，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="7be89-198">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="7be89-200">将项目命名为“MvcMovie”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="7be89-200">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="7be89-201">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配  。</span><span class="sxs-lookup"><span data-stu-id="7be89-201">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="7be89-203">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="7be89-203">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="7be89-204">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="7be89-204">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="7be89-205">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="7be89-205">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="7be89-206">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-206">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="7be89-207">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="7be89-207">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7be89-208">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7be89-208">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7be89-209">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="7be89-209">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="7be89-210">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="7be89-210">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="7be89-211">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="7be89-211">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="7be89-212">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="7be89-212">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="7be89-213">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="7be89-213">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="7be89-214">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="7be89-214">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="7be89-215">选择“是” </span><span class="sxs-lookup"><span data-stu-id="7be89-215">Select **Yes**</span></span>

  * <span data-ttu-id="7be89-216">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目  。</span><span class="sxs-lookup"><span data-stu-id="7be89-216">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="7be89-217">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件  。</span><span class="sxs-lookup"><span data-stu-id="7be89-217">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7be89-218">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7be89-218">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="7be89-219">选择“文件”>“新建解决方案”  >  。</span><span class="sxs-lookup"><span data-stu-id="7be89-219">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="7be89-221">选择“.NET Core”>“应用”>“Web 应用程序(模型-视图-控制器)”>“下一步”     。</span><span class="sxs-lookup"><span data-stu-id="7be89-221">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS“新建项目”对话框](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="7be89-223">在“配置新的 ASP.NET Core Web API”对话框中，接受默认的 .NET Core 2.2“目标框架”    。</span><span class="sxs-lookup"><span data-stu-id="7be89-223">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 选择](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="7be89-225">将项目命名为“MvcMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="7be89-225">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="7be89-226">运行应用</span><span class="sxs-lookup"><span data-stu-id="7be89-226">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="7be89-227">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7be89-227">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="7be89-228">选择 Ctrl+F5 以在非调试模式下运行应用  。</span><span class="sxs-lookup"><span data-stu-id="7be89-228">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="7be89-229">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="7be89-229">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="7be89-230">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="7be89-230">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="7be89-231">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7be89-231">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="7be89-232">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="7be89-232">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="7be89-233">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="7be89-233">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="7be89-234">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="7be89-234">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="7be89-235">可以从“调试”  菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-235">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="7be89-237">可以通过选择“IIS Express”按钮来调试应用 </span><span class="sxs-lookup"><span data-stu-id="7be89-237">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="7be89-239">选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="7be89-239">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="7be89-240">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="7be89-240">This app doesn't track personal information.</span></span> <span data-ttu-id="7be89-241">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="7be89-241">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="7be89-243">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-243">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="7be89-245">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7be89-245">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="7be89-246">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7be89-246">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="7be89-247">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="7be89-247">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="7be89-248">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="7be89-248">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="7be89-249">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7be89-249">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="7be89-250">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="7be89-250">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="7be89-251">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="7be89-251">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="7be89-252">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="7be89-252">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="7be89-253">选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="7be89-253">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="7be89-254">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="7be89-254">This app doesn't track personal information.</span></span> <span data-ttu-id="7be89-255">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="7be89-255">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="7be89-257">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-257">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="7be89-259">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="7be89-259">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="7be89-260">选择“运行” > “开始执行(不调试)”以启动应用   。</span><span class="sxs-lookup"><span data-stu-id="7be89-260">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="7be89-261">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号  。</span><span class="sxs-lookup"><span data-stu-id="7be89-261">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="7be89-262">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="7be89-262">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="7be89-263">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="7be89-263">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="7be89-264">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="7be89-264">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="7be89-265">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="7be89-265">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="7be89-266">可以从“运行”菜单中以调试或非调试模式启动应用。 </span><span class="sxs-lookup"><span data-stu-id="7be89-266">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="7be89-267">选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="7be89-267">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="7be89-268">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="7be89-268">This app doesn't track personal information.</span></span> <span data-ttu-id="7be89-269">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="7be89-269">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="7be89-271">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="7be89-271">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="7be89-273">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="7be89-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="7be89-274">下一页</span><span class="sxs-lookup"><span data-stu-id="7be89-274">Next</span></span>](adding-controller.md)

::: moniker-end
