---
title: '结合使用 ASP.NET Core :::no-loc(SignalR)::: 和 :::no-loc(Blazor WebAssembly):::'
author: guardrex
description: '创建结合使用 ASP.NET Core :::no-loc(SignalR)::: 和 :::no-loc(Blazor WebAssembly)::: 的聊天应用。'
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 10/01/2020
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
uid: tutorials/signalr-blazor-webassembly
ms.openlocfilehash: 81cbfb692ffbd0bb6335ccef6dd10ad6c20fb334
ms.sourcegitcommit: ca34c1ac578e7d3daa0febf1810ba5fc74f60bbf
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/30/2020
ms.locfileid: "93052690"
---
# <a name="use-aspnet-core-no-locsignalr-with-no-locblazor-webassembly"></a><span data-ttu-id="41398-103">结合使用 ASP.NET Core :::no-loc(SignalR)::: 和 :::no-loc(Blazor WebAssembly):::</span><span class="sxs-lookup"><span data-stu-id="41398-103">Use ASP.NET Core :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::</span></span>

<span data-ttu-id="41398-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="41398-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="41398-105">本教程介绍结合使用 :::no-loc(SignalR)::: 和 :::no-loc(Blazor WebAssembly)::: 生成实时应用的基础知识。</span><span class="sxs-lookup"><span data-stu-id="41398-105">This tutorial teaches the basics of building a real-time app using :::no-loc(SignalR)::: with :::no-loc(Blazor WebAssembly):::.</span></span> <span data-ttu-id="41398-106">您将学习如何：</span><span class="sxs-lookup"><span data-stu-id="41398-106">You learn how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="41398-107">创建 :::no-loc(Blazor WebAssembly)::: 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="41398-107">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="41398-108">添加 :::no-loc(SignalR)::: 客户端库</span><span class="sxs-lookup"><span data-stu-id="41398-108">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="41398-109">添加 :::no-loc(SignalR)::: 集线器</span><span class="sxs-lookup"><span data-stu-id="41398-109">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="41398-110">添加 :::no-loc(SignalR)::: 服务和 :::no-loc(SignalR)::: 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="41398-110">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="41398-111">添加用于聊天的 :::no-loc(Razor)::: 组件代码</span><span class="sxs-lookup"><span data-stu-id="41398-111">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="41398-112">在本教程结束时，你将拥有一个正常运行的聊天应用。</span><span class="sxs-lookup"><span data-stu-id="41398-112">At the end of this tutorial, you'll have a working chat app.</span></span>

<span data-ttu-id="41398-113">[查看或下载示例代码](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/)（[如何下载](xref:index#how-to-download-a-sample)）</span><span class="sxs-lookup"><span data-stu-id="41398-113">[View or download sample code](https://github.com/dotnet/AspNetCore.Docs/tree/master/aspnetcore/tutorials/signalr-blazor-webassembly/samples/) ([how to download](xref:index#how-to-download-a-sample))</span></span>

## <a name="prerequisites"></a><span data-ttu-id="41398-114">先决条件</span><span class="sxs-lookup"><span data-stu-id="41398-114">Prerequisites</span></span>

::: moniker range=">= aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="41398-115">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41398-115">Visual Studio</span></span>](#tab/visual-studio)

<!-- * [Visual Studio 2019 16.8 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload -->
* <span data-ttu-id="41398-116">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.8 或更高版本（预览版）](https://visualstudio.microsoft.com/vs/preview/)</span><span class="sxs-lookup"><span data-stu-id="41398-116">[Visual Studio 2019 16.8 or later (in preview)](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="41398-117">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41398-117">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-5.0.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41398-118">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="41398-118">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<!-- * [Visual Studio for Mac version 8.8 or later (in preview)](https://visualstudio.microsoft.com/vs/mac/) -->
* [<span data-ttu-id="41398-119">Visual Studio for Mac 版本 8.8 或更高版本（预览版）</span><span class="sxs-lookup"><span data-stu-id="41398-119">Visual Studio for Mac version 8.8 or later (in preview)</span></span>](/visualstudio/releasenotes/vs2019-mac-preview-relnotes)
* [!INCLUDE [.NET Core 5.0 SDK](~/includes/5.0-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="41398-120">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41398-120">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/5.0-SDK.md)]

---

::: moniker-end

::: moniker range="< aspnetcore-5.0"

# <a name="visual-studio"></a>[<span data-ttu-id="41398-121">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41398-121">Visual Studio</span></span>](#tab/visual-studio)

* <span data-ttu-id="41398-122">具有“ASP.NET 和 Web 开发”工作负载的 [Visual Studio 2019 16.6 或更高版本](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019)</span><span class="sxs-lookup"><span data-stu-id="41398-122">[Visual Studio 2019 16.6 or later](https://visualstudio.microsoft.com/downloads/?utm_medium=microsoft&utm_source=docs.microsoft.com&utm_campaign=inline+link&utm_content=download+vs2019) with the **ASP.NET and web development** workload</span></span>
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="visual-studio-code"></a>[<span data-ttu-id="41398-123">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41398-123">Visual Studio Code</span></span>](#tab/visual-studio-code)

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.1.md)]

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41398-124">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="41398-124">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

* [<span data-ttu-id="41398-125">Visual Studio for Mac 版本 8.6 或更高版本</span><span class="sxs-lookup"><span data-stu-id="41398-125">Visual Studio for Mac version 8.6 or later</span></span>](https://visualstudio.microsoft.com/vs/mac/)
* [!INCLUDE [.NET Core 3.1 SDK](~/includes/3.1-SDK.md)]

# <a name="net-core-cli"></a>[<span data-ttu-id="41398-126">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41398-126">.NET Core CLI</span></span>](#tab/netcore-cli/)

[!INCLUDE[](~/includes/3.1-SDK.md)]

---

::: moniker-end

## <a name="create-a-hosted-no-locblazor-webassembly-app-project"></a><span data-ttu-id="41398-127">创建托管 :::no-loc(Blazor WebAssembly)::: 应用项目</span><span class="sxs-lookup"><span data-stu-id="41398-127">Create a hosted :::no-loc(Blazor WebAssembly)::: app project</span></span>

<span data-ttu-id="41398-128">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="41398-128">Follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41398-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41398-129">Visual Studio</span></span>](#tab/visual-studio)

::: moniker range=">= aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="41398-130">需要 Visual Studio 16.8 或更高版本以及 .NET Core SDK 5.0.0 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="41398-130">Visual Studio 16.8 or later and .NET Core SDK 5.0.0 or later are required.</span></span>

::: moniker-end

::: moniker range="< aspnetcore-5.0"

> [!NOTE]
> <span data-ttu-id="41398-131">需要 Visual Studio 16.6 或更高版本以及 .NET Core SDK 3.1.300 或更高版本。</span><span class="sxs-lookup"><span data-stu-id="41398-131">Visual Studio 16.6 or later and .NET Core SDK 3.1.300 or later are required.</span></span>

::: moniker-end

1. <span data-ttu-id="41398-132">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="41398-132">Create a new project.</span></span>

1. <span data-ttu-id="41398-133">选择“:::no-loc(Blazor)::: 应用”，然后选择“下一步”。 </span><span class="sxs-lookup"><span data-stu-id="41398-133">Select **:::no-loc(Blazor)::: App** and select **Next**.</span></span>

1. <span data-ttu-id="41398-134">在“项目名称”字段中键入 `:::no-loc(Blazor)::::::no-loc(SignalR):::App`。</span><span class="sxs-lookup"><span data-stu-id="41398-134">Type `:::no-loc(Blazor)::::::no-loc(SignalR):::App` in the **Project name** field.</span></span> <span data-ttu-id="41398-135">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="41398-135">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="41398-136">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="41398-136">Select **Create**.</span></span>

1. <span data-ttu-id="41398-137">选择“:::no-loc(Blazor WebAssembly)::: 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="41398-137">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span>

1. <span data-ttu-id="41398-138">在“高级”下选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="41398-138">Under **Advanced** , select the **ASP.NET Core hosted** check box.</span></span>

1. <span data-ttu-id="41398-139">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="41398-139">Select **Create**.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="41398-140">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41398-140">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="41398-141">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41398-141">In a command shell, execute the following command:</span></span>

   ```dotnetcli
   dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
   ```

1. <span data-ttu-id="41398-142">在 Visual Studio Code 中打开应用的项目文件夹。</span><span class="sxs-lookup"><span data-stu-id="41398-142">In Visual Studio Code, open the app's project folder.</span></span>

1. <span data-ttu-id="41398-143">当显示添加资产以生成和调试应用的对话框时，选择“是”。</span><span class="sxs-lookup"><span data-stu-id="41398-143">When the dialog appears to add assets to build and debug the app, select **Yes**.</span></span> <span data-ttu-id="41398-144">Visual Studio Code 会自动将生成的 `launch.json` 和 `tasks.json` 文件添加到 `.vscode` 文件夹中。</span><span class="sxs-lookup"><span data-stu-id="41398-144">Visual Studio Code automatically adds the `.vscode` folder with generated `launch.json` and `tasks.json` files.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41398-145">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="41398-145">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="41398-146">安装最新版本的 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)，并执行以下步骤：</span><span class="sxs-lookup"><span data-stu-id="41398-146">Install the latest version of [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/) and perform the following steps:</span></span>

1. <span data-ttu-id="41398-147">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="41398-147">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="41398-148">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="41398-148">In the sidebar, select **Web and Console** > **App**.</span></span>

1. <span data-ttu-id="41398-149">选择“:::no-loc(Blazor WebAssembly)::: 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="41398-149">Choose the **:::no-loc(Blazor WebAssembly)::: App** template.</span></span> <span data-ttu-id="41398-150">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="41398-150">Select **Next**.</span></span>

1. <span data-ttu-id="41398-151">确认已将“身份验证”设置为“无身份验证”。</span><span class="sxs-lookup"><span data-stu-id="41398-151">Confirm that **Authentication** is set to **No Authentication**.</span></span> <span data-ttu-id="41398-152">选中“托管的 ASP.NET Core”复选框。</span><span class="sxs-lookup"><span data-stu-id="41398-152">Select the **ASP.NET Core Hosted** check box.</span></span> <span data-ttu-id="41398-153">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="41398-153">Select **Next**.</span></span>

1. <span data-ttu-id="41398-154">在“项目名称”字段中，将应用命名为 `:::no-loc(Blazor)::::::no-loc(SignalR):::App`。</span><span class="sxs-lookup"><span data-stu-id="41398-154">In the **Project Name** field, name the app `:::no-loc(Blazor)::::::no-loc(SignalR):::App`.</span></span> <span data-ttu-id="41398-155">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="41398-155">Select **Create**.</span></span>

   <span data-ttu-id="41398-156">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="41398-156">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="41398-157">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="41398-157">The user and keychain passwords are required to trust the certificate.</span></span>

1. <span data-ttu-id="41398-158">通过导航到项目文件夹并打开项目的解决方案文件 (`.sln`) 打开项目。</span><span class="sxs-lookup"><span data-stu-id="41398-158">Open the project by navigating to the project folder and opening the project's solution file (`.sln`).</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41398-159">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41398-159">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="41398-160">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41398-160">In a command shell, execute the following command:</span></span>

```dotnetcli
dotnet new blazorwasm --hosted --output :::no-loc(Blazor)::::::no-loc(SignalR):::App
```

---

## <a name="add-the-no-locsignalr-client-library"></a><span data-ttu-id="41398-161">添加 :::no-loc(SignalR)::: 客户端库</span><span class="sxs-lookup"><span data-stu-id="41398-161">Add the :::no-loc(SignalR)::: client library</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41398-162">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41398-162">Visual Studio</span></span>](#tab/visual-studio/)

1. <span data-ttu-id="41398-163">在“解决方案资源管理器”中，右键单击 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="41398-163">In **Solution Explorer** , right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="41398-164">在“管理 NuGet 包”对话框中，确认“包源”设置为“`nuget.org`” 。</span><span class="sxs-lookup"><span data-stu-id="41398-164">In the **Manage NuGet Packages** dialog, confirm that the **Package source** is set to `nuget.org`.</span></span>

1. <span data-ttu-id="41398-165">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`”。</span><span class="sxs-lookup"><span data-stu-id="41398-165">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="41398-166">在搜索结果中，选中 [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) 包，然后选择“安装”。</span><span class="sxs-lookup"><span data-stu-id="41398-166">In the search results, select the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Install**.</span></span>

1. <span data-ttu-id="41398-167">如果出现“预览更改”对话框，则选择“确定”。</span><span class="sxs-lookup"><span data-stu-id="41398-167">If the **Preview Changes** dialog appears, select **OK**.</span></span>

1. <span data-ttu-id="41398-168">如果出现“许可证接受”对话框，如果你同意许可条款，请选择“我接受”。</span><span class="sxs-lookup"><span data-stu-id="41398-168">If the **License Acceptance** dialog appears, select **I Accept** if you agree with the license terms.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="41398-169">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41398-169">Visual Studio Code</span></span>](#tab/visual-studio-code/)

<span data-ttu-id="41398-170">在“集成终端”（工具栏上的“视图” > “终端”）中，执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41398-170">In the **Integrated Terminal** ( **View** > **Terminal** from the toolbar), execute the following commands:</span></span>

```dotnetcli
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41398-171">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="41398-171">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="41398-172">在“解决方案资源管理器”中，右键单击 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` 项目，然后选择“管理 NuGet 包” 。</span><span class="sxs-lookup"><span data-stu-id="41398-172">In the **Solution** sidebar, right-click the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project and select **Manage NuGet Packages**.</span></span>

1. <span data-ttu-id="41398-173">在“管理 NuGet 包”对话框中，确认源下拉列表设置为“`nuget.org`”。</span><span class="sxs-lookup"><span data-stu-id="41398-173">In the **Manage NuGet Packages** dialog, confirm that the source drop-down is set to `nuget.org`.</span></span>

1. <span data-ttu-id="41398-174">选择“浏览”后，在搜索框中键入“`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`”。</span><span class="sxs-lookup"><span data-stu-id="41398-174">With **Browse** selected, type `Microsoft.AspNetCore.:::no-loc(SignalR):::.Client` in the search box.</span></span>

1. <span data-ttu-id="41398-175">在搜索结果中，选中 [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) 包旁边的复选框，然后选择“添加包”。</span><span class="sxs-lookup"><span data-stu-id="41398-175">In the search results, select the check box next to the [`Microsoft.AspNetCore.:::no-loc(SignalR):::.Client`](https://www.nuget.org/packages/Microsoft.AspNetCore.:::no-loc(SignalR):::.Client) package and select **Add Package**.</span></span>

1. <span data-ttu-id="41398-176">出现“许可证接受”对话框时，如果你同意许可条款，请选择“接受”。</span><span class="sxs-lookup"><span data-stu-id="41398-176">If the **License Acceptance** dialog appears, select **Accept** if you agree with the license terms.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41398-177">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41398-177">.NET Core CLI</span></span>](#tab/netcore-cli/)

<span data-ttu-id="41398-178">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41398-178">In a command shell, execute the following commands:</span></span>

```dotnetcli
cd :::no-loc(Blazor)::::::no-loc(SignalR):::App
dotnet add Client package Microsoft.AspNetCore.:::no-loc(SignalR):::.Client
```

---

## <a name="add-a-no-locsignalr-hub"></a><span data-ttu-id="41398-179">添加 :::no-loc(SignalR)::: 集线器</span><span class="sxs-lookup"><span data-stu-id="41398-179">Add a :::no-loc(SignalR)::: hub</span></span>

<span data-ttu-id="41398-180">在 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 项目中，创建 `Hubs`（复数）文件夹，并添加以下 `ChatHub` 类 (`Hubs/ChatHub.cs`)：</span><span class="sxs-lookup"><span data-stu-id="41398-180">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, create a `Hubs` (plural) folder and add the following `ChatHub` class (`Hubs/ChatHub.cs`):</span></span>

::: moniker range=">= aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Hubs/ChatHub.cs)]

::: moniker-end

## <a name="add-services-and-an-endpoint-for-the-no-locsignalr-hub"></a><span data-ttu-id="41398-181">为 :::no-loc(SignalR)::: 中心添加服务和终结点</span><span class="sxs-lookup"><span data-stu-id="41398-181">Add services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>

1. <span data-ttu-id="41398-182">在 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 项目中打开 `Startup.cs` 文件。</span><span class="sxs-lookup"><span data-stu-id="41398-182">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project, open the `Startup.cs` file.</span></span>

1. <span data-ttu-id="41398-183">将 `ChatHub` 类的命名空间添加到文件顶部：</span><span class="sxs-lookup"><span data-stu-id="41398-183">Add the namespace for the `ChatHub` class to the top of the file:</span></span>

   ```csharp
   using :::no-loc(Blazor)::::::no-loc(SignalR):::App.Server.Hubs;
   ```

1. <span data-ttu-id="41398-184">将 :::no-loc(SignalR)::: 和响应压缩中间件服务添加到 `Startup.ConfigureServices`：</span><span class="sxs-lookup"><span data-stu-id="41398-184">Add :::no-loc(SignalR)::: and Response Compression Middleware services to `Startup.ConfigureServices`:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_ConfigureServices&highlight=3,5-9)]

::: moniker-end

1. <span data-ttu-id="41398-185">在 `Startup.Configure`中：</span><span class="sxs-lookup"><span data-stu-id="41398-185">In `Startup.Configure`:</span></span>

   * <span data-ttu-id="41398-186">使用处理管道的配置顶部的“响应压缩中间件”。</span><span class="sxs-lookup"><span data-stu-id="41398-186">Use Response Compression Middleware at the top of the processing pipeline's configuration.</span></span>
   * <span data-ttu-id="41398-187">在控制器终结点和客户端回退之间，为中心添加一个终结点。</span><span class="sxs-lookup"><span data-stu-id="41398-187">Between the endpoints for controllers and the client-side fallback, add an endpoint for the hub.</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-csharp[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Server/Startup.cs?name=snippet_Configure&highlight=3,25)]

::: moniker-end

## <a name="add-no-locrazor-component-code-for-chat"></a><span data-ttu-id="41398-188">添加用于聊天的 :::no-loc(Razor)::: 组件代码</span><span class="sxs-lookup"><span data-stu-id="41398-188">Add :::no-loc(Razor)::: component code for chat</span></span>

1. <span data-ttu-id="41398-189">在 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` 项目中打开 `Pages/Index.razor` 文件。</span><span class="sxs-lookup"><span data-stu-id="41398-189">In the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Client` project, open the `Pages/Index.razor` file.</span></span>

1. <span data-ttu-id="41398-190">将标记替换为以下代码：</span><span class="sxs-lookup"><span data-stu-id="41398-190">Replace the markup with the following code:</span></span>

::: moniker range=">= aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/5.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   [!code-razor[](signalr-blazor-webassembly/samples/3.x/:::no-loc(Blazor)::::::no-loc(SignalR):::App/Client/Pages/Index.razor)]

::: moniker-end

## <a name="run-the-app"></a><span data-ttu-id="41398-191">运行应用</span><span class="sxs-lookup"><span data-stu-id="41398-191">Run the app</span></span>

1. <span data-ttu-id="41398-192">按照工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="41398-192">Follow the guidance for your tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="41398-193">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="41398-193">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="41398-194">在“解决方案资源管理器”中，选择 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="41398-194">In **Solution Explorer** , select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="41398-195">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="41398-195">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="41398-196">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="41398-196">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="41398-197">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="41398-197">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="41398-198">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="41398-198">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="41398-200">Quotes: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="41398-200">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="41398-201">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="41398-201">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="41398-202">当 VS Code 主动为服务器应用创建一个启动配置文件 (`.vscode/launch.json`) 时，`program` 条目如下所示，它指向应用的程序集 (`{APPLICATION NAME}.Server.dll`)：</span><span class="sxs-lookup"><span data-stu-id="41398-202">When VS Code offers to create a launch profile for the Server app (`.vscode/launch.json`), the `program` entry appears similar to the following to point to the app's assembly (`{APPLICATION NAME}.Server.dll`):</span></span>

::: moniker range=">= aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/net5.0/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

::: moniker range="< aspnetcore-5.0"

   ```json
   "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/{APPLICATION NAME}.Server.dll"
   ```

::: moniker-end

1. <span data-ttu-id="41398-203">按 <kbd>F5</kbd> 来运行应用并进行调试，或者按 <kbd>Ctrl</kbd>+<kbd>F5</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="41398-203">Press <kbd>F5</kbd> to run the app with debugging or <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="41398-204">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="41398-204">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="41398-205">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="41398-205">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="41398-206">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="41398-206">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="41398-208">Quotes: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="41398-208">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="41398-209">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="41398-209">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="41398-210">在“解决方案”边栏中，选择 `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` 项目。</span><span class="sxs-lookup"><span data-stu-id="41398-210">In the **Solution** sidebar, select the `:::no-loc(Blazor)::::::no-loc(SignalR):::App.Server` project.</span></span> <span data-ttu-id="41398-211">按 <kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用并进行调试，或者按 <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> 来运行应用但不调试。</span><span class="sxs-lookup"><span data-stu-id="41398-211">Press <kbd>⌘</kbd>+<kbd>↩</kbd> to run the app with debugging or <kbd>⌥</kbd>+<kbd>⌘</kbd>+<kbd>↩</kbd> to run the app without debugging.</span></span>

1. <span data-ttu-id="41398-212">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="41398-212">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="41398-213">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="41398-213">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="41398-214">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="41398-214">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="41398-216">Quotes: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="41398-216">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="41398-217">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="41398-217">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="41398-218">在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="41398-218">In a command shell, execute the following commands:</span></span>

   ```dotnetcli
   cd Server
   dotnet run
   ```

1. <span data-ttu-id="41398-219">从地址栏复制 URL，打开另一个浏览器实例或选项卡，并在地址栏中粘贴该 URL。</span><span class="sxs-lookup"><span data-stu-id="41398-219">Copy the URL from the address bar, open another browser instance or tab, and paste the URL in the address bar.</span></span>

1. <span data-ttu-id="41398-220">选择任一浏览器，输入名称和消息，然后选择按钮发送消息。</span><span class="sxs-lookup"><span data-stu-id="41398-220">Choose either browser, enter a name and message, and select the button to send the message.</span></span> <span data-ttu-id="41398-221">两个页面上立即显示名称和消息：</span><span class="sxs-lookup"><span data-stu-id="41398-221">The name and message are displayed on both pages instantly:</span></span>

   ![:::no-loc(SignalR)::: :::no-loc(Blazor WebAssembly)::: 示例应用在两个浏览器窗口中打开，显示交换的消息。](signalr-blazor-webassembly/_static/3.x/signalr-blazor-webassembly-finished.png)

   <span data-ttu-id="41398-223">Quotes: *Star Trek VI:The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span><span class="sxs-lookup"><span data-stu-id="41398-223">Quotes: *Star Trek VI: The Undiscovered Country* &copy;1991 [Paramount](https://www.paramountmovies.com/movies/star-trek-vi-the-undiscovered-country)</span></span>

---

## <a name="next-steps"></a><span data-ttu-id="41398-224">后续步骤</span><span class="sxs-lookup"><span data-stu-id="41398-224">Next steps</span></span>

<span data-ttu-id="41398-225">在本教程中，你将了解：</span><span class="sxs-lookup"><span data-stu-id="41398-225">In this tutorial, you learned how to:</span></span>

> [!div class="checklist"]
> * <span data-ttu-id="41398-226">创建 :::no-loc(Blazor WebAssembly)::: 托管应用项目</span><span class="sxs-lookup"><span data-stu-id="41398-226">Create a :::no-loc(Blazor WebAssembly)::: Hosted app project</span></span>
> * <span data-ttu-id="41398-227">添加 :::no-loc(SignalR)::: 客户端库</span><span class="sxs-lookup"><span data-stu-id="41398-227">Add the :::no-loc(SignalR)::: client library</span></span>
> * <span data-ttu-id="41398-228">添加 :::no-loc(SignalR)::: 集线器</span><span class="sxs-lookup"><span data-stu-id="41398-228">Add a :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="41398-229">添加 :::no-loc(SignalR)::: 服务和 :::no-loc(SignalR)::: 中心的终结点</span><span class="sxs-lookup"><span data-stu-id="41398-229">Add :::no-loc(SignalR)::: services and an endpoint for the :::no-loc(SignalR)::: hub</span></span>
> * <span data-ttu-id="41398-230">添加用于聊天的 :::no-loc(Razor)::: 组件代码</span><span class="sxs-lookup"><span data-stu-id="41398-230">Add :::no-loc(Razor)::: component code for chat</span></span>

<span data-ttu-id="41398-231">若要了解有关生成 :::no-loc(Blazor)::: 应用的详细信息，请参阅 :::no-loc(Blazor)::: 文档：</span><span class="sxs-lookup"><span data-stu-id="41398-231">To learn more about building :::no-loc(Blazor)::: apps, see the :::no-loc(Blazor)::: documentation:</span></span>

> [!div class="nextstepaction"]
> <span data-ttu-id="41398-232"><xref:blazor/index>
> [具有 :::no-loc(Identity)::: 服务器、WebSocket 和服务器发送事件的持有者令牌身份验证](xref:signalr/authn-and-authz#bearer-token-authentication)</span><span class="sxs-lookup"><span data-stu-id="41398-232"><xref:blazor/index>
[Bearer token authentication with :::no-loc(Identity)::: Server, WebSockets, and Server-Sent Events](xref:signalr/authn-and-authz#bearer-token-authentication)</span></span>

## <a name="additional-resources"></a><span data-ttu-id="41398-233">其他资源</span><span class="sxs-lookup"><span data-stu-id="41398-233">Additional resources</span></span>

* <xref:signalr/introduction>
* [<span data-ttu-id="41398-234">:::no-loc(SignalR)::: 用于身份验证的跨源协商</span><span class="sxs-lookup"><span data-stu-id="41398-234">:::no-loc(SignalR)::: cross-origin negotiation for authentication</span></span>](xref:blazor/fundamentals/additional-scenarios#signalr-cross-origin-negotiation-for-authentication)
