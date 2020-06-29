---
title: 结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly
author: guardrex
description: 创建结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly 的聊天应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 06/10/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 3f8aeec1e0471bab5034d1dcc8a42023f6b13c0d
ms.sourcegitcommit: 77729ba225d5143c0e3954db005906f4a5c7da95
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/20/2020
ms.locfileid: "85122095"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="81288-103">结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="81288-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="81288-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="81288-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="81288-105">本教程介绍使用 SignalR 和 Blazor WebAssembly 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="81288-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="81288-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="81288-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="81288-107">创建 Blazor WebAssembly 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="81288-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="81288-108">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="81288-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="81288-109">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="81288-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="81288-110">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="81288-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="81288-111">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="81288-111">Add Razor component code for chat</span></span>

<span data-ttu-id="81288-112">在本教程结束时，你将拥有一个正常运行的聊天应用。</span><span class="sxs-lookup"><span data-stu-id="81288-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="81288-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="81288-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="81288-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="81288-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81288-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81288-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="81288-116">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="81288-116">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="81288-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81288-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81288-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="81288-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="81288-119">Visual Studio for Mac 版本 8.6 或更高版本</span><span class="sxs-lookup"><span data-stu-id="81288-119">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="81288-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="81288-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="81288-121">创建托管 Blazor WebAssembly 应用项目</span><span class="sxs-lookup"><span data-stu-id="81288-121">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="81288-122">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="81288-122">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81288-123">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81288-123">Visual Studio</span></span>](#tab/visual-studio)

> [!NOTE]
> <span data-ttu-id="81288-124">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="81288-124">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

1. <span data-ttu-id="81288-125">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="81288-125">Create a new project.</span></span>

1. <span data-ttu-id="81288-126">选择“Blazor 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="81288-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="81288-127">在“项目名称”字段中键入 `BlazorSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="81288-127">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="81288-128">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="81288-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="81288-129">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="81288-129">Select **Create**.</span></span>

1. <span data-ttu-id="81288-130">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="81288-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="81288-131">在“高级”下选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="81288-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="81288-132">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="81288-132">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="81288-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81288-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="81288-134">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="81288-134">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="81288-135">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="81288-135">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="81288-136">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="81288-136">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="81288-137">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="81288-137">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81288-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="81288-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="81288-139">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="81288-139">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="81288-140">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="81288-140">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="81288-141">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="81288-141">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="81288-142">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="81288-142">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="81288-143">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="81288-143">Select **Next**.</span></span>

   <span data-ttu-id="81288-144">确认以下配置：</span><span class="sxs-lookup"><span data-stu-id="81288-144">Confirm the following configurations:</span></span>

   * <span data-ttu-id="81288-145">“目标框架”设置为“.NET Core 3.1” 。</span><span class="sxs-lookup"><span data-stu-id="81288-145">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="81288-146">“身份验证”设置为“无身份验证” 。</span><span class="sxs-lookup"><span data-stu-id="81288-146">**Authentication** set to **No Authentication**.</span></span>

   <span data-ttu-id="81288-147">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="81288-147">Select the **ASP.NET Core Hosted** check box.</span></span>

   <span data-ttu-id="81288-148">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="81288-148">Select **Next**.</span></span>

1. <span data-ttu-id="81288-149">在“项目名称”字段中，将应用命名为 `BlazorSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="81288-149">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="81288-150">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="81288-150">Select **Create**.</span></span>

   <span data-ttu-id="81288-151">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="81288-151">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="81288-152">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="81288-152">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="81288-153">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="81288-153">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81288-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="81288-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="81288-155">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="81288-155">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="81288-156">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="81288-156">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81288-157">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81288-157">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="81288-158">在“解决方案资源管理器”中，右键单击 `BlazorSignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="81288-158">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="81288-159">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="81288-159">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="81288-160">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="81288-160">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="81288-161">在搜索结果中，选中 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="81288-161">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="81288-162">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="81288-162">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="81288-163">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="81288-163">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="81288-164">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81288-164">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="81288-165">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="81288-165">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81288-166">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="81288-166">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="81288-167">在“解决方案资源管理器”中，右键单击 `BlazorSignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="81288-167">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="81288-168">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="81288-168">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="81288-169">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="81288-169">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="81288-170">在搜索结果中，选中 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="81288-170">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="81288-171">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="81288-171">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81288-172">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="81288-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="81288-173">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="81288-173">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="81288-174">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="81288-174">Add a SignalR hub</span></span>

<span data-ttu-id="81288-175">在 `BlazorSignalRApp.Server` 项目中，创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="81288-175">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="81288-176">为 SignalR 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="81288-176">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="81288-177">在 `BlazorSignalRApp.Server` 项目中打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="81288-177">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="81288-178">将 `ChatHub` 类的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="81288-178">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="81288-179">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="81288-179">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

1. <span data-ttu-id="81288-180">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="81288-180">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="81288-181">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="81288-181">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="81288-182">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="81288-182">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="81288-183">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="81288-183">Add Razor component code for chat</span></span>

1. <span data-ttu-id="81288-184">在 `BlazorSignalRApp.Client` 项目中打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="81288-184">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="81288-185">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="81288-185">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="81288-186">运行应用</span><span class="sxs-lookup"><span data-stu-id="81288-186">Run the app</span></span>

1. <span data-ttu-id="81288-187">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="81288-187">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="81288-188">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="81288-188">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="81288-189">在“解决方案资源管理器”中，选择 `BlazorSignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="81288-189">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="81288-190">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="81288-190">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="81288-191">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="81288-191">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="81288-192">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="81288-192">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="81288-193">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="81288-193">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="81288-194">![SignalRBlazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="81288-194">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="81288-195">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="81288-195">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="81288-196">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="81288-196">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="81288-197">当 VS Code 主动为服务器应用创建一个启动配置文件 (`.vscode/launch.json`) 时，`program` 条目如下所示，它指向应用的程序集 (`{APPLICATION NAME}.Server.dll`)：</span><span class="sxs-lookup"><span data-stu-id="81288-197">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

1. <span data-ttu-id="81288-198">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="81288-198">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="81288-199">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="81288-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="81288-200">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="81288-200">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="81288-201">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="81288-201">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="81288-202">![SignalRBlazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="81288-202">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="81288-203">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="81288-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="81288-204">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="81288-204">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="81288-205">在“解决方案”边栏中，选择 `BlazorSignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="81288-205">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="81288-206">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="81288-206">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="81288-207">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="81288-207">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="81288-208">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="81288-208">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="81288-209">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="81288-209">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="81288-210">![SignalRBlazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="81288-210">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="81288-211">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="81288-211">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="81288-212">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="81288-212">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="81288-213">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="81288-213">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="81288-214">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="81288-214">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="81288-215">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="81288-215">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="81288-216">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="81288-216">The name and message are displayed on both pages instantly:</span></span>

   <span data-ttu-id="81288-217">![SignalRBlazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span><span class="sxs-lookup"><span data-stu-id="81288-217">![SignalR Blazor WebAssembly sample app open in two browser windows showing exchanged messages.](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)</span></span>

   <span data-ttu-id="81288-218">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="81288-218">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="81288-219">后续步骤</span><span class="sxs-lookup"><span data-stu-id="81288-219">Next steps</span></span>

<span data-ttu-id="81288-220">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="81288-220">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="81288-221">创建 Blazor WebAssembly 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="81288-221">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="81288-222">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="81288-222">Add the SignalR client library</span></span>
> * <span data-ttu-id="81288-223">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="81288-223">Add a SignalR hub</span></span>
> * <span data-ttu-id="81288-224">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="81288-224">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="81288-225">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="81288-225">Add Razor component code for chat</span></span>

<span data-ttu-id="81288-226">若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：</span><span class="sxs-lookup"><span data-stu-id="81288-226">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="81288-227">其他资源</span><span class="sxs-lookup"><span data-stu-id="81288-227">Additional resources</span></span>

* <xref:signalr/introduction>
* <span data-ttu-id="81288-228">[SignalR 用于身份验证的跨源协商](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span><span class="sxs-lookup"><span data-stu-id="81288-228">[SignalR cross-origin negotiation for authentication](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)</span></span>
