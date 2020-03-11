---
title: 结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly
author: guardrex
description: 创建结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly 的聊天应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/31/2020
no-loc:
- Blazor
- SignalR
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 765595863cb18c889c36b756392bc8163e73c591
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78649758"
---
# <a name="use-aspnet-core-signalr-with-blazor-webassembly"></a><span data-ttu-id="8316b-103">结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="8316b-103">Use ASP.NET Core SignalR with Blazor WebAssembly</span></span>

<span data-ttu-id="8316b-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="8316b-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="8316b-105">本教程介绍使用 SignalR 和 Blazor WebAssembly 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="8316b-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="8316b-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="8316b-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8316b-107">创建 Blazor WebAssembly 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="8316b-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="8316b-108">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="8316b-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="8316b-109">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="8316b-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="8316b-110">添加 SignalR 服务和 SignalR 集线器的终结点</span><span class="sxs-lookup"><span data-stu-id="8316b-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="8316b-111">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="8316b-111">Add Razor component code for chat</span></span>

<span data-ttu-id="8316b-112">在本教程结束时，你将拥有一个正常运行的聊天应用。</span><span class="sxs-lookup"><span data-stu-id="8316b-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="8316b-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="8316b-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="8316b-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="8316b-114">Prerequisites</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8316b-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8316b-115">Visual Studio</span></span>](#tab/visual-studio)

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.1.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="8316b-116">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8316b-116">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8316b-117">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8316b-117">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.1.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="8316b-118">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8316b-118">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

## <a name="create-a-hosted-blazor-webassembly-app-project"></a><span data-ttu-id="8316b-119">创建托管 Blazor WebAssembly 应用项目</span><span class="sxs-lookup"><span data-stu-id="8316b-119">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="8316b-120">安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 模板。</span><span class="sxs-lookup"><span data-stu-id="8316b-120">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template.</span></span> <span data-ttu-id="8316b-121">当 Blazor WebAssembly 处于预览状态时，[ Microsoft.AspNetCore.Blazor.Templates ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 包具有预览版本。</span><span class="sxs-lookup"><span data-stu-id="8316b-121">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span> <span data-ttu-id="8316b-122">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-122">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
```

<span data-ttu-id="8316b-123">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="8316b-123">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8316b-124">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8316b-124">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8316b-125">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="8316b-125">Create a new project.</span></span>

1. <span data-ttu-id="8316b-126">选择“Blazor 应用”  ，然后选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-126">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="8316b-127">在“项目名称”  字段中键入“BlazorSignalRApp”。</span><span class="sxs-lookup"><span data-stu-id="8316b-127">Type "BlazorSignalRApp" in the **Project name** field.</span></span> <span data-ttu-id="8316b-128">确认“位置”  条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="8316b-128">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="8316b-129">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-129">Select **Create**.</span></span>

1. <span data-ttu-id="8316b-130">选择“Blazor WebAssembly 应用”  模板。</span><span class="sxs-lookup"><span data-stu-id="8316b-130">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="8316b-131">在“高级”  下选中“托管的 ASP.NET Core”  复选框。</span><span class="sxs-lookup"><span data-stu-id="8316b-131">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="8316b-132">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-132">Select **Create**.</span></span>

> [!NOTE]
> <span data-ttu-id="8316b-133">如果升级或安装了新版 Visual Studio，并且 Blazor WebAssembly 模板没有出现在 VS UI 中，请使用前面显示的 `dotnet new` 命令重新安装该模板。</span><span class="sxs-lookup"><span data-stu-id="8316b-133">If you upgraded or installed a new version of Visual Studio and the Blazor WebAssembly template doesn't appear in the VS UI, reinstall the template using the `dotnet new` command shown previously.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8316b-134">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8316b-134">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8316b-135">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-135">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="8316b-136">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="8316b-136">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="8316b-137">当显示添加资产以生成和调试应用的对话框时，选择“是”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-137">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="8316b-138">Visual Studio Code 会自动添加“.vscode”  文件夹以及生成的“launch.json”  和“tasks.json”  文件。</span><span class="sxs-lookup"><span data-stu-id="8316b-138">Visual Studio Code automatically adds the *.vscode* folder with generated *launch.json* and *tasks.json* files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8316b-139">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8316b-139">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8316b-140">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-140">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="8316b-141">在 Visual Studio for Mac 中，通过导航到项目文件夹并打开项目的解决方案文件 (.sln  ) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="8316b-141">In Visual Studio for Mac, open the project by navigating to the project folder and opening the project's solution file (*.sln*).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8316b-142">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8316b-142">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="8316b-143">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-143">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-signalr-client-library"></a><span data-ttu-id="8316b-144">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="8316b-144">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8316b-145">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8316b-145">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="8316b-146">在“解决方案资源管理器”中，右键单击“BlazorSignalRApp.Client”项目，然后选择“管理 NuGet 包”    。</span><span class="sxs-lookup"><span data-stu-id="8316b-146">In **Solution Explorer**, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="8316b-147">在“管理 NuGet 包”  对话框中，确认“包源”  设置为“nuget.org”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-147">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to *nuget.org*.</span></span>

1. <span data-ttu-id="8316b-148">选择“浏览”  后，在搜索框中键入“Microsoft.AspNetCore.SignalR.Client”。</span><span class="sxs-lookup"><span data-stu-id="8316b-148">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="8316b-149">在搜索结果中，选中 `Microsoft.AspNetCore.SignalR.Client` 包，然后选择“安装”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-149">In the search results, select the `Microsoft.AspNetCore.SignalR.Client` package and select **Install**.</span></span>

1. <span data-ttu-id="8316b-150">如果出现“预览更改”  对话框，则选择“确定”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-150">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="8316b-151">如果出现“许可证接受”  对话框，如果你同意许可条款，请选择“我接受”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-151">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8316b-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8316b-152">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="8316b-153">在“集成终端”  （工具栏上的“视图”   > “终端”  ）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-153">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8316b-154">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8316b-154">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8316b-155">在“解决方案”  边栏中，右键单击“BlazorSignalRApp.Client”  项目，然后选择“管理 NuGet 包”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-155">In the **Solution** sidebar, right-click the **BlazorSignalRApp.Client** project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="8316b-156">在“管理 NuGet 包”  对话框中，确认源下拉列表设置为“nuget.org”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-156">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to *nuget.org*.</span></span>

1. <span data-ttu-id="8316b-157">选择“浏览”  后，在搜索框中键入“Microsoft.AspNetCore.SignalR.Client”。</span><span class="sxs-lookup"><span data-stu-id="8316b-157">With **Browse** selected, type "Microsoft.AspNetCore.SignalR.Client" in the search box.</span></span>

1. <span data-ttu-id="8316b-158">在搜索结果中，选中 `Microsoft.AspNetCore.SignalR.Client` 包旁边的复选框，然后选择“添加包”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-158">In the search results, select the check box next to the `Microsoft.AspNetCore.SignalR.Client` package and select **Add Package**.</span></span>

1. <span data-ttu-id="8316b-159">出现“许可证接受”  对话框时，如果你同意许可条款，请选择“接受”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-159">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8316b-160">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8316b-160">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="8316b-161">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-161">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

## <a name="add-a-signalr-hub"></a><span data-ttu-id="8316b-162">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="8316b-162">Add a SignalR hub</span></span>

<span data-ttu-id="8316b-163">在“BlazorSignalRApp.Server”  项目中，创建一个“Hubs”  （复数）文件夹，并添加以下 `ChatHub` 类 (Hubs/ChatHub.cs  )：</span><span class="sxs-lookup"><span data-stu-id="8316b-163">In the **BlazorSignalRApp.Server** project, create a *Hubs* (plural) folder and add the following `ChatHub` class (*Hubs/ChatHub.cs*):</span></span>

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

## <a name="add-signalr-services-and-an-endpoint-for-the-signalr-hub"></a><span data-ttu-id="8316b-164">添加 SignalR 服务和 SignalR 集线器的终结点</span><span class="sxs-lookup"><span data-stu-id="8316b-164">Add SignalR services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="8316b-165">在“BlazorSignalRApp.Server”  项目中，打开“Startup.cs”  文件。</span><span class="sxs-lookup"><span data-stu-id="8316b-165">In the **BlazorSignalRApp.Server** project, open the *Startup.cs* file.</span></span>

1. <span data-ttu-id="8316b-166">将 `ChatHub` 类的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="8316b-166">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

1. <span data-ttu-id="8316b-167">将 SignalR 服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="8316b-167">Add the SignalR services to `Startup.ConfigureServices`:</span></span>

   ```csharp
   services.AddSignalR();
   ```

1. <span data-ttu-id="8316b-168">在默认控制器路由的终结点与客户端回退的终结点之间的 `Startup.Configure` 中，为集线器添加终结点：</span><span class="sxs-lookup"><span data-stu-id="8316b-168">In `Startup.Configure` between the endpoints for the default controller route and the client-side fallback, add an endpoint for the hub:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet&highlight=4)]

## <a name="add-razor-component-code-for-chat"></a><span data-ttu-id="8316b-169">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="8316b-169">Add Razor component code for chat</span></span>

1. <span data-ttu-id="8316b-170">在“BlazorSignalRApp.Client”  项目中，打开“Pages/Index.razor”  文件。</span><span class="sxs-lookup"><span data-stu-id="8316b-170">In the **BlazorSignalRApp.Client** project, open the *Pages/Index.razor* file.</span></span>

1. <span data-ttu-id="8316b-171">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="8316b-171">Replace the markup with the following code:</span></span>

[!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

## <a name="run-the-app"></a><span data-ttu-id="8316b-172">运行应用</span><span class="sxs-lookup"><span data-stu-id="8316b-172">Run the app</span></span>

1. <span data-ttu-id="8316b-173">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="8316b-173">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="8316b-174">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="8316b-174">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="8316b-175">在“解决方案资源管理器”  中，选择“BlazorSignalRApp.Server”  项目。</span><span class="sxs-lookup"><span data-stu-id="8316b-175">In **Solution Explorer**, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="8316b-176">按 Ctrl+F5 可运行应用而不进行调试  。</span><span class="sxs-lookup"><span data-stu-id="8316b-176">Press **Ctrl+F5** to run the app without debugging.</span></span>

1. <span data-ttu-id="8316b-177">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="8316b-177">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8316b-178">选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。</span><span class="sxs-lookup"><span data-stu-id="8316b-178">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="8316b-179">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="8316b-179">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8316b-181">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8316b-181">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="8316b-182">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="8316b-182">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="8316b-183">从工具栏选择“调试”   > “运行(不调试)”  。</span><span class="sxs-lookup"><span data-stu-id="8316b-183">Select **Debug** > **Run Without Debugging** from the toolbar.</span></span>

1. <span data-ttu-id="8316b-184">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="8316b-184">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8316b-185">选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。</span><span class="sxs-lookup"><span data-stu-id="8316b-185">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="8316b-186">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="8316b-186">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8316b-188">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8316b-188">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="8316b-189">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="8316b-189">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="8316b-190">在“解决方案”  边栏中，选择“BlazorSignalRApp.Server”  项目。</span><span class="sxs-lookup"><span data-stu-id="8316b-190">In the **Solution** sidebar, select the **BlazorSignalRApp.Server** project.</span></span> <span data-ttu-id="8316b-191">从菜单中选择“运行” > “开始执行(不调试)”   。</span><span class="sxs-lookup"><span data-stu-id="8316b-191">From the menu, select **Run** > **Start Without Debugging**.</span></span>

1. <span data-ttu-id="8316b-192">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="8316b-192">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8316b-193">选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。</span><span class="sxs-lookup"><span data-stu-id="8316b-193">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="8316b-194">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="8316b-194">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8316b-196">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8316b-196">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="8316b-197">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="8316b-197">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="8316b-198">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="8316b-198">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="8316b-199">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="8316b-199">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="8316b-200">选择任一浏览器，输入名称和消息，然后选择“发送”按钮  。</span><span class="sxs-lookup"><span data-stu-id="8316b-200">Choose either browser, enter a name and message, and select the **Send** button.</span></span> <span data-ttu-id="8316b-201">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="8316b-201">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="8316b-203">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="8316b-203">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="8316b-204">后续步骤</span><span class="sxs-lookup"><span data-stu-id="8316b-204">Next steps</span></span>

<span data-ttu-id="8316b-205">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="8316b-205">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="8316b-206">创建 Blazor WebAssembly 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="8316b-206">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="8316b-207">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="8316b-207">Add the SignalR client library</span></span>
> * <span data-ttu-id="8316b-208">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="8316b-208">Add a SignalR hub</span></span>
> * <span data-ttu-id="8316b-209">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="8316b-209">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="8316b-210">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="8316b-210">Add Razor component code for chat</span></span>

<span data-ttu-id="8316b-211">若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：</span><span class="sxs-lookup"><span data-stu-id="8316b-211">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <xref:blazor/index>

## <a name="additional-resources"></a><span data-ttu-id="8316b-212">其他资源</span><span class="sxs-lookup"><span data-stu-id="8316b-212">Additional resources</span></span>

* <xref:signalr/introduction>
