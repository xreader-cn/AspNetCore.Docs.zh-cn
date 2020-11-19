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
ms.openlocfilehash: c96e7107c85bf36f55f6571c71c20d09bc94ddb3
ms.sourcegitcommit: fb208f907249cc7aab029afff941a0266c187050
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/17/2020
ms.locfileid: "94688467"
---
# <a name="get-started-with-aspnet-core-mvc"></a><span data-ttu-id="74082-103">ASP.NET Core MVC 入门</span><span class="sxs-lookup"><span data-stu-id="74082-103">Get started with ASP.NET Core MVC</span></span>

<span data-ttu-id="74082-104">作者：[Rick Anderson](https://twitter.com/RickAndMSFT)</span><span class="sxs-lookup"><span data-stu-id="74082-104">By [Rick Anderson](https://twitter.com/RickAndMSFT)</span></span>

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="74082-105">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="74082-105">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="74082-106">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="74082-106">The app manages a database of movie titles.</span></span> <span data-ttu-id="74082-107">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="74082-107">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="74082-108">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="74082-108">Create a web app.</span></span>
> * <span data-ttu-id="74082-109">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="74082-109">Add and scaffold a model.</span></span>
> * <span data-ttu-id="74082-110">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="74082-110">Work with a database.</span></span>
> * <span data-ttu-id="74082-111">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="74082-111">Add search and validation.</span></span>

<span data-ttu-id="74082-112">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="74082-112">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="74082-113">先决条件</span><span class="sxs-lookup"><span data-stu-id="74082-113">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-114">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-5.0.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-115">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-115">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-116">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-116">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-5.0.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="74082-117">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="74082-117">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-118">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-118">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="74082-119">启动 Visual Studio 并选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="74082-119">Start Visual Studio and select **Create a new project**.</span></span>
1. <span data-ttu-id="74082-120">在“新建项目”对话框中，选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="74082-120">In the **Create a new project** dialog, select **ASP.NET Core Web Application** > **Next**.</span></span>
1. <span data-ttu-id="74082-121">在“配置新项目”对话框中，为“项目名称”输入 `MvcMovie`。</span><span class="sxs-lookup"><span data-stu-id="74082-121">In the **Configure your new project** dialog, enter `MvcMovie` for **Project name**.</span></span> <span data-ttu-id="74082-122">请务必使用此名称（含大写），确保在复制代码时与每个 `namespace` 都相匹配。</span><span class="sxs-lookup"><span data-stu-id="74082-122">It's important to use this exact name including capitalization, so each `namespace` matches when code is copied.</span></span>
1. <span data-ttu-id="74082-123">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="74082-123">Select **Create**.</span></span>
1. <span data-ttu-id="74082-124">在“创建新的 ASP.NET Core Web 应用程序”对话框中，选择：</span><span class="sxs-lookup"><span data-stu-id="74082-124">In the **Create a new ASP.NET Core web application** dialog, select:</span></span>
    1. <span data-ttu-id="74082-125">下拉列表中的“.NET Core”和“ASP.NET Core 5.0”。</span><span class="sxs-lookup"><span data-stu-id="74082-125">**.NET Core** and **ASP.NET Core 5.0** in the dropdowns.</span></span>
    1. <span data-ttu-id="74082-126">ASP.NET Core Web 应用程序（模型-视图-控制器）。</span><span class="sxs-lookup"><span data-stu-id="74082-126">**ASP.NET Core Web App (Model-View-Controller)**.</span></span>
    1. <span data-ttu-id="74082-127">**创建**</span><span class="sxs-lookup"><span data-stu-id="74082-127">**Create**</span></span>

![<span data-ttu-id="74082-128">创建新的 ASP.NET Core Web 应用呈现</span><span class="sxs-lookup"><span data-stu-id="74082-128">Create a new ASP.NET Core web application</span></span> ](start-mvc/_static/mvcVS19v16.9.png)

<span data-ttu-id="74082-129">有关创建项目的替代方法，请参阅[在 Visual Studio 中创建新项目](/visualstudio/ide/create-new-project)。</span><span class="sxs-lookup"><span data-stu-id="74082-129">For alternative approaches to create the project, see [Create a new project in Visual Studio](/visualstudio/ide/create-new-project).</span></span>

<span data-ttu-id="74082-130">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="74082-130">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="74082-131">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="74082-131">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="74082-132">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="74082-132">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="74082-134">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="74082-134">The tutorial assumes familiarity with VS Code.</span></span> <span data-ttu-id="74082-135">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="74082-135">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="74082-136">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="74082-136">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="74082-137">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="74082-137">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="74082-138">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="74082-138">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="74082-139">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="74082-139">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="74082-140">选择“是”</span><span class="sxs-lookup"><span data-stu-id="74082-140">Select **Yes**</span></span>

  * <span data-ttu-id="74082-141">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="74082-141">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="74082-142">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="74082-142">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-143">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-143">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="74082-144">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="74082-144">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="74082-146">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="74082-146">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="74082-147">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="74082-147">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="74082-149">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="74082-149">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="74082-150">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="74082-150">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="74082-151">如果看到用于选择“目标框架”的选项，请选择最新的 5.x 版本。</span><span class="sxs-lookup"><span data-stu-id="74082-151">If presented an option to select a **Target Framework**, select the latest 5.x version.</span></span>

  <span data-ttu-id="74082-152">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="74082-152">Select **Next**.</span></span>

* <span data-ttu-id="74082-153">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="74082-153">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="74082-155">运行应用</span><span class="sxs-lookup"><span data-stu-id="74082-155">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-156">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-156">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="74082-157">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="74082-157">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="74082-158">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="74082-158">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="74082-159">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="74082-159">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="74082-160">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-160">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="74082-161">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="74082-161">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="74082-162">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="74082-162">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="74082-163">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="74082-163">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="74082-164">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="74082-164">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="74082-166">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="74082-166">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="74082-168">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="74082-168">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home50-vs.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-170">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-170">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="74082-171">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="74082-171">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="74082-172">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="74082-172">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="74082-173">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="74082-173">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="74082-174">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-174">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="74082-175">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="74082-175">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="74082-176">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="74082-176">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="74082-177">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="74082-177">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home50-port5001.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-179">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-179">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="74082-180">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="74082-180">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="74082-181">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="74082-181">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="74082-182">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="74082-182">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="74082-183">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-183">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="74082-184">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="74082-184">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="74082-185">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="74082-185">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="74082-186">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="74082-186">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="74082-187">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="74082-187">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="74082-189">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="74082-189">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="74082-190">下一页</span><span class="sxs-lookup"><span data-stu-id="74082-190">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range=">= aspnetcore-3.0 < aspnetcore-5.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="74082-191">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="74082-191">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="74082-192">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="74082-192">The app manages a database of movie titles.</span></span> <span data-ttu-id="74082-193">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="74082-193">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="74082-194">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="74082-194">Create a web app.</span></span>
> * <span data-ttu-id="74082-195">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="74082-195">Add and scaffold a model.</span></span>
> * <span data-ttu-id="74082-196">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="74082-196">Work with a database.</span></span>
> * <span data-ttu-id="74082-197">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="74082-197">Add search and validation.</span></span>

<span data-ttu-id="74082-198">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="74082-198">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="74082-199">先决条件</span><span class="sxs-lookup"><span data-stu-id="74082-199">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-200">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-200">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-202">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-202">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

---

## <a name="create-a-web-app"></a><span data-ttu-id="74082-203">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="74082-203">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-204">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-204">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="74082-205">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="74082-205">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="74082-206">选择“ASP.NET Core Web 应用程序”>“下一步”。</span><span class="sxs-lookup"><span data-stu-id="74082-206">Select **ASP.NET Core Web Application** > **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="74082-208">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="74082-208">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="74082-209">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="74082-209">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)

* <span data-ttu-id="74082-211">选择“Web 应用(模型-视图-控制器)”。</span><span class="sxs-lookup"><span data-stu-id="74082-211">Select **Web Application(Model-View-Controller)**.</span></span> <span data-ttu-id="74082-212">在下拉框中，选择“.NET Core”和“ASP.NET Core 3.1”，然后选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="74082-212">From the dropdown boxes, select **.NET Core** and **ASP.NET Core 3.1**, then select **Create**.</span></span>

![<span data-ttu-id="74082-213">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="74082-213">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project30.png)

<span data-ttu-id="74082-214">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="74082-214">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="74082-215">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="74082-215">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="74082-216">这是一个基本的入门项目。</span><span class="sxs-lookup"><span data-stu-id="74082-216">This is a basic starter project.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-217">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-217">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="74082-218">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="74082-218">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="74082-219">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="74082-219">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="74082-220">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="74082-220">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="74082-221">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="74082-221">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="74082-222">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="74082-222">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="74082-223">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="74082-223">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="74082-224">选择“是”</span><span class="sxs-lookup"><span data-stu-id="74082-224">Select **Yes**</span></span>

  * <span data-ttu-id="74082-225">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="74082-225">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="74082-226">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="74082-226">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-227">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-227">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="74082-228">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="74082-228">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="74082-230">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="74082-230">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="74082-231">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="74082-231">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

  ![macOS Web 应用模板选择](start-mvc/_static/web_app_template_vsmac.png)

* <span data-ttu-id="74082-233">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="74082-233">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="74082-234">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="74082-234">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="74082-235">如果看到用于选择“目标框架”的选项，请选择最新的 3.x 版本。</span><span class="sxs-lookup"><span data-stu-id="74082-235">If presented an option to select a **Target Framework**, select the latest 3.x version.</span></span>

  <span data-ttu-id="74082-236">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="74082-236">Select **Next**.</span></span>

* <span data-ttu-id="74082-237">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="74082-237">Name the project **MvcMovie**, and then select **Create**.</span></span>

  ![macOS 命名项目](start-mvc/_static/MvcMovie.png)

---

### <a name="run-the-app"></a><span data-ttu-id="74082-239">运行应用</span><span class="sxs-lookup"><span data-stu-id="74082-239">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-240">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-240">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="74082-241">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="74082-241">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="74082-242">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="74082-242">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="74082-243">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="74082-243">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="74082-244">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-244">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="74082-245">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="74082-245">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="74082-246">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="74082-246">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="74082-247">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="74082-247">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="74082-248">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="74082-248">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="74082-250">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="74082-250">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

  <span data-ttu-id="74082-252">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="74082-252">The following image shows the app:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-254">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-254">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="74082-255">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="74082-255">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="74082-256">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="74082-256">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="74082-257">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="74082-257">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="74082-258">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-258">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="74082-259">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="74082-259">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="74082-260">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="74082-260">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="74082-261">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="74082-261">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-263">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-263">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="74082-264">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="74082-264">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="74082-265">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="74082-265">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="74082-266">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="74082-266">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="74082-267">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-267">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="74082-268">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="74082-268">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="74082-269">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="74082-269">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="74082-270">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="74082-270">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

  <span data-ttu-id="74082-271">下图显示该应用：</span><span class="sxs-lookup"><span data-stu-id="74082-271">The following image shows the app:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="74082-273">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="74082-273">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="74082-274">下一页</span><span class="sxs-lookup"><span data-stu-id="74082-274">Next</span></span>](adding-controller.md)

::: moniker-end

::: moniker range="< aspnetcore-3.0"

[!INCLUDE [consider RP](~/includes/razor.md)]

<span data-ttu-id="74082-275">本教程介绍构建 ASP.NET Core MVC Web 应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="74082-275">This tutorial teaches the basics of building an ASP.NET Core MVC web app.</span></span>

<span data-ttu-id="74082-276">该应用管理电影标题的数据库。</span><span class="sxs-lookup"><span data-stu-id="74082-276">The app manages a database of movie titles.</span></span> <span data-ttu-id="74082-277">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="74082-277">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="74082-278">创建 Web 应用。</span><span class="sxs-lookup"><span data-stu-id="74082-278">Create a web app.</span></span>
> * <span data-ttu-id="74082-279">添加和构架模型。</span><span class="sxs-lookup"><span data-stu-id="74082-279">Add and scaffold a model.</span></span>
> * <span data-ttu-id="74082-280">使用数据库。</span><span class="sxs-lookup"><span data-stu-id="74082-280">Work with a database.</span></span>
> * <span data-ttu-id="74082-281">添加搜索和验证。</span><span class="sxs-lookup"><span data-stu-id="74082-281">Add search and validation.</span></span>

<span data-ttu-id="74082-282">在结束时，你会获得可以管理和显示电影数据的应用。</span><span class="sxs-lookup"><span data-stu-id="74082-282">At the end, you have an app that can manage and display movie data.</span></span>

[!INCLUDE[](~/includes/mvc-intro/download.md)]

## <a name="prerequisites"></a><span data-ttu-id="74082-283">先决条件</span><span class="sxs-lookup"><span data-stu-id="74082-283">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-284">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-284">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs2019-2.2.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-285">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-285">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-2.2.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-286">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-286">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-2.2.md)]

---
## <a name="create-a-web-app"></a><span data-ttu-id="74082-287">创建 Web 应用</span><span class="sxs-lookup"><span data-stu-id="74082-287">Create a web app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-288">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-288">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="74082-289">在 Visual Studio 中，选择“创建新项目”。</span><span class="sxs-lookup"><span data-stu-id="74082-289">From the Visual Studio select **Create a new project**.</span></span>

* <span data-ttu-id="74082-290">选择“ASP.NET Core Web 应用程序”，然后选择“下一步” 。</span><span class="sxs-lookup"><span data-stu-id="74082-290">Select **ASP.NET Core Web Application** and then select **Next**.</span></span>

![新建 ASP.NET Core Web 应用程序](start-mvc/_static/np_2.1.png)

* <span data-ttu-id="74082-292">将项目命名为“MvcMovie”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="74082-292">Name the project **MvcMovie** and select **Create**.</span></span> <span data-ttu-id="74082-293">将项目命名为“MvcMovie”非常重要，这样在复制代码时，命名空间才会匹配。</span><span class="sxs-lookup"><span data-stu-id="74082-293">It's important to name the project **MvcMovie** so when you copy code, the namespace will match.</span></span>

  ![新建 ASP.NET Core Web 应用程序](start-mvc/_static/config.png)


* <span data-ttu-id="74082-295">选择“Web 应用程序(模型-视图-控制器)”，然后选择“创建” 。</span><span class="sxs-lookup"><span data-stu-id="74082-295">Select **Web Application(Model-View-Controller)**, and then select **Create**.</span></span>

![<span data-ttu-id="74082-296">“新建项目”对话框，左窗格中的“.NET Core”，ASP.NET Core Web</span><span class="sxs-lookup"><span data-stu-id="74082-296">New project dialog, .NET Core in left pane, ASP.NET Core web</span></span> ](start-mvc/_static/new_project22-21.png)

<span data-ttu-id="74082-297">Visual Studio 为刚刚创建的 MVC 项目使用默认模板。</span><span class="sxs-lookup"><span data-stu-id="74082-297">Visual Studio used the default template for the MVC project you just created.</span></span> <span data-ttu-id="74082-298">输入项目名称并选择几个选项后，就拥有了一个可正常运行的应用。</span><span class="sxs-lookup"><span data-stu-id="74082-298">You have a working app right now by entering a project name and selecting a few options.</span></span> <span data-ttu-id="74082-299">这是一个基本的初学者项目，适合入门使用。</span><span class="sxs-lookup"><span data-stu-id="74082-299">This is a basic starter project, and it's a good place to start.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-300">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-300">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="74082-301">本教程假定用户熟悉 VS Code。</span><span class="sxs-lookup"><span data-stu-id="74082-301">The tutorial assumes familarity with VS Code.</span></span> <span data-ttu-id="74082-302">有关详细信息，请参阅 [VS Code 入门](https://code.visualstudio.com/docs) 和 [Visual Studio Code 帮助](#visual-studio-code-help)。</span><span class="sxs-lookup"><span data-stu-id="74082-302">See [Getting started with VS Code](https://code.visualstudio.com/docs) and [Visual Studio Code help](#visual-studio-code-help) for more information.</span></span>

* <span data-ttu-id="74082-303">打开[集成终端](https://code.visualstudio.com/docs/editor/integrated-terminal)。</span><span class="sxs-lookup"><span data-stu-id="74082-303">Open the [integrated terminal](https://code.visualstudio.com/docs/editor/integrated-terminal).</span></span>
* <span data-ttu-id="74082-304">将目录更改为 (`cd`) 包含项目的文件夹。</span><span class="sxs-lookup"><span data-stu-id="74082-304">Change directories (`cd`) to a folder which will contain the project.</span></span>
* <span data-ttu-id="74082-305">运行下面的命令：</span><span class="sxs-lookup"><span data-stu-id="74082-305">Run the following command:</span></span>

   ```dotnetcli
   dotnet new mvc -o MvcMovie
   code -r MvcMovie
   ```

  * <span data-ttu-id="74082-306">一个对话框随即出现，其中包含：“‘MvcMovie’中缺少进行生成和调试所需的资产。是否添加它们?”</span><span class="sxs-lookup"><span data-stu-id="74082-306">A dialog box appears with **Required assets to build and debug are missing from 'MvcMovie'. Add them?**</span></span>  <span data-ttu-id="74082-307">选择“是”</span><span class="sxs-lookup"><span data-stu-id="74082-307">Select **Yes**</span></span>

  * <span data-ttu-id="74082-308">`dotnet new mvc -o MvcMovie`：在 MvcMovie 文件夹中创建一个新的 ASP.NET Core MVC 项目。</span><span class="sxs-lookup"><span data-stu-id="74082-308">`dotnet new mvc -o MvcMovie`: creates a new ASP.NET Core MVC project in the *MvcMovie* folder.</span></span>
  * <span data-ttu-id="74082-309">`code -r MvcMovie`：在 Visual Studio Code 中加载 MvcMovie.csproj 项目文件。</span><span class="sxs-lookup"><span data-stu-id="74082-309">`code -r MvcMovie`: Loads the *MvcMovie.csproj* project file in Visual Studio Code.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-310">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-310">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* <span data-ttu-id="74082-311">选择“文件”>“新建解决方案” 。</span><span class="sxs-lookup"><span data-stu-id="74082-311">Select **File** > **New Solution**.</span></span>

  ![macOS 新建解决方案](./start-mvc/_static/new_project_vsmac.png)

* <span data-ttu-id="74082-313">在版本 8.6 之前的 Visual Studio for Mac 中，依次选择“.NET Core” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="74082-313">In Visual Studio for Mac earlier than version 8.6, select **.NET Core** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span> <span data-ttu-id="74082-314">在版本 8.6 或更高版本中，依次选择“Web 和控制台” > “应用” > “Web 应用程序(模型-视图-控制器)” > “下一步”。   </span><span class="sxs-lookup"><span data-stu-id="74082-314">In version 8.6 or later, select **Web and Console** > **App** > **Web Application (Model-View-Controller)** > **Next**.</span></span>

* <span data-ttu-id="74082-315">在“配置新的 Web 应用”对话框中：</span><span class="sxs-lookup"><span data-stu-id="74082-315">In the **Configure your new Web Application** dialog:</span></span>

  * <span data-ttu-id="74082-316">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="74082-316">Confirm that **Authentication** is set to **No Authentication**.</span></span>
  * <span data-ttu-id="74082-317">如果看到用于选择“目标框架”的选项，请选择最新的 2.x 版本。</span><span class="sxs-lookup"><span data-stu-id="74082-317">If presented an option to select a **Target Framework**, select the latest 2.x version.</span></span>

  <span data-ttu-id="74082-318">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="74082-318">Select **Next**.</span></span>

* <span data-ttu-id="74082-319">将项目命名为“MvcMovie”，然后选择“创建”。 </span><span class="sxs-lookup"><span data-stu-id="74082-319">Name the project **MvcMovie**, and then select **Create**.</span></span>

---

### <a name="run-the-app"></a><span data-ttu-id="74082-320">运行应用</span><span class="sxs-lookup"><span data-stu-id="74082-320">Run the app</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="74082-321">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="74082-321">Visual Studio</span></span>](#tab/visual-studio)

<span data-ttu-id="74082-322">选择 Ctrl+F5 以在非调试模式下运行应用。</span><span class="sxs-lookup"><span data-stu-id="74082-322">Select **Ctrl-F5** to run the app in non-debug mode.</span></span>

[!INCLUDE[](~/includes/trustCertVS.md)]

* <span data-ttu-id="74082-323">Visual Studio 启动 [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) 并运行应用。</span><span class="sxs-lookup"><span data-stu-id="74082-323">Visual Studio starts [IIS Express](/iis/extensions/introduction-to-iis-express/iis-express-overview) and runs the app.</span></span> <span data-ttu-id="74082-324">请注意，地址栏显示 `localhost:port#`，而不显示 `example.com` 之类的内容。</span><span class="sxs-lookup"><span data-stu-id="74082-324">Notice that the address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="74082-325">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-325">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="74082-326">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="74082-326">When Visual Studio creates a web project, a random port is used for the web server.</span></span>
* <span data-ttu-id="74082-327">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="74082-327">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="74082-328">许多开发人员更喜欢使用非调试模式快速启动应用并查看更改。</span><span class="sxs-lookup"><span data-stu-id="74082-328">Many developers prefer to use non-debug mode to quickly launch the app and view changes.</span></span>
* <span data-ttu-id="74082-329">可以从“调试”菜单项中以调试或非调试模式启动应用：</span><span class="sxs-lookup"><span data-stu-id="74082-329">You can launch the app in debug or non-debug mode from the **Debug** menu item:</span></span>

  ![调试菜单](start-mvc/_static/debug_menu.png)

* <span data-ttu-id="74082-331">可以通过选择“IIS Express”按钮来调试应用</span><span class="sxs-lookup"><span data-stu-id="74082-331">You can debug the app by selecting the **IIS Express** button</span></span>

  ![IIS Express](start-mvc/_static/iis_express.png)

* <span data-ttu-id="74082-333">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="74082-333">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="74082-334">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="74082-334">This app doesn't track personal information.</span></span> <span data-ttu-id="74082-335">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="74082-335">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="74082-337">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="74082-337">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-code"></a>[<span data-ttu-id="74082-339">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="74082-339">Visual Studio Code</span></span>](#tab/visual-studio-code)

<span data-ttu-id="74082-340">按 Ctrl+F5 以在不使用调试程序的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="74082-340">Press Ctrl+F5 to run without the debugger.</span></span>

[!INCLUDE[](~/includes/trustCertVSC.md)]

  <span data-ttu-id="74082-341">Visual Studio Code 启动 [Kestrel](xref:fundamentals/servers/kestrel)，启动浏览器并导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="74082-341">Visual Studio Code starts [Kestrel](xref:fundamentals/servers/kestrel), launches a browser, and navigates to `https://localhost:5001`.</span></span> <span data-ttu-id="74082-342">地址栏显示 `localhost:port:5001`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="74082-342">The address bar shows `localhost:port:5001` and not something like `example.com`.</span></span> <span data-ttu-id="74082-343">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-343">That's because `localhost` is the standard hostname for  local computer.</span></span> <span data-ttu-id="74082-344">Localhost 仅为来自本地计算机的 Web 请求提供服务。</span><span class="sxs-lookup"><span data-stu-id="74082-344">Localhost only serves web requests from the local computer.</span></span>

  <span data-ttu-id="74082-345">使用 Ctrl+F5 启动应用（非调试模式）后，可执行代码更改、保存文件、刷新浏览器和查看代码更改等操作。</span><span class="sxs-lookup"><span data-stu-id="74082-345">Launching the app with Ctrl+F5 (non-debug mode) allows you to make code changes, save the file, refresh the browser, and see the code changes.</span></span> <span data-ttu-id="74082-346">许多开发人员更喜欢使用非调试模式刷新页面并查看更改。</span><span class="sxs-lookup"><span data-stu-id="74082-346">Many developers prefer to use non-debug mode to refresh the page and view changes.</span></span>

* <span data-ttu-id="74082-347">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="74082-347">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="74082-348">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="74082-348">This app doesn't track personal information.</span></span> <span data-ttu-id="74082-349">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="74082-349">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](start-mvc/_static/privacy.png)

  <span data-ttu-id="74082-351">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="74082-351">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](start-mvc/_static/home2.2.png)

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="74082-353">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="74082-353">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="74082-354">选择“运行” > “开始执行(不调试)”以启动应用 。</span><span class="sxs-lookup"><span data-stu-id="74082-354">Select **Run** > **Start Without Debugging** to launch the app.</span></span> <span data-ttu-id="74082-355">Visual Studio for Mac 启动 [Kestrel](xref:fundamentals/servers/index#kestrel) 服务器，启动浏览器并导航到 `http://localhost:port`，其中的 port 是随机选择的端口号。</span><span class="sxs-lookup"><span data-stu-id="74082-355">Visual Studio for Mac starts [Kestrel](xref:fundamentals/servers/index#kestrel) server, launches a browser, and navigates to `http://localhost:port`, where *port* is a randomly chosen port number.</span></span>

[!INCLUDE[](~/includes/trustCertMac.md)]

* <span data-ttu-id="74082-356">地址栏显示 `localhost:port#`，而不是显示 `example.com`。</span><span class="sxs-lookup"><span data-stu-id="74082-356">The address bar shows `localhost:port#` and not something like `example.com`.</span></span> <span data-ttu-id="74082-357">这是因为 `localhost` 是本地计算机的标准主机名。</span><span class="sxs-lookup"><span data-stu-id="74082-357">That's because `localhost` is the standard hostname for your local computer.</span></span> <span data-ttu-id="74082-358">Visual Studio 创建 Web 项目时，Web 服务器使用的是随机端口。</span><span class="sxs-lookup"><span data-stu-id="74082-358">When Visual Studio creates a web project, a random port is used for the web server.</span></span> <span data-ttu-id="74082-359">运行应用时，将看到不同的端口号。</span><span class="sxs-lookup"><span data-stu-id="74082-359">When you run the app, you'll see a different port number.</span></span>
* <span data-ttu-id="74082-360">可以从“运行”菜单中以调试或非调试模式启动应用。</span><span class="sxs-lookup"><span data-stu-id="74082-360">You can launch the app in debug or non-debug mode from the **Run** menu.</span></span>

* <span data-ttu-id="74082-361">选择“接受”以同意跟踪。</span><span class="sxs-lookup"><span data-stu-id="74082-361">Select **Accept** to consent to tracking.</span></span> <span data-ttu-id="74082-362">此应用不会跟踪个人信息。</span><span class="sxs-lookup"><span data-stu-id="74082-362">This app doesn't track personal information.</span></span> <span data-ttu-id="74082-363">模板生成的代码包含有助于符合[一般数据保护条例 (GDPR)](xref:security/gdpr) 的资产。</span><span class="sxs-lookup"><span data-stu-id="74082-363">The template generated code includes assets to help meet [General Data Protection Regulation (GDPR)](xref:security/gdpr).</span></span>

  ![主页或索引页](./start-mvc/_static/output_privacy_macos.png)

  <span data-ttu-id="74082-365">下图展示了接受跟踪后的应用：</span><span class="sxs-lookup"><span data-stu-id="74082-365">The following image shows the app after accepting tracking:</span></span>

  ![主页或索引页](./start-mvc/_static/output_macos.png)

---

[!INCLUDE[](~/includes/vs-vsc-vsmac-help.md)]

<span data-ttu-id="74082-367">在本教程的下一部分中，你将了解 MVC 并开始撰写一些代码。</span><span class="sxs-lookup"><span data-stu-id="74082-367">In the next part of this tutorial, you learn about MVC and start writing some code.</span></span>

> [!div class="step-by-step"]
> [<span data-ttu-id="74082-368">下一页</span><span class="sxs-lookup"><span data-stu-id="74082-368">Next</span></span>](adding-controller.md)

::: moniker-end
