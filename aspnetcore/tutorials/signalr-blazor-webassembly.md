---
title: 将 ASP.NET Core SignalR 与承载的 Blazor WebAssembly 应用一起使用
author: guardrex
description: 创建结合使用 ASP.NET Core SignalR 和 Blazor WebAssembly 的聊天应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 11/11/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: b2f58fb29e451628aead4ad35c7272a1409cf3d8
ms.sourcegitcommit: 3593c4efa707edeaaceffbfa544f99f41fc62535
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/04/2021
ms.locfileid: "97797348"
---
# <a name="use-aspnet-core-no-locsignalr-with-a-hosted-no-locblazor-webassembly-app"></a><span data-ttu-id="78377-103">将 ASP.NET Core SignalR 与承载的 Blazor WebAssembly 应用一起使用</span><span class="sxs-lookup"><span data-stu-id="78377-103">Use ASP.NET Core SignalR with a hosted Blazor WebAssembly app</span></span>

<span data-ttu-id="78377-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="78377-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="78377-105">本教程介绍结合使用 SignalR 和 Blazor WebAssembly 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="78377-105">This tutorial teaches the basics of building a real-time app using SignalR with Blazor WebAssembly.</span></span> <span data-ttu-id="78377-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="78377-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="78377-107">创建 Blazor WebAssembly 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="78377-107">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="78377-108">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="78377-108">Add the SignalR client library</span></span>
> * <span data-ttu-id="78377-109">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="78377-109">Add a SignalR hub</span></span>
> * <span data-ttu-id="78377-110">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="78377-110">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="78377-111">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="78377-111">Add Razor component code for chat</span></span>

<span data-ttu-id="78377-112">在本教程结束时，你将拥有一个正常运行的聊天应用。</span><span class="sxs-lookup"><span data-stu-id="78377-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="78377-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="78377-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="78377-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="78377-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="78377-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78377-115">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="78377-116">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.8 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="78377-116">[Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="78377-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78377-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78377-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78377-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="78377-119">Visual Studio for Mac 8.8 或更高版本</span><span class="sxs-lookup"><span data-stu-id="78377-119">Visual Studio for Mac version 8.8 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="78377-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="78377-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="78377-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78377-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="78377-122">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="78377-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="78377-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78377-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78377-124">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78377-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="78377-125">Visual Studio for Mac 版本 8.6 或更高版本</span><span class="sxs-lookup"><span data-stu-id="78377-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="78377-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="78377-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="78377-127">创建托管 Blazor WebAssembly 应用项目</span><span class="sxs-lookup"><span data-stu-id="78377-127">Create a hosted Blazor WebAssembly app project</span></span>

<span data-ttu-id="78377-128">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="78377-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78377-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78377-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="78377-130">需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="78377-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="78377-131">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="78377-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="78377-132">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="78377-132">Create a new project.</span></span>

1. <span data-ttu-id="78377-133">选择“Blazor 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="78377-133">Select **Blazor App** and select **Next**.</span></span>

1. <span data-ttu-id="78377-134">在“项目名称”字段中键入 `BlazorSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="78377-134">Type `BlazorSignalRApp` in the **Project name** field.</span></span> <span data-ttu-id="78377-135">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="78377-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="78377-136">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="78377-136">Select **Create**.</span></span>

1. <span data-ttu-id="78377-137">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="78377-137">Choose the **Blazor WebAssembly App** template.</span></span>

1. <span data-ttu-id="78377-138">在“高级”下选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="78377-138">Under **Advanced**, select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="78377-139">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="78377-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="78377-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78377-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="78377-141">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output BlazorSignalRApp
   ```

1. <span data-ttu-id="78377-142">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="78377-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="78377-143">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="78377-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="78377-144">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="78377-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78377-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78377-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="78377-146">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="78377-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="78377-147">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="78377-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="78377-148">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="78377-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="78377-149">选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="78377-149">Choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="78377-150">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="78377-150">Select **Next**.</span></span>

1. <span data-ttu-id="78377-151">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="78377-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="78377-152">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="78377-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="78377-153">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="78377-153">Select **Next**.</span></span>

1. <span data-ttu-id="78377-154">在“项目名称”字段中，将应用命名为 `BlazorSignalRApp`。</span><span class="sxs-lookup"><span data-stu-id="78377-154">In the **Project Name** field, name the app `BlazorSignalRApp`.</span></span> <span data-ttu-id="78377-155">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="78377-155">Select **Create**.</span></span>

   <span data-ttu-id="78377-156">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="78377-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="78377-157">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="78377-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="78377-158">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="78377-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="78377-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="78377-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="78377-160">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output BlazorSignalRApp
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="78377-161">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="78377-161">Add the SignalR client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78377-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78377-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="78377-163">在“解决方案资源管理器”中，右键单击 `BlazorSignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="78377-163">In **Solution Explorer**, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="78377-164">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="78377-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="78377-165">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="78377-165">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="78377-166">在搜索结果中，选中 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="78377-166">In the search results, select the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="78377-167">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="78377-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="78377-168">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="78377-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="78377-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78377-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="78377-170">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-170">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78377-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78377-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="78377-172">在“解决方案资源管理器”中，右键单击 `BlazorSignalRApp.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="78377-172">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="78377-173">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="78377-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="78377-174">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.SignalR.Client`”。</span><span class="sxs-lookup"><span data-stu-id="78377-174">With **Browse** selected, type `Microsoft.AspNetCore.SignalR.Client` in the search box.</span></span>

1. <span data-ttu-id="78377-175">在搜索结果中，选中 [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) 包旁边的复选框，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="78377-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.SignalR.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.SignalR.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="78377-176">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="78377-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="78377-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="78377-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="78377-178">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd BlazorSignalRApp
dotnet add Client package Microsoft.AspNetCore.SignalR.Client
```

---

::: moniker range="< aspnetcore-5.0"

## <a name="add-the-systemtextencodingsweb-package"></a><span data-ttu-id="78377-179">添加 System.Text.Encodings.Web 包</span><span class="sxs-lookup"><span data-stu-id="78377-179">Add the System.Text.Encodings.Web package</span></span>

<span data-ttu-id="78377-180">由于在 ASP.NET Core 3.1 应用中使用 [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 时出现包解析问题，`BlazorSignalRApp.Server` 项目需要 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 的包引用。</span><span class="sxs-lookup"><span data-stu-id="78377-180">Due to a package resolution issue when using [`System.Text.Json`](https://www.nuget.org/packages/System.Text.Json) 5.0.0 in an ASP.NET Core 3.1 app, the `BlazorSignalRApp.Server` project requires a package reference for [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web).</span></span> <span data-ttu-id="78377-181">在未来的 .NET 5 修补程序版本中，将解决基础问题。</span><span class="sxs-lookup"><span data-stu-id="78377-181">The underlying issue will be resolved in a future patch release of .NET 5.</span></span> <span data-ttu-id="78377-182">有关详细信息，请参阅 [System.Text.Json 定义无依赖项的 netcoreapp3.0（dotnet/运行时 #45560）](https://github.com/dotnet/runtime/issues/45560)。</span><span class="sxs-lookup"><span data-stu-id="78377-182">For more information, see [System.Text.Json defines netcoreapp3.0 with no dependencies (dotnet/runtime #45560)](https://github.com/dotnet/runtime/issues/45560).</span></span>

<span data-ttu-id="78377-183">若要将 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 添加到 ASP.NET Core 3.1 托管 Blazor 解决方案的 `BlazorSignalRApp.Server` 项目，请按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="78377-183">To add [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) to the `BlazorSignalRApp.Server` project of the ASP.NET Core 3.1 hosted Blazor solution, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78377-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78377-184">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="78377-185">在“解决方案资源管理器”中，右键单击 `BlazorSignalRApp.Server` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="78377-185">In **Solution Explorer**, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="78377-186">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="78377-186">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="78377-187">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="78377-187">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="78377-188">在搜索结果中，选中 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="78377-188">In the search results, select the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Install**.</span></span>

1. <span data-ttu-id="78377-189">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="78377-189">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="78377-190">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="78377-190">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="78377-191">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78377-191">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="78377-192">在“集成终端”（工具栏上的“视图”>“终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-192">In the **Integrated Terminal** (**View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78377-193">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78377-193">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="78377-194">在“解决方案资源管理器”中，右键单击 `BlazorSignalRApp.Server` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="78377-194">In the **Solution** sidebar, right-click the `BlazorSignalRApp.Server` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="78377-195">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="78377-195">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="78377-196">选择“浏览”后，在搜索框中键入“`System.Text.Encodings.Web`”。</span><span class="sxs-lookup"><span data-stu-id="78377-196">With **Browse** selected, type `System.Text.Encodings.Web` in the search box.</span></span>

1. <span data-ttu-id="78377-197">在搜索结果中，选中 [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) 包旁边的复选框，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="78377-197">In the search results, select the check box next to the [`System.Text.Encodings.Web`](https://www.nuget.org/packages/System.Text.Encodings.Web) package and select **Add Package**.</span></span>

1. <span data-ttu-id="78377-198">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="78377-198">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="78377-199">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="78377-199">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="78377-200">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-200">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet add Server package System.Text.Encodings.Web
```

---

::: moniker-end

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="78377-201">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="78377-201">Add a SignalR hub</span></span>

<span data-ttu-id="78377-202">在 `BlazorSignalRApp.Server` 项目中，创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="78377-202">In the `BlazorSignalRApp.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="78377-203">为 SignalR 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="78377-203">Add services and an endpoint for the SignalR hub</span></span>

1. <span data-ttu-id="78377-204">在 `BlazorSignalRApp.Server` 项目中打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="78377-204">In the `BlazorSignalRApp.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="78377-205">将 `ChatHub` 类的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="78377-205">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using BlazorSignalRApp.Server.Hubs;
   ```

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="78377-206">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="78377-206">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,6-10)]
   
1. <span data-ttu-id="78377-207">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="78377-207">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="78377-208">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="78377-208">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="78377-209">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="78377-209">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,26)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="78377-210">将 SignalR 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="78377-210">Add SignalR and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]
   
1. <span data-ttu-id="78377-211">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="78377-211">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="78377-212">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="78377-212">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="78377-213">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="78377-213">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="78377-214">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="78377-214">Add Razor component code for chat</span></span>

1. <span data-ttu-id="78377-215">在 `BlazorSignalRApp.Client` 项目中打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="78377-215">In the `BlazorSignalRApp.Client` project, open the `Pages/Index.razor` file.</span></span>

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="78377-216">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="78377-216">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="78377-217">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="78377-217">Replace the markup with the following code:</span></span>

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/BlazorSignalRApp/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="78377-218">运行应用</span><span class="sxs-lookup"><span data-stu-id="78377-218">Run the app</span></span>

<span data-ttu-id="78377-219">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="78377-219">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="78377-220">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="78377-220">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="78377-221">在“解决方案资源管理器”中，选择 `BlazorSignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="78377-221">In **Solution Explorer**, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="78377-222">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="78377-222">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="78377-223">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="78377-223">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="78377-224">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="78377-224">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="78377-225">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="78377-225">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="78377-227">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="78377-227">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="78377-228">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="78377-228">Visual Studio Code</span></span>](#tab/visual-studio-code)

::: moniker range=">= aspnetcore-5.0"

1. <span data-ttu-id="78377-229">当 VS Code 主动为服务器应用创建一个启动配置文件 (`.vscode/launch.json`) 时，`program` 条目如下所示，它指向应用的程序集 (`{APPLICATION NAME}.Server.dll`)：</span><span class="sxs-lookup"><span data-stu-id="78377-229">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

1. <span data-ttu-id="78377-230">当 VS Code 主动为服务器应用创建一个启动配置文件 (`.vscode/launch.json`) 时，`program` 条目如下所示，它指向应用的程序集 (`{APPLICATION NAME}.Server.dll`)：</span><span class="sxs-lookup"><span data-stu-id="78377-230">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="78377-231">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="78377-231">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="78377-232">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="78377-232">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="78377-233">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="78377-233">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="78377-234">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="78377-234">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="78377-236">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="78377-236">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="78377-237">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="78377-237">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="78377-238">在“解决方案”边栏中，选择 `BlazorSignalRApp.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="78377-238">In the **Solution** sidebar, select the `BlazorSignalRApp.Server` project.</span></span> <span data-ttu-id="78377-239">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="78377-239">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="78377-240">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="78377-240">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="78377-241">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="78377-241">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="78377-242">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="78377-242">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="78377-244">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="78377-244">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="78377-245">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="78377-245">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="78377-246">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="78377-246">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="78377-247">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="78377-247">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="78377-248">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="78377-248">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="78377-249">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="78377-249">The name and message are displayed on both pages instantly:</span></span>

   ![SignalR Blazor WebAssembly 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="78377-251">Quotes:*Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="78377-251">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="78377-252">后续步骤</span><span class="sxs-lookup"><span data-stu-id="78377-252">Next steps</span></span>

<span data-ttu-id="78377-253">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="78377-253">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="78377-254">创建 Blazor WebAssembly 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="78377-254">Create a Blazor WebAssembly Hosted app project</span></span>
> * <span data-ttu-id="78377-255">添加 SignalR 客户端库</span><span class="sxs-lookup"><span data-stu-id="78377-255">Add the SignalR client library</span></span>
> * <span data-ttu-id="78377-256">添加 SignalR 集线器</span><span class="sxs-lookup"><span data-stu-id="78377-256">Add a SignalR hub</span></span>
> * <span data-ttu-id="78377-257">添加 SignalR 服务和 SignalR 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="78377-257">Add SignalR services and an endpoint for the SignalR hub</span></span>
> * <span data-ttu-id="78377-258">添加用于聊天的 Razor 组件代码</span><span class="sxs-lookup"><span data-stu-id="78377-258">Add Razor component code for chat</span></span>

<span data-ttu-id="78377-259">若要了解有关生成 Blazor 应用的详细信息，请参阅 Blazor 文档：</span><span class="sxs-lookup"><span data-stu-id="78377-259">To learn more about building Blazor apps, see the Blazor documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="78377-260"><xref:blazor/index>
> [具有 Identity 服务器、WebSocket 和服务器发送事件的持有者令牌身份验证](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="78377-260"><xref:blazor/index>
[Bearer token authentication with Identity Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="78377-261">其他资源</span><span class="sxs-lookup"><span data-stu-id="78377-261">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="78377-262">SignalR 用于身份验证的跨源协商</span><span class="sxs-lookup"><span data-stu-id="78377-262">SignalR cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
