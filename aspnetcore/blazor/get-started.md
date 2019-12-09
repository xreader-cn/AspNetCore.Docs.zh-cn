---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 通过使用所选工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 12/09/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: e368ecaf931d392de7e52ec2d5a2dfd171c2c86f
ms.sourcegitcommit: 851b921080fe8d719f54871770ccf6f78052584e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2019
ms.locfileid: "74943755"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a><span data-ttu-id="302c6-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="302c6-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="302c6-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="302c6-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="302c6-105">Blazor入门：</span><span class="sxs-lookup"><span data-stu-id="302c6-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="302c6-106">安装[.Net Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="302c6-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="302c6-107">（可选）安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板：</span><span class="sxs-lookup"><span data-stu-id="302c6-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="302c6-108">安装[.Net Core 3.1 或更高版本（预览版） SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="302c6-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="302c6-109">在命令行界面中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="302c6-109">Run the following command in a command shell.</span></span> <span data-ttu-id="302c6-110">[AspNetCore.Blazor。模板](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)包具有预览版本，而 Blazor WebAssembly 处于预览阶段。</span><span class="sxs-lookup"><span data-stu-id="302c6-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="302c6-111">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="302c6-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="302c6-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="302c6-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="302c6-113">1 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-113">1\.</span></span> <span data-ttu-id="302c6-114">安装[Visual Studio 16.4 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="302c6-114">Install [Visual Studio 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="302c6-115">2。</span><span class="sxs-lookup"><span data-stu-id="302c6-115">2\.</span></span> <span data-ttu-id="302c6-116">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="302c6-116">Create a new project.</span></span>

   <span data-ttu-id="302c6-117">3。</span><span class="sxs-lookup"><span data-stu-id="302c6-117">3\.</span></span> <span data-ttu-id="302c6-118">选择 **Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="302c6-118">Select **Blazor App**.</span></span> <span data-ttu-id="302c6-119">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="302c6-119">Select **Next**.</span></span>

   <span data-ttu-id="302c6-120">4 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-120">4\.</span></span> <span data-ttu-id="302c6-121">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="302c6-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="302c6-122">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="302c6-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="302c6-123">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="302c6-123">Select **Create**.</span></span>

   <span data-ttu-id="302c6-124">5 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-124">5\.</span></span> <span data-ttu-id="302c6-125">Blazor WebAssembly 体验，请选择 **Blazor WebAssembly 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="302c6-126">要获得 Blazor 服务器体验，请选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="302c6-127">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="302c6-127">Select **Create**.</span></span> <span data-ttu-id="302c6-128">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="302c6-129">6 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-129">6\.</span></span> <span data-ttu-id="302c6-130">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="302c6-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="302c6-131">如果安装了 ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本的 Blazor Visual Studio 扩展，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="302c6-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="302c6-132">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="302c6-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="302c6-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="302c6-134">1 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-134">1\.</span></span> <span data-ttu-id="302c6-135">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="302c6-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="302c6-136">2。</span><span class="sxs-lookup"><span data-stu-id="302c6-136">2\.</span></span> <span data-ttu-id="302c6-137">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="302c6-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="302c6-138">3。</span><span class="sxs-lookup"><span data-stu-id="302c6-138">3\.</span></span> <span data-ttu-id="302c6-139">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="302c6-140">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="302c6-141">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="302c6-142">4 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-142">4\.</span></span> <span data-ttu-id="302c6-143">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="302c6-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="302c6-144">5 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-144">5\.</span></span> <span data-ttu-id="302c6-145">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="302c6-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="302c6-146">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="302c6-146">Select **Yes**.</span></span>

   <span data-ttu-id="302c6-147">6 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-147">6\.</span></span> <span data-ttu-id="302c6-148">如果使用 Blazor Server 应用程序，请使用 Visual Studio Code 调试器运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="302c6-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="302c6-149">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹中执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="302c6-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="302c6-150">7 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-150">7\.</span></span> <span data-ttu-id="302c6-151">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="302c6-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="302c6-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="302c6-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="302c6-153">1 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-153">1\.</span></span> <span data-ttu-id="302c6-154">安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="302c6-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="302c6-155">将[更新通道切换到 "预览](/visualstudio/mac/install-preview)"。</span><span class="sxs-lookup"><span data-stu-id="302c6-155">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="302c6-156">2。</span><span class="sxs-lookup"><span data-stu-id="302c6-156">2\.</span></span> <span data-ttu-id="302c6-157">选择 "**文件**" > "**新建解决方案**" 或创建一个**新项目**。</span><span class="sxs-lookup"><span data-stu-id="302c6-157">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="302c6-158">3。</span><span class="sxs-lookup"><span data-stu-id="302c6-158">3\.</span></span> <span data-ttu-id="302c6-159">在边栏中，选择 " **.Net Core** > **应用**"。</span><span class="sxs-lookup"><span data-stu-id="302c6-159">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="302c6-160">4 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-160">4\.</span></span> <span data-ttu-id="302c6-161">选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-161">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="302c6-162">此时 Visual Studio for Mac 中仅提供 Blazor 服务器模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-162">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="302c6-163">Blazor WebAssembly 体验，请按照 **.NET Core CLI**选项卡上的说明进行操作。选择 Blazor 服务器模板之后，选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="302c6-163">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="302c6-164">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-164">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="302c6-165">5 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-165">5\.</span></span> <span data-ttu-id="302c6-166">将**目标框架**设置为 **.net Core 3.1** ，然后选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="302c6-166">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="302c6-167">6 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-167">6\.</span></span> <span data-ttu-id="302c6-168">在 "**项目名称**" 字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="302c6-168">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="302c6-169">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="302c6-169">Select **Create**.</span></span>

   <span data-ttu-id="302c6-170">7 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-170">7\.</span></span> <span data-ttu-id="302c6-171">选择 "**运行**" > "运行**但不调试** *" 以在没有调试器的情况下*运行应用。</span><span class="sxs-lookup"><span data-stu-id="302c6-171">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="302c6-172">通过 "**启动调试**" 运行应用程序，以*通过调试器*运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="302c6-172">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="302c6-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="302c6-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="302c6-174">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="302c6-175">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="302c6-176">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="302c6-177">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="302c6-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="302c6-178">安装最新的[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)。</span><span class="sxs-lookup"><span data-stu-id="302c6-178">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0).</span></span>

1. <span data-ttu-id="302c6-179">（可选）安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板：</span><span class="sxs-lookup"><span data-stu-id="302c6-179">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="302c6-180">安装[.Net Core 3.1 或更高版本（预览版） SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="302c6-180">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="302c6-181">在命令行界面中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="302c6-181">Run the following command in a command shell.</span></span> <span data-ttu-id="302c6-182">[AspNetCore.Blazor。模板](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)包具有预览版本，而 Blazor WebAssembly 处于预览阶段。</span><span class="sxs-lookup"><span data-stu-id="302c6-182">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview4.19579.2
   ```

1. <span data-ttu-id="302c6-183">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="302c6-183">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="302c6-184">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="302c6-184">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="302c6-185">1 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-185">1\.</span></span> <span data-ttu-id="302c6-186">安装最新的[Visual Studio](https://visualstudio.com/vs/) **和 ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="302c6-186">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="302c6-187">2。</span><span class="sxs-lookup"><span data-stu-id="302c6-187">2\.</span></span> <span data-ttu-id="302c6-188">可以选择安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含用于 Blazor WebAssembly 应用开发的**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="302c6-188">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="302c6-189">3。</span><span class="sxs-lookup"><span data-stu-id="302c6-189">3\.</span></span> <span data-ttu-id="302c6-190">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="302c6-190">Create a new project.</span></span>

   <span data-ttu-id="302c6-191">4 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-191">4\.</span></span> <span data-ttu-id="302c6-192">选择 **Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="302c6-192">Select **Blazor App**.</span></span> <span data-ttu-id="302c6-193">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="302c6-193">Select **Next**.</span></span>

   <span data-ttu-id="302c6-194">5 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-194">5\.</span></span> <span data-ttu-id="302c6-195">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="302c6-195">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="302c6-196">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="302c6-196">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="302c6-197">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="302c6-197">Select **Create**.</span></span>

   <span data-ttu-id="302c6-198">6 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-198">6\.</span></span> <span data-ttu-id="302c6-199">Blazor WebAssembly 体验，请选择 **Blazor WebAssembly 应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-199">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="302c6-200">要获得 Blazor 服务器体验，请选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-200">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="302c6-201">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="302c6-201">Select **Create**.</span></span> <span data-ttu-id="302c6-202">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-202">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="302c6-203">7 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-203">7\.</span></span> <span data-ttu-id="302c6-204">按 F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="302c6-204">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="302c6-205">如果安装了 ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本的 Blazor Visual Studio 扩展，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="302c6-205">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="302c6-206">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-206">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="302c6-207">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="302c6-207">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="302c6-208">1 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-208">1\.</span></span> <span data-ttu-id="302c6-209">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="302c6-209">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="302c6-210">2。</span><span class="sxs-lookup"><span data-stu-id="302c6-210">2\.</span></span> <span data-ttu-id="302c6-211">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="302c6-211">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="302c6-212">3。</span><span class="sxs-lookup"><span data-stu-id="302c6-212">3\.</span></span> <span data-ttu-id="302c6-213">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-213">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="302c6-214">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-214">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="302c6-215">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-215">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="302c6-216">4 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-216">4\.</span></span> <span data-ttu-id="302c6-217">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="302c6-217">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="302c6-218">5 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-218">5\.</span></span> <span data-ttu-id="302c6-219">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="302c6-219">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="302c6-220">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="302c6-220">Select **Yes**.</span></span>

   <span data-ttu-id="302c6-221">6 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-221">6\.</span></span> <span data-ttu-id="302c6-222">如果使用 Blazor Server 应用程序，请使用 Visual Studio Code 调试器运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="302c6-222">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="302c6-223">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹中执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="302c6-223">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="302c6-224">7 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-224">7\.</span></span> <span data-ttu-id="302c6-225">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="302c6-225">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[<span data-ttu-id="302c6-226">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="302c6-226">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="302c6-227">1 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-227">1\.</span></span> <span data-ttu-id="302c6-228">安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="302c6-228">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span> <span data-ttu-id="302c6-229">将[更新通道切换到 "预览](/visualstudio/mac/install-preview)"。</span><span class="sxs-lookup"><span data-stu-id="302c6-229">Switch the [Update channel to Preview](/visualstudio/mac/install-preview).</span></span>

   <span data-ttu-id="302c6-230">2。</span><span class="sxs-lookup"><span data-stu-id="302c6-230">2\.</span></span> <span data-ttu-id="302c6-231">选择 "**文件**" > "**新建解决方案**" 或创建一个**新项目**。</span><span class="sxs-lookup"><span data-stu-id="302c6-231">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="302c6-232">3。</span><span class="sxs-lookup"><span data-stu-id="302c6-232">3\.</span></span> <span data-ttu-id="302c6-233">在边栏中，选择 " **.Net Core** > **应用**"。</span><span class="sxs-lookup"><span data-stu-id="302c6-233">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="302c6-234">4 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-234">4\.</span></span> <span data-ttu-id="302c6-235">选择 **Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-235">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="302c6-236">此时 Visual Studio for Mac 中仅提供 Blazor 服务器模板。</span><span class="sxs-lookup"><span data-stu-id="302c6-236">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="302c6-237">Blazor WebAssembly 体验，请按照 **.NET Core CLI**选项卡上的说明进行操作。选择 Blazor 服务器模板之后，选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="302c6-237">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="302c6-238">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-238">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="302c6-239">5 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-239">5\.</span></span> <span data-ttu-id="302c6-240">将**目标框架**设置为 **.net Core 3.0** ，然后选择 "**下一步**"。</span><span class="sxs-lookup"><span data-stu-id="302c6-240">Set the **Target Framework** to **.NET Core 3.0** and select **Next**.</span></span>

   <span data-ttu-id="302c6-241">6 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-241">6\.</span></span> <span data-ttu-id="302c6-242">在 "**项目名称**" 字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="302c6-242">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="302c6-243">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="302c6-243">Select **Create**.</span></span>

   <span data-ttu-id="302c6-244">7 \。</span><span class="sxs-lookup"><span data-stu-id="302c6-244">7\.</span></span> <span data-ttu-id="302c6-245">选择 "**运行**" > "运行**但不调试** *" 以在没有调试器的情况下*运行应用。</span><span class="sxs-lookup"><span data-stu-id="302c6-245">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="302c6-246">通过 "**启动调试**" 运行应用程序，以*通过调试器*运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="302c6-246">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="302c6-247">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="302c6-247">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="302c6-248">Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-248">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="302c6-249">若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="302c6-249">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="302c6-250">有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="302c6-250">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="302c6-251">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="302c6-251">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="302c6-252">边栏中的选项卡上提供了多个页面：</span><span class="sxs-lookup"><span data-stu-id="302c6-252">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="302c6-253">主页</span><span class="sxs-lookup"><span data-stu-id="302c6-253">Home</span></span>
* <span data-ttu-id="302c6-254">计数器</span><span class="sxs-lookup"><span data-stu-id="302c6-254">Counter</span></span>
* <span data-ttu-id="302c6-255">提取数据</span><span class="sxs-lookup"><span data-stu-id="302c6-255">Fetch data</span></span>

<span data-ttu-id="302c6-256">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="302c6-256">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="302c6-257">在网页中递增计数器通常需要编写 JavaScript，但 Blazor 可以使用C#。</span><span class="sxs-lookup"><span data-stu-id="302c6-257">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="302c6-258">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="302c6-258">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="302c6-259">浏览器中 `/counter` 的请求由顶部的 `@page` 指令指定，导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="302c6-259">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="302c6-260">组件呈现为呈现树的内存中表示形式，然后可以使用它以一种灵活而高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="302c6-260">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="302c6-261">每次选择 "**单击我**" 按钮时：</span><span class="sxs-lookup"><span data-stu-id="302c6-261">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="302c6-262">引发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="302c6-262">The `onclick` event is fired.</span></span>
* <span data-ttu-id="302c6-263">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="302c6-263">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="302c6-264">`currentCount` 递增。</span><span class="sxs-lookup"><span data-stu-id="302c6-264">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="302c6-265">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="302c6-265">The component is rendered again.</span></span>

<span data-ttu-id="302c6-266">运行时将新内容与以前的内容进行比较，并仅将更改的内容应用到文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="302c6-266">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="302c6-267">使用 HTML 语法将组件添加到其他组件。</span><span class="sxs-lookup"><span data-stu-id="302c6-267">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="302c6-268">例如，通过向 `Index` 组件添加 `<Counter />` 元素，将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="302c6-268">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="302c6-269">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="302c6-269">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="302c6-270">运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="302c6-270">Run the app.</span></span> <span data-ttu-id="302c6-271">主页有由 `Counter` 组件提供的自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="302c6-271">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="302c6-272">使用特性或[子内容](xref:blazor/components#child-content)指定组件参数，这些参数允许你设置子组件的属性。</span><span class="sxs-lookup"><span data-stu-id="302c6-272">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="302c6-273">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="302c6-273">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="302c6-274">使用 `[Parameter]` 属性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="302c6-274">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="302c6-275">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="302c6-275">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="302c6-276">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="302c6-276">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="302c6-277">使用特性指定 `Index` 组件的 `<Counter>` 元素中的 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="302c6-277">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="302c6-278">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="302c6-278">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="302c6-279">运行应用程序。</span><span class="sxs-lookup"><span data-stu-id="302c6-279">Run the app.</span></span> <span data-ttu-id="302c6-280">每次选择 "**单击我**" 按钮时，`Index` 组件都有其自己的计数器，每次增加10个。</span><span class="sxs-lookup"><span data-stu-id="302c6-280">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="302c6-281">`/counter` 的 `Counter` 组件（*Counter*）继续递增1。</span><span class="sxs-lookup"><span data-stu-id="302c6-281">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="302c6-282">后续步骤</span><span class="sxs-lookup"><span data-stu-id="302c6-282">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="302c6-283">其他资源</span><span class="sxs-lookup"><span data-stu-id="302c6-283">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
