---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 通过使用所选工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 7d495bddde3c01c743db9757204a5cf59d8b160b
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550318"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="307a8-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="307a8-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="307a8-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="307a8-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="307a8-105">Blazor入门：</span><span class="sxs-lookup"><span data-stu-id="307a8-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="307a8-106">安装[.Net Core 3.1 预览版 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="307a8-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="307a8-107">通过在命令行界面中运行以下命令来安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="307a8-108">[AspNetCore.Blazor。模板](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)包具有预览版本，而 Blazor WebAssembly 处于预览阶段。</span><span class="sxs-lookup"><span data-stu-id="307a8-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. <span data-ttu-id="307a8-109">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="307a8-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="307a8-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="307a8-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="307a8-111">1 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-111">1\.</span></span> <span data-ttu-id="307a8-112">安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="307a8-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="307a8-113">2。</span><span class="sxs-lookup"><span data-stu-id="307a8-113">2\.</span></span> <span data-ttu-id="307a8-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="307a8-114">Create a new project.</span></span>

   <span data-ttu-id="307a8-115">3。</span><span class="sxs-lookup"><span data-stu-id="307a8-115">3\.</span></span> <span data-ttu-id="307a8-116">选择 **Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="307a8-116">Select **Blazor App**.</span></span> <span data-ttu-id="307a8-117">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="307a8-117">Select **Next**.</span></span>

   <span data-ttu-id="307a8-118">4 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-118">4\.</span></span> <span data-ttu-id="307a8-119">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="307a8-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="307a8-120">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="307a8-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="307a8-121">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="307a8-121">Select **Create**.</span></span>

   <span data-ttu-id="307a8-122">5 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-122">5\.</span></span> <span data-ttu-id="307a8-123">Blazor WebAssembly 体验，请选择 **Blazor WebAssembly 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="307a8-124">要获得 Blazor 服务器体验，请选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="307a8-125">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="307a8-125">Select **Create**.</span></span> <span data-ttu-id="307a8-126">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="307a8-127">6 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-127">6\.</span></span> <span data-ttu-id="307a8-128">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="307a8-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="307a8-129">如果安装了 ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本的 Blazor Visual Studio 扩展，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="307a8-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="307a8-130">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="307a8-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="307a8-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="307a8-132">1 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-132">1\.</span></span> <span data-ttu-id="307a8-133">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="307a8-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="307a8-134">2。</span><span class="sxs-lookup"><span data-stu-id="307a8-134">2\.</span></span> <span data-ttu-id="307a8-135">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="307a8-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="307a8-136">3。</span><span class="sxs-lookup"><span data-stu-id="307a8-136">3\.</span></span> <span data-ttu-id="307a8-137">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="307a8-138">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="307a8-139">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="307a8-140">4 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-140">4\.</span></span> <span data-ttu-id="307a8-141">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="307a8-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="307a8-142">5 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-142">5\.</span></span> <span data-ttu-id="307a8-143">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="307a8-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="307a8-144">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="307a8-144">Select **Yes**.</span></span>

   <span data-ttu-id="307a8-145">6 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-145">6\.</span></span> <span data-ttu-id="307a8-146">如果使用 Blazor Server 应用程序，请使用 Visual Studio Code 调试器运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="307a8-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="307a8-147">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹中执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="307a8-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="307a8-148">7 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-148">7\.</span></span> <span data-ttu-id="307a8-149">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="307a8-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="307a8-150">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="307a8-150">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="307a8-151">1 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-151">1\.</span></span> <span data-ttu-id="307a8-152">安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="307a8-152">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="307a8-153">将[更新通道切换到 "预览](/visualstudio/mac/install-preview)"。</span><span class="sxs-lookup"><span data-stu-id="307a8-153">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="307a8-154">2。</span><span class="sxs-lookup"><span data-stu-id="307a8-154">2\.</span></span> <span data-ttu-id="307a8-155">选择 "**文件**" > "**新建解决方案**" 或创建一个**新项目**。</span><span class="sxs-lookup"><span data-stu-id="307a8-155">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="307a8-156">3。</span><span class="sxs-lookup"><span data-stu-id="307a8-156">3\.</span></span> <span data-ttu-id="307a8-157">在边栏中，选择 " **.Net Core** > **应用**"。</span><span class="sxs-lookup"><span data-stu-id="307a8-157">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="307a8-158">4 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-158">4\.</span></span> <span data-ttu-id="307a8-159">选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-159">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="307a8-160">此时 Visual Studio for Mac 中仅提供 Blazor 服务器模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-160">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="307a8-161">Blazor WebAssembly 体验，请按照 **.NET Core CLI**选项卡上的说明进行操作。选择 Blazor 服务器模板之后，选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="307a8-161">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="307a8-162">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-162">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="307a8-163">5 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-163">5\.</span></span> <span data-ttu-id="307a8-164">如果安装了 3.1 Preview SDK，则**目标框架**默认为 " **.net core 3.0** " （或 " **.net core 3.1** "）。</span><span class="sxs-lookup"><span data-stu-id="307a8-164">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="307a8-165">选择框架，然后选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="307a8-165">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="307a8-166">6 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-166">6\.</span></span> <span data-ttu-id="307a8-167">在 "**项目名称**" 字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="307a8-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="307a8-168">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="307a8-168">Select **Create**.</span></span>

   <span data-ttu-id="307a8-169">7 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-169">7\.</span></span> <span data-ttu-id="307a8-170">选择 "**运行**" > "运行**但不调试** *" 以在没有调试器的情况下*运行应用。</span><span class="sxs-lookup"><span data-stu-id="307a8-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="307a8-171">通过 "**启动调试**" 运行应用程序，以*通过调试器*运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="307a8-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="307a8-172">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="307a8-172">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="307a8-173">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-173">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="307a8-174">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-174">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="307a8-175">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-175">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="307a8-176">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="307a8-176">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="307a8-177">安装最新的[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)版本。</span><span class="sxs-lookup"><span data-stu-id="307a8-177">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="307a8-178">还可以通过安装[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) ，然后在命令行界面中运行以下命令来安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板：</span><span class="sxs-lookup"><span data-stu-id="307a8-178">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. <span data-ttu-id="307a8-179">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="307a8-179">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="307a8-180">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="307a8-180">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="307a8-181">1 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-181">1\.</span></span> <span data-ttu-id="307a8-182">安装最新的[Visual Studio](https://visualstudio.com/vs/) **和 ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="307a8-182">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="307a8-183">2。</span><span class="sxs-lookup"><span data-stu-id="307a8-183">2\.</span></span> <span data-ttu-id="307a8-184">可以选择安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含用于 Blazor WebAssembly 应用开发的**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="307a8-184">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="307a8-185">3。</span><span class="sxs-lookup"><span data-stu-id="307a8-185">3\.</span></span> <span data-ttu-id="307a8-186">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="307a8-186">Create a new project.</span></span>

   <span data-ttu-id="307a8-187">4 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-187">4\.</span></span> <span data-ttu-id="307a8-188">选择 **Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="307a8-188">Select **Blazor App**.</span></span> <span data-ttu-id="307a8-189">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="307a8-189">Select **Next**.</span></span>

   <span data-ttu-id="307a8-190">5 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-190">5\.</span></span> <span data-ttu-id="307a8-191">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="307a8-191">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="307a8-192">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="307a8-192">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="307a8-193">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="307a8-193">Select **Create**.</span></span>

   <span data-ttu-id="307a8-194">6 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-194">6\.</span></span> <span data-ttu-id="307a8-195">Blazor WebAssembly 体验，请选择 **Blazor WebAssembly 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-195">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="307a8-196">要获得 Blazor 服务器体验，请选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-196">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="307a8-197">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="307a8-197">Select **Create**.</span></span> <span data-ttu-id="307a8-198">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-198">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="307a8-199">7 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-199">7\.</span></span> <span data-ttu-id="307a8-200">按 F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="307a8-200">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="307a8-201">如果安装了 ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本的 Blazor Visual Studio 扩展，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="307a8-201">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="307a8-202">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-202">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="307a8-203">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="307a8-203">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="307a8-204">1 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-204">1\.</span></span> <span data-ttu-id="307a8-205">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="307a8-205">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="307a8-206">2。</span><span class="sxs-lookup"><span data-stu-id="307a8-206">2\.</span></span> <span data-ttu-id="307a8-207">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="307a8-207">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="307a8-208">3。</span><span class="sxs-lookup"><span data-stu-id="307a8-208">3\.</span></span> <span data-ttu-id="307a8-209">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-209">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="307a8-210">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-210">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="307a8-211">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-211">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="307a8-212">4 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-212">4\.</span></span> <span data-ttu-id="307a8-213">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="307a8-213">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="307a8-214">5 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-214">5\.</span></span> <span data-ttu-id="307a8-215">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="307a8-215">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="307a8-216">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="307a8-216">Select **Yes**.</span></span>

   <span data-ttu-id="307a8-217">6 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-217">6\.</span></span> <span data-ttu-id="307a8-218">如果使用 Blazor Server 应用程序，请使用 Visual Studio Code 调试器运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="307a8-218">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="307a8-219">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹中执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="307a8-219">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="307a8-220">7 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-220">7\.</span></span> <span data-ttu-id="307a8-221">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="307a8-221">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="307a8-222">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="307a8-222">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="307a8-223">1 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-223">1\.</span></span> <span data-ttu-id="307a8-224">安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="307a8-224">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="307a8-225">将[更新通道切换到 "预览](/visualstudio/mac/install-preview)"。</span><span class="sxs-lookup"><span data-stu-id="307a8-225">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="307a8-226">2。</span><span class="sxs-lookup"><span data-stu-id="307a8-226">2\.</span></span> <span data-ttu-id="307a8-227">选择 "**文件**" > "**新建解决方案**" 或创建一个**新项目**。</span><span class="sxs-lookup"><span data-stu-id="307a8-227">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="307a8-228">3。</span><span class="sxs-lookup"><span data-stu-id="307a8-228">3\.</span></span> <span data-ttu-id="307a8-229">在边栏中，选择 " **.Net Core** > **应用**"。</span><span class="sxs-lookup"><span data-stu-id="307a8-229">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="307a8-230">4 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-230">4\.</span></span> <span data-ttu-id="307a8-231">选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-231">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="307a8-232">此时 Visual Studio for Mac 中仅提供 Blazor 服务器模板。</span><span class="sxs-lookup"><span data-stu-id="307a8-232">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="307a8-233">Blazor WebAssembly 体验，请按照 **.NET Core CLI**选项卡上的说明进行操作。选择 Blazor 服务器模板之后，选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="307a8-233">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="307a8-234">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-234">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="307a8-235">5 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-235">5\.</span></span> <span data-ttu-id="307a8-236">如果安装了 3.1 Preview SDK，则**目标框架**默认为 " **.net core 3.0** " （或 " **.net core 3.1** "）。</span><span class="sxs-lookup"><span data-stu-id="307a8-236">The **Target Framework** defaults to **.NET Core 3.0** (or **.NET Core 3.1** if the 3.1 Preview SDK is installed).</span></span> <span data-ttu-id="307a8-237">选择框架，然后选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="307a8-237">Select the framework and select **Next**.</span></span>

   <span data-ttu-id="307a8-238">6 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-238">6\.</span></span> <span data-ttu-id="307a8-239">在 "**项目名称**" 字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="307a8-239">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="307a8-240">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="307a8-240">Select **Create**.</span></span>

   <span data-ttu-id="307a8-241">7 \。</span><span class="sxs-lookup"><span data-stu-id="307a8-241">7\.</span></span> <span data-ttu-id="307a8-242">选择 "**运行**" > "运行**但不调试** *" 以在没有调试器的情况下*运行应用。</span><span class="sxs-lookup"><span data-stu-id="307a8-242">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="307a8-243">通过 "**启动调试**" 运行应用程序，以*通过调试器*运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="307a8-243">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="307a8-244">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="307a8-244">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="307a8-245">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-245">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="307a8-246">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="307a8-246">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="307a8-247">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="307a8-247">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="307a8-248">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="307a8-248">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="307a8-249">边栏中的选项卡上提供了多个页面：</span><span class="sxs-lookup"><span data-stu-id="307a8-249">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="307a8-250">主页</span><span class="sxs-lookup"><span data-stu-id="307a8-250">Home</span></span>
* <span data-ttu-id="307a8-251">计数器</span><span class="sxs-lookup"><span data-stu-id="307a8-251">Counter</span></span>
* <span data-ttu-id="307a8-252">提取数据</span><span class="sxs-lookup"><span data-stu-id="307a8-252">Fetch data</span></span>

<span data-ttu-id="307a8-253">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="307a8-253">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="307a8-254">在网页中递增计数器通常需要编写 JavaScript，但 Blazor 可以使用C#。</span><span class="sxs-lookup"><span data-stu-id="307a8-254">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="307a8-255">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="307a8-255">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="307a8-256">浏览器中 `/counter` 的请求由顶部的 `@page` 指令指定，导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="307a8-256">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="307a8-257">组件呈现为呈现树的内存中表示形式，然后可以使用它以一种灵活而高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="307a8-257">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="307a8-258">每次选择 "**单击我**" 按钮时：</span><span class="sxs-lookup"><span data-stu-id="307a8-258">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="307a8-259">引发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="307a8-259">The `onclick` event is fired.</span></span>
* <span data-ttu-id="307a8-260">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="307a8-260">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="307a8-261">`currentCount` 递增。</span><span class="sxs-lookup"><span data-stu-id="307a8-261">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="307a8-262">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="307a8-262">The component is rendered again.</span></span>

<span data-ttu-id="307a8-263">运行时将新内容与以前的内容进行比较，并仅将更改的内容应用到文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="307a8-263">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="307a8-264">使用 HTML 语法将组件添加到其他组件。</span><span class="sxs-lookup"><span data-stu-id="307a8-264">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="307a8-265">例如，通过向 `Index` 组件添加 `<Counter />` 元素，将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="307a8-265">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="307a8-266">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="307a8-266">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="307a8-267">运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="307a8-267">Run the app.</span></span> <span data-ttu-id="307a8-268">主页有由 `Counter` 组件提供的自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="307a8-268">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="307a8-269">使用特性或[子内容](xref:blazor/components#child-content)指定组件参数，这些参数允许你设置子组件的属性。</span><span class="sxs-lookup"><span data-stu-id="307a8-269">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="307a8-270">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="307a8-270">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="307a8-271">使用 `[Parameter]` 属性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="307a8-271">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="307a8-272">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="307a8-272">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="307a8-273">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="307a8-273">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="307a8-274">使用特性指定 `Index` 组件的 `<Counter>` 元素中的 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="307a8-274">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="307a8-275">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="307a8-275">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="307a8-276">运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="307a8-276">Run the app.</span></span> <span data-ttu-id="307a8-277">每次选择 "**单击我**" 按钮时，`Index` 组件都有其自己的计数器，每次增加10个。</span><span class="sxs-lookup"><span data-stu-id="307a8-277">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="307a8-278">`/counter` 的 `Counter` 组件（*Counter*）继续递增1。</span><span class="sxs-lookup"><span data-stu-id="307a8-278">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="307a8-279">后续步骤</span><span class="sxs-lookup"><span data-stu-id="307a8-279">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="307a8-280">其他资源</span><span class="sxs-lookup"><span data-stu-id="307a8-280">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
