---
title: ASP.NET Core MVC 入门
author: rick-anderson
description: 了解如何开始使用 ASP.NET Core MVC。
ms.author: riande
ms.date: 11/16/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
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
ms.openlocfilehash: 1c703cdbd168c2e83d09c40f7740689df8938dad
ms.sourcegitcommit: 91e14f1e2a25c98a57c2217fe91b172e0ff2958c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/10/2020
ms.locfileid: "94422767"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="d42ab-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="d42ab-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="d42ab-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="d42ab-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d42ab-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="d42ab-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d42ab-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="d42ab-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="d42ab-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="d42ab-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d42ab-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-108">Create a web app.</span></span>
> * <span data-ttu-id="d42ab-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="d42ab-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d42ab-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="d42ab-110">Work with a database.</span></span>
> * <span data-ttu-id="d42ab-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="d42ab-111">Add search and validation.</span></span>

<span data-ttu-id="d42ab-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d42ab-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="d42ab-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d42ab-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="d42ab-119">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="d42ab-120">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="d42ab-121">在“配置新项目”对话框中，为“项目名称”输入 `MvcMovie`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="d42ab-122">请务必使用此名称（含大写），确保在复制代码时与每个 `namespace` 都相匹配。</span><span class="sxs-lookup"><span data-stu-id="d42ab-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="d42ab-123">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-123">Select **Create**.</span></span>
1. <span data-ttu-id="d42ab-124">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：</span><span class="sxs-lookup"><span data-stu-id="d42ab-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="d42ab-125">下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="d42ab-126">ASP.NET Core Web 应用程序（模型-视图-控制器）。</span><span class="sxs-lookup"><span data-stu-id="d42ab-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="d42ab-127">**创建**</span><span class="sxs-lookup"><span data-stu-id="d42ab-127">**Create**</span></span>

![<span data-ttu-id="d42ab-128">创建新的 ASP.NET Core Web 应用呈现</span><span class="sxs-lookup"><span data-stu-id="d42ab-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/5/mvc.png)

<span data-ttu-id="d42ab-129">有关创建项目的替代方法，请参阅[在 Visual Studio 中创建新项目](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="d42ab-130">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="d42ab-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d42ab-131">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d42ab-132">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="d42ab-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d42ab-134">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="d42ab-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="d42ab-135">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d42ab-136">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d42ab-137">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d42ab-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d42ab-138">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d42ab-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d42ab-139">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="d42ab-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d42ab-140">选择“是”</span><span class="sxs-lookup"><span data-stu-id="d42ab-140">Select **Yes**</span></span>

  * <span data-ttu-id="d42ab-141">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="d42ab-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d42ab-142">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="d42ab-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d42ab-144">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-144">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d42ab-146">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="d42ab-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d42ab-147">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="d42ab-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d42ab-149">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d42ab-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d42ab-150">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d42ab-151">如果看到用于选择“目标框架”的选项，请选择最新的 5.x 版本。</span><span class="sxs-lookup"><span data-stu-id="d42ab-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="d42ab-152">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-152">Select **Next**.</span></span>

* <span data-ttu-id="d42ab-153">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="d42ab-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d42ab-155">运行应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d42ab-157">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d42ab-158">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d42ab-159">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="d42ab-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-160">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d42ab-161">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="d42ab-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d42ab-162">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="d42ab-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d42ab-163">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="d42ab-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d42ab-164">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d42ab-166">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="d42ab-168">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-168">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d42ab-171">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="d42ab-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d42ab-172">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d42ab-173">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-174">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d42ab-175">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="d42ab-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d42ab-176">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="d42ab-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d42ab-177">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="d42ab-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d42ab-180">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d42ab-181">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d42ab-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d42ab-182">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-183">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d42ab-184">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="d42ab-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d42ab-185">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="d42ab-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d42ab-186">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="d42ab-187">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-187">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d42ab-189">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="d42ab-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d42ab-190">下一页</span><span class="sxs-lookup"><span data-stu-id="d42ab-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d42ab-191">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="d42ab-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d42ab-192">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="d42ab-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="d42ab-193">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="d42ab-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d42ab-194">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-194">Create a web app.</span></span>
> * <span data-ttu-id="d42ab-195">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="d42ab-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d42ab-196">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="d42ab-196">Work with a database.</span></span>
> * <span data-ttu-id="d42ab-197">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="d42ab-197">Add search and validation.</span></span>

<span data-ttu-id="d42ab-198">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d42ab-199">先决条件</span><span class="sxs-lookup"><span data-stu-id="d42ab-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="d42ab-203">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d42ab-205">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d42ab-206">选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d42ab-208">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d42ab-209">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="d42ab-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="d42ab-211">选择“Web 应用(模型-视图-控制器)”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="d42ab-212">在下拉框中，选择“.NET Core”和“ASP.NET Core 3.1”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="d42ab-213">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="d42ab-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="d42ab-214">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="d42ab-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d42ab-215">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d42ab-216">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="d42ab-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d42ab-218">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="d42ab-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d42ab-219">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d42ab-220">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d42ab-221">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d42ab-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d42ab-222">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d42ab-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d42ab-223">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="d42ab-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d42ab-224">选择“是”</span><span class="sxs-lookup"><span data-stu-id="d42ab-224">Select **Yes**</span></span>

  * <span data-ttu-id="d42ab-225">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="d42ab-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d42ab-226">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="d42ab-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d42ab-228">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-228">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d42ab-230">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="d42ab-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d42ab-231">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="d42ab-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="d42ab-233">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d42ab-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d42ab-234">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d42ab-235">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="d42ab-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="d42ab-236">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-236">Select **Next**.</span></span>

* <span data-ttu-id="d42ab-237">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="d42ab-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="d42ab-239">运行应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d42ab-241">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d42ab-242">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d42ab-243">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="d42ab-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-244">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d42ab-245">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="d42ab-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d42ab-246">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="d42ab-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d42ab-247">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="d42ab-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d42ab-248">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d42ab-250">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="d42ab-252">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-252">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d42ab-255">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="d42ab-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d42ab-256">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d42ab-257">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-258">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d42ab-259">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="d42ab-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d42ab-260">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="d42ab-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d42ab-261">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="d42ab-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-263">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d42ab-264">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d42ab-265">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d42ab-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d42ab-266">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-267">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d42ab-268">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="d42ab-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d42ab-269">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="d42ab-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d42ab-270">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="d42ab-271">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-271">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d42ab-273">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="d42ab-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d42ab-274">下一页</span><span class="sxs-lookup"><span data-stu-id="d42ab-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="d42ab-275">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="d42ab-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="d42ab-276">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="d42ab-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="d42ab-277">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="d42ab-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="d42ab-278">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-278">Create a web app.</span></span>
> * <span data-ttu-id="d42ab-279">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="d42ab-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="d42ab-280">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="d42ab-280">Work with a database.</span></span>
> * <span data-ttu-id="d42ab-281">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="d42ab-281">Add search and validation.</span></span>

<span data-ttu-id="d42ab-282">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="d42ab-283">先决条件</span><span class="sxs-lookup"><span data-stu-id="d42ab-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-286">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="d42ab-287">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="d42ab-289">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="d42ab-290">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="d42ab-292">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="d42ab-293">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="d42ab-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="d42ab-295">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="d42ab-296">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="d42ab-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="d42ab-297">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="d42ab-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="d42ab-298">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="d42ab-299">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d42ab-301">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="d42ab-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="d42ab-302">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="d42ab-303">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="d42ab-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="d42ab-304">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="d42ab-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="d42ab-305">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="d42ab-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="d42ab-306">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="d42ab-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="d42ab-307">选择“是”</span><span class="sxs-lookup"><span data-stu-id="d42ab-307">Select **Yes**</span></span>

  * <span data-ttu-id="d42ab-308">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="d42ab-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="d42ab-309">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="d42ab-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-310">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="d42ab-311">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-311">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="d42ab-313">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="d42ab-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="d42ab-314">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="d42ab-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="d42ab-315">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="d42ab-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="d42ab-316">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="d42ab-317">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="d42ab-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="d42ab-318">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="d42ab-318">Select **Next**.</span></span>

* <span data-ttu-id="d42ab-319">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="d42ab-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="d42ab-320">运行应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="d42ab-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="d42ab-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="d42ab-322">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="d42ab-323">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="d42ab-324">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="d42ab-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-325">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d42ab-326">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="d42ab-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="d42ab-327">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="d42ab-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d42ab-328">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="d42ab-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="d42ab-329">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="d42ab-331">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="d42ab-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="d42ab-333">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="d42ab-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d42ab-334">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="d42ab-334">This app doesn't track personal information.</span></span> <span data-ttu-id="d42ab-335">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="d42ab-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="d42ab-337">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-337">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="d42ab-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="d42ab-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="d42ab-340">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="d42ab-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="d42ab-341">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="d42ab-342">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-343">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="d42ab-344">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="d42ab-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="d42ab-345">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="d42ab-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="d42ab-346">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="d42ab-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="d42ab-347">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="d42ab-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d42ab-348">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="d42ab-348">This app doesn't track personal information.</span></span> <span data-ttu-id="d42ab-349">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="d42ab-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="d42ab-351">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-351">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="d42ab-353">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="d42ab-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="d42ab-354">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="d42ab-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="d42ab-355">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="d42ab-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="d42ab-356">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="d42ab-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="d42ab-357">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="d42ab-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="d42ab-358">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="d42ab-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="d42ab-359">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="d42ab-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="d42ab-360">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="d42ab-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="d42ab-361">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="d42ab-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="d42ab-362">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="d42ab-362">This app doesn't track personal information.</span></span> <span data-ttu-id="d42ab-363">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="d42ab-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="d42ab-365">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="d42ab-365">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="d42ab-367">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="d42ab-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="d42ab-368">下一页</span><span class="sxs-lookup"><span data-stu-id="d42ab-368">Next</span></span>](adding-controller.md)

::: moniker-end
