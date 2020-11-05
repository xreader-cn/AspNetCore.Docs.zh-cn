---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 10/16/2019
no-loc:
- ':::no-loc(appsettings.json):::'
- ':::no-loc(ASP.NET Core Identity):::'
- ':::no-loc(cookie):::'
- ':::no-loc(Cookie):::'
- ':::no-loc(Blazor):::'
- ':::no-loc(Blazor Server):::'
- ':::no-loc(Blazor WebAssembly):::'
- ':::no-loc(Identity):::'
- ":::no-loc(Let's Encrypt):::"
- ':::no-loc(Razor):::'
- ':::no-loc(SignalR):::'
uid: tutorials/first-mvc-app/start-mvc
ms.openlocfilehash: cf17aaf8eff342c378536d4f635e09b936459bee
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052898"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="94d5c-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="94d5c-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="94d5c-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="94d5c-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="94d5c-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="94d5c-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="94d5c-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="94d5c-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="94d5c-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="94d5c-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94d5c-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-108">Create a web app.</span></span>
> * <span data-ttu-id="94d5c-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="94d5c-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="94d5c-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="94d5c-110">Work with a database.</span></span>
> * <span data-ttu-id="94d5c-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="94d5c-111">Add search and validation.</span></span>

<span data-ttu-id="94d5c-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="94d5c-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="94d5c-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94d5c-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d5c-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94d5c-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d5c-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94d5c-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d5c-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="94d5c-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="94d5c-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94d5c-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d5c-118">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94d5c-119">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-119">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="94d5c-120">选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-120">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="94d5c-122">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-122">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="94d5c-123">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="94d5c-123">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="94d5c-125">选择“Web 应用(模型-视图-控制器)”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-125">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="94d5c-126">在下拉框中，选择“.NET Core”和“ASP.NET Core 3.1”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-126">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1** , then select **Create**.</span></span>

![<span data-ttu-id="94d5c-127">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="94d5c-127">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="94d5c-128">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="94d5c-128">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="94d5c-129">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-129">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="94d5c-130">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="94d5c-130">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94d5c-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d5c-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94d5c-132">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="94d5c-132">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="94d5c-133">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="94d5c-133">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="94d5c-134">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="94d5c-134">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="94d5c-135">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="94d5c-135">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="94d5c-136">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="94d5c-136">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="94d5c-137">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="94d5c-137">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="94d5c-138">选择“是”</span><span class="sxs-lookup"><span data-stu-id="94d5c-138">Select **Yes**</span></span>

  * <span data-ttu-id="94d5c-139">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="94d5c-139">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="94d5c-140">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="94d5c-140">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94d5c-141">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d5c-141">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94d5c-142">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-142">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="94d5c-144">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="94d5c-144">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="94d5c-145">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="94d5c-145">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="94d5c-147">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="94d5c-147">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="94d5c-148">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-148">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="94d5c-149">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="94d5c-149">If presented an option to select a **Target Framework** , select the latest 3.x version.</span></span>

  <span data-ttu-id="94d5c-150">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-150">Select **Next**.</span></span>

* <span data-ttu-id="94d5c-151">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="94d5c-151">Name the project **MvcMovie** , and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="94d5c-153">运行应用</span><span class="sxs-lookup"><span data-stu-id="94d5c-153">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94d5c-154">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d5c-154">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94d5c-155">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-155">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="94d5c-156">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-156">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="94d5c-157">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="94d5c-157">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94d5c-158">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="94d5c-158">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="94d5c-159">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="94d5c-159">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="94d5c-160">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="94d5c-160">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="94d5c-161">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="94d5c-161">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="94d5c-162">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-162">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="94d5c-164">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="94d5c-164">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="94d5c-166">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-166">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94d5c-168">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d5c-168">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94d5c-169">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="94d5c-169">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="94d5c-170">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="94d5c-170">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="94d5c-171">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="94d5c-171">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="94d5c-172">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="94d5c-172">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="94d5c-173">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="94d5c-173">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="94d5c-174">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="94d5c-174">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="94d5c-175">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="94d5c-175">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94d5c-177">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d5c-177">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94d5c-178">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-178">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="94d5c-179">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="94d5c-179">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="94d5c-180">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="94d5c-180">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94d5c-181">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="94d5c-181">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="94d5c-182">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="94d5c-182">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="94d5c-183">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="94d5c-183">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="94d5c-184">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-184">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="94d5c-185">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-185">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="94d5c-187">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="94d5c-187">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="94d5c-188">下一页</span><span class="sxs-lookup"><span data-stu-id="94d5c-188">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="94d5c-189">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="94d5c-189">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="94d5c-190">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="94d5c-190">The app manages a database of movie titles.</span></span> <span data-ttu-id="94d5c-191">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="94d5c-191">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="94d5c-192">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-192">Create a web app.</span></span>
> * <span data-ttu-id="94d5c-193">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="94d5c-193">Add and scaffold a model.</span></span>
> * <span data-ttu-id="94d5c-194">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="94d5c-194">Work with a database.</span></span>
> * <span data-ttu-id="94d5c-195">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="94d5c-195">Add search and validation.</span></span>

<span data-ttu-id="94d5c-196">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-196">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="94d5c-197">先决条件</span><span class="sxs-lookup"><span data-stu-id="94d5c-197">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94d5c-198">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d5c-198">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="94d5c-199">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d5c-199">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94d5c-200">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d5c-200">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="94d5c-201">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="94d5c-201">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94d5c-202">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d5c-202">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="94d5c-203">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-203">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="94d5c-204">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-204">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="94d5c-206">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-206">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="94d5c-207">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="94d5c-207">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="94d5c-209">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-209">Select **Web Application(Model-View-Controller)** , and then select **Create**.</span></span>

![<span data-ttu-id="94d5c-210">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="94d5c-210">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="94d5c-211">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="94d5c-211">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="94d5c-212">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-212">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="94d5c-213">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-213">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94d5c-214">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d5c-214">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94d5c-215">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="94d5c-215">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="94d5c-216">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="94d5c-216">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="94d5c-217">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="94d5c-217">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="94d5c-218">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="94d5c-218">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="94d5c-219">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="94d5c-219">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="94d5c-220">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="94d5c-220">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="94d5c-221">选择“是”</span><span class="sxs-lookup"><span data-stu-id="94d5c-221">Select **Yes**</span></span>

  * <span data-ttu-id="94d5c-222">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="94d5c-222">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="94d5c-223">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="94d5c-223">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94d5c-224">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d5c-224">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="94d5c-225">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-225">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="94d5c-227">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="94d5c-227">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="94d5c-228">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="94d5c-228">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="94d5c-229">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="94d5c-229">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="94d5c-230">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-230">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="94d5c-231">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="94d5c-231">If presented an option to select a **Target Framework** , select the latest 2.x version.</span></span>

  <span data-ttu-id="94d5c-232">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="94d5c-232">Select **Next**.</span></span>

* <span data-ttu-id="94d5c-233">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="94d5c-233">Name the project **MvcMovie** , and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="94d5c-234">运行应用</span><span class="sxs-lookup"><span data-stu-id="94d5c-234">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94d5c-235">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94d5c-235">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="94d5c-236">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-236">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="94d5c-237">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-237">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="94d5c-238">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="94d5c-238">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94d5c-239">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="94d5c-239">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="94d5c-240">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="94d5c-240">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="94d5c-241">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="94d5c-241">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="94d5c-242">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="94d5c-242">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="94d5c-243">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-243">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="94d5c-245">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="94d5c-245">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="94d5c-247">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="94d5c-247">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="94d5c-248">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="94d5c-248">This app doesn't track personal information.</span></span> <span data-ttu-id="94d5c-249">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="94d5c-249">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="94d5c-251">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-251">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="94d5c-253">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94d5c-253">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="94d5c-254">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="94d5c-254">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="94d5c-255">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="94d5c-255">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="94d5c-256">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="94d5c-256">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="94d5c-257">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="94d5c-257">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="94d5c-258">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="94d5c-258">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="94d5c-259">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="94d5c-259">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="94d5c-260">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="94d5c-260">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="94d5c-261">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="94d5c-261">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="94d5c-262">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="94d5c-262">This app doesn't track personal information.</span></span> <span data-ttu-id="94d5c-263">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="94d5c-263">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="94d5c-265">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-265">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94d5c-267">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94d5c-267">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="94d5c-268">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="94d5c-268">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="94d5c-269">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="94d5c-269">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="94d5c-270">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="94d5c-270">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="94d5c-271">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="94d5c-271">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="94d5c-272">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="94d5c-272">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="94d5c-273">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="94d5c-273">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="94d5c-274">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="94d5c-274">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="94d5c-275">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="94d5c-275">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="94d5c-276">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="94d5c-276">This app doesn't track personal information.</span></span> <span data-ttu-id="94d5c-277">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="94d5c-277">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="94d5c-279">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="94d5c-279">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="94d5c-281">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="94d5c-281">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="94d5c-282">下一页</span><span class="sxs-lookup"><span data-stu-id="94d5c-282">Next</span></span>](adding-controller.md)

::: moniker-end
