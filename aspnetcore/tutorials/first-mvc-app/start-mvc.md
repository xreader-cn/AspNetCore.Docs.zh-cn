---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: 901257efdfbc7b36249233745175f5ed253da2c7
ms.sourcegitcommit: da2fb2d78ce70accdba903ccbfdcfffdd0112123
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/07/2020
ms.locfileid: "75722844"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="2f823-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="2f823-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="2f823-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="2f823-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2f823-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="2f823-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2f823-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="2f823-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="2f823-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="2f823-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2f823-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-108">Create a web app.</span></span>
> * <span data-ttu-id="2f823-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="2f823-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2f823-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="2f823-110">Work with a database.</span></span>
> * <span data-ttu-id="2f823-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="2f823-111">Add search and validation.</span></span>

<span data-ttu-id="2f823-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2f823-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="2f823-113">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2f823-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f823-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2f823-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f823-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2f823-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2f823-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="2f823-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="2f823-117">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2f823-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f823-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2f823-119">在 Visual Studio 中，选择“创建新项目”  。</span><span class="sxs-lookup"><span data-stu-id="2f823-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2f823-120">选择“ASP.NET Core Web 应用程序”，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-120">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2f823-122">将项目命名为“MvcMovie”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2f823-123">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配  。</span><span class="sxs-lookup"><span data-stu-id="2f823-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="2f823-125">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-125">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="2f823-126">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="2f823-126">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="2f823-127">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="2f823-127">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2f823-128">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-128">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2f823-129">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="2f823-129">This is a basic starter project.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2f823-130">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f823-130">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f823-131">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="2f823-131">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2f823-132">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="2f823-132">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2f823-133">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2f823-133">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2f823-134">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2f823-134">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2f823-135">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="2f823-135">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2f823-136">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="2f823-136">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2f823-137">选择“是” </span><span class="sxs-lookup"><span data-stu-id="2f823-137">Select **Yes**</span></span>

  * <span data-ttu-id="2f823-138">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目  。</span><span class="sxs-lookup"><span data-stu-id="2f823-138">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2f823-139">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件  。</span><span class="sxs-lookup"><span data-stu-id="2f823-139">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2f823-140">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2f823-140">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2f823-141">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-141">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2f823-143">选择“.NET Core”  >“应用”  >“Web 应用程序(模型视图控制器)”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="2f823-143">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS“新建项目”对话框](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="2f823-145">在“配置新的 ASP.NET Core Web API”对话框中，将目标框架设置为“.NET Core 3.1”    。</span><span class="sxs-lookup"><span data-stu-id="2f823-145">In the **Configure your new ASP.NET Core Web API** dialog, set the  **Target Framework** of **.NET Core 3.1**.</span></span>

  ![macOS .NET Core 3.1 选择](./start-mvc/_static/new_project_31_vsmac.png)

* <span data-ttu-id="2f823-147">将项目命名为“MvcMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="2f823-147">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="2f823-148">运行应用</span><span class="sxs-lookup"><span data-stu-id="2f823-148">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2f823-149">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f823-149">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f823-150">选择 Ctrl+F5 以在非调试模式下运行应用  。</span><span class="sxs-lookup"><span data-stu-id="2f823-150">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2f823-151">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-151">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2f823-152">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="2f823-152">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2f823-153">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2f823-153">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2f823-154">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="2f823-154">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2f823-155">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="2f823-155">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2f823-156">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="2f823-156">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2f823-157">可以从“调试”  菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-157">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2f823-159">可以通过选择“IIS Express”按钮来调试应用 </span><span class="sxs-lookup"><span data-stu-id="2f823-159">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="2f823-161">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-161">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2f823-163">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f823-163">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f823-164">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2f823-164">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2f823-165">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="2f823-165">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2f823-166">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2f823-166">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2f823-167">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2f823-167">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2f823-168">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f823-168">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2f823-169">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="2f823-169">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2f823-170">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="2f823-170">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2f823-172">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2f823-172">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f823-173">选择“运行” > “开始执行(不调试)”以启动应用   。</span><span class="sxs-lookup"><span data-stu-id="2f823-173">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2f823-174">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号  。</span><span class="sxs-lookup"><span data-stu-id="2f823-174">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2f823-175">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2f823-175">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2f823-176">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2f823-176">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2f823-177">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="2f823-177">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2f823-178">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="2f823-178">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2f823-179">可以从“运行”菜单中以调试或非调试模式启动应用。 </span><span class="sxs-lookup"><span data-stu-id="2f823-179">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="2f823-180">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-180">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2f823-182">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="2f823-182">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2f823-183">下一页</span><span class="sxs-lookup"><span data-stu-id="2f823-183">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="2f823-184">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="2f823-184">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="2f823-185">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="2f823-185">The app manages a database of movie titles.</span></span> <span data-ttu-id="2f823-186">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="2f823-186">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="2f823-187">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-187">Create a web app.</span></span>
> * <span data-ttu-id="2f823-188">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="2f823-188">Add and scaffold a model.</span></span>
> * <span data-ttu-id="2f823-189">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="2f823-189">Work with a database.</span></span>
> * <span data-ttu-id="2f823-190">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="2f823-190">Add search and validation.</span></span>

<span data-ttu-id="2f823-191">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-191">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="2f823-192">先决条件</span><span class="sxs-lookup"><span data-stu-id="2f823-192">Prerequisites</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2f823-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f823-193">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2f823-194">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f823-194">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2f823-195">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2f823-195">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="2f823-196">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="2f823-196">Create a web app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2f823-197">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f823-197">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="2f823-198">在 Visual Studio 中，选择“创建新项目”  。</span><span class="sxs-lookup"><span data-stu-id="2f823-198">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="2f823-199">选择“ASP.NET Core Web 应用程序”，然后选择“下一步”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-199">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="2f823-201">将项目命名为“MvcMovie”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-201">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="2f823-202">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配  。</span><span class="sxs-lookup"><span data-stu-id="2f823-202">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="2f823-204">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-204">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="2f823-205">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="2f823-205">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="2f823-206">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="2f823-206">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="2f823-207">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-207">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="2f823-208">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="2f823-208">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2f823-209">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f823-209">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f823-210">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="2f823-210">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="2f823-211">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="2f823-211">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="2f823-212">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="2f823-212">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="2f823-213">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="2f823-213">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="2f823-214">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="2f823-214">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="2f823-215">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。  是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="2f823-215">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="2f823-216">选择“是” </span><span class="sxs-lookup"><span data-stu-id="2f823-216">Select **Yes**</span></span>

  * <span data-ttu-id="2f823-217">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目  。</span><span class="sxs-lookup"><span data-stu-id="2f823-217">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="2f823-218">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件  。</span><span class="sxs-lookup"><span data-stu-id="2f823-218">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2f823-219">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2f823-219">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="2f823-220">选择“文件”>“新建解决方案”   。</span><span class="sxs-lookup"><span data-stu-id="2f823-220">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="2f823-222">选择“.NET Core”  >“应用”  >“Web 应用程序(模型视图控制器)”  >“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="2f823-222">Select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS“新建项目”对话框](./start-mvc/_static/new_project_mvc_vsmac.png)

* <span data-ttu-id="2f823-224">在“配置新的 ASP.NET Core Web API”对话框中，接受默认的 .NET Core 2.2“目标框架”    。</span><span class="sxs-lookup"><span data-stu-id="2f823-224">In the **Configure your new ASP.NET Core Web API** dialog, accept the default **Target Framework** of **.NET Core 2.2**.</span></span>

  ![macOS .NET Core 2.2 选择](./start-mvc/_static/new_project_22_vsmac.png)

* <span data-ttu-id="2f823-226">将项目命名为“MvcMovie”，然后选择“创建”。  </span><span class="sxs-lookup"><span data-stu-id="2f823-226">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="2f823-227">运行应用</span><span class="sxs-lookup"><span data-stu-id="2f823-227">Run the app</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2f823-228">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2f823-228">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="2f823-229">选择 Ctrl+F5 以在非调试模式下运行应用  。</span><span class="sxs-lookup"><span data-stu-id="2f823-229">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="2f823-230">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="2f823-230">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="2f823-231">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="2f823-231">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2f823-232">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2f823-232">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2f823-233">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="2f823-233">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="2f823-234">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="2f823-234">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2f823-235">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="2f823-235">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="2f823-236">可以从“调试”  菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-236">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="2f823-238">可以通过选择“IIS Express”按钮来调试应用 </span><span class="sxs-lookup"><span data-stu-id="2f823-238">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="2f823-240">选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="2f823-240">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2f823-241">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="2f823-241">This app doesn't track personal information.</span></span> <span data-ttu-id="2f823-242">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="2f823-242">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="2f823-244">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-244">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="2f823-246">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="2f823-246">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="2f823-247">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="2f823-247">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="2f823-248">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="2f823-248">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="2f823-249">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2f823-249">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="2f823-250">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2f823-250">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="2f823-251">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="2f823-251">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="2f823-252">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="2f823-252">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="2f823-253">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="2f823-253">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="2f823-254">选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="2f823-254">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2f823-255">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="2f823-255">This app doesn't track personal information.</span></span> <span data-ttu-id="2f823-256">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="2f823-256">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="2f823-258">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-258">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="2f823-260">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="2f823-260">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="2f823-261">选择“运行” > “开始执行(不调试)”以启动应用   。</span><span class="sxs-lookup"><span data-stu-id="2f823-261">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="2f823-262">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号  。</span><span class="sxs-lookup"><span data-stu-id="2f823-262">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="2f823-263">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="2f823-263">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="2f823-264">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="2f823-264">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="2f823-265">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="2f823-265">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="2f823-266">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="2f823-266">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="2f823-267">可以从“运行”菜单中以调试或非调试模式启动应用。 </span><span class="sxs-lookup"><span data-stu-id="2f823-267">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="2f823-268">选择“接受”以同意跟踪  。</span><span class="sxs-lookup"><span data-stu-id="2f823-268">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="2f823-269">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="2f823-269">This app doesn't track personal information.</span></span> <span data-ttu-id="2f823-270">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="2f823-270">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="2f823-272">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="2f823-272">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="2f823-274">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="2f823-274">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="2f823-275">下一页</span><span class="sxs-lookup"><span data-stu-id="2f823-275">Next</span></span>](adding-controller.md)

::: moniker-end
