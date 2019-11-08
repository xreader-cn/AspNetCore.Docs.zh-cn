---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选工具构建 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/07/2019
uid: blazor/get-started
ms.openlocfilehash: b5043c7e4549800c1ab49bc37dd8f3568975d4aa
ms.sourcegitcommit: 67116718dc33a7a01696d41af38590fdbb58e014
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/07/2019
ms.locfileid: "73799225"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="66713-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="66713-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="66713-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="66713-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="66713-105">Blazor 入门：</span><span class="sxs-lookup"><span data-stu-id="66713-105">Get started with Blazor:</span></span>

::: moniker range=">= aspnetcore-3.1"

1. <span data-ttu-id="66713-106">安装[.Net Core 3.1 预览版 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="66713-106">Install the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="66713-107">通过在命令行界面中运行以下命令，安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板。</span><span class="sxs-lookup"><span data-stu-id="66713-107">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by running the following command in a command shell.</span></span> <span data-ttu-id="66713-108">[AspNetCore](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)包具有预览版本，而 Blazor WebAssembly 处于预览阶段。</span><span class="sxs-lookup"><span data-stu-id="66713-108">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="66713-109">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="66713-109">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="66713-110">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66713-110">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="66713-111">1 \。</span><span class="sxs-lookup"><span data-stu-id="66713-111">1\.</span></span> <span data-ttu-id="66713-112">安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="66713-112">Install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="66713-113">2。</span><span class="sxs-lookup"><span data-stu-id="66713-113">2\.</span></span> <span data-ttu-id="66713-114">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="66713-114">Create a new project.</span></span>

   <span data-ttu-id="66713-115">3。</span><span class="sxs-lookup"><span data-stu-id="66713-115">3\.</span></span> <span data-ttu-id="66713-116">选择**Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="66713-116">Select **Blazor App**.</span></span> <span data-ttu-id="66713-117">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="66713-117">Select **Next**.</span></span>

   <span data-ttu-id="66713-118">4 \。</span><span class="sxs-lookup"><span data-stu-id="66713-118">4\.</span></span> <span data-ttu-id="66713-119">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="66713-119">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="66713-120">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="66713-120">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="66713-121">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="66713-121">Select **Create**.</span></span>

   <span data-ttu-id="66713-122">5 \。</span><span class="sxs-lookup"><span data-stu-id="66713-122">5\.</span></span> <span data-ttu-id="66713-123">对于 "Blazor WebAssembly 体验"，请选择 " **Blazor WebAssembly 应用**" 模板。</span><span class="sxs-lookup"><span data-stu-id="66713-123">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="66713-124">对于 Blazor 服务器体验，请选择**Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="66713-124">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="66713-125">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="66713-125">Select **Create**.</span></span> <span data-ttu-id="66713-126">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="66713-126">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66713-127">6 \。</span><span class="sxs-lookup"><span data-stu-id="66713-127">6\.</span></span> <span data-ttu-id="66713-128">按 Ctrl+F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="66713-128">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="66713-129">如果安装了 Blazor Visual Studio extension for the ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="66713-129">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="66713-130">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="66713-130">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="66713-131">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66713-131">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="66713-132">1 \。</span><span class="sxs-lookup"><span data-stu-id="66713-132">1\.</span></span> <span data-ttu-id="66713-133">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="66713-133">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="66713-134">2。</span><span class="sxs-lookup"><span data-stu-id="66713-134">2\.</span></span> <span data-ttu-id="66713-135">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="66713-135">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="66713-136">3。</span><span class="sxs-lookup"><span data-stu-id="66713-136">3\.</span></span> <span data-ttu-id="66713-137">对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-137">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="66713-138">对于 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-138">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="66713-139">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="66713-139">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66713-140">4 \。</span><span class="sxs-lookup"><span data-stu-id="66713-140">4\.</span></span> <span data-ttu-id="66713-141">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="66713-141">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="66713-142">5 \。</span><span class="sxs-lookup"><span data-stu-id="66713-142">5\.</span></span> <span data-ttu-id="66713-143">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="66713-143">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="66713-144">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="66713-144">Select **Yes**.</span></span>

   <span data-ttu-id="66713-145">6 \。</span><span class="sxs-lookup"><span data-stu-id="66713-145">6\.</span></span> <span data-ttu-id="66713-146">如果使用的是 Blazor 服务器应用，请使用 Visual Studio Code 调试程序运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="66713-146">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="66713-147">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="66713-147">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="66713-148">7 \。</span><span class="sxs-lookup"><span data-stu-id="66713-148">7\.</span></span> <span data-ttu-id="66713-149">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="66713-149">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="66713-150">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="66713-150">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="66713-151">对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-151">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="66713-152">对于 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-152">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="66713-153">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="66713-153">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66713-154">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="66713-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. <span data-ttu-id="66713-155">安装最新的[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)版本。</span><span class="sxs-lookup"><span data-stu-id="66713-155">Install the latest [.NET Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0) release.</span></span>

1. <span data-ttu-id="66713-156">还可以通过安装[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) ，然后在命令行界面中运行以下命令，安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板：</span><span class="sxs-lookup"><span data-stu-id="66713-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template by installing the [.NET Core 3.1 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) and then running the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview2.19528.8
   ```

1. <span data-ttu-id="66713-157">按照所选工具的指导进行操作：</span><span class="sxs-lookup"><span data-stu-id="66713-157">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="66713-158">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="66713-158">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="66713-159">1 \。</span><span class="sxs-lookup"><span data-stu-id="66713-159">1\.</span></span> <span data-ttu-id="66713-160">安装最新的[Visual Studio](https://visualstudio.com/vs/) **和 ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="66713-160">Install the latest [Visual Studio](https://visualstudio.com/vs/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="66713-161">2。</span><span class="sxs-lookup"><span data-stu-id="66713-161">2\.</span></span> <span data-ttu-id="66713-162">可以选择安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含用于 Blazor WebAssembly 应用开发的**ASP.NET 和 web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="66713-162">Optionally install [Visual Studio 16.4 Preview 2 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload for Blazor WebAssembly app development.</span></span>

   <span data-ttu-id="66713-163">3。</span><span class="sxs-lookup"><span data-stu-id="66713-163">3\.</span></span> <span data-ttu-id="66713-164">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="66713-164">Create a new project.</span></span>

   <span data-ttu-id="66713-165">4 \。</span><span class="sxs-lookup"><span data-stu-id="66713-165">4\.</span></span> <span data-ttu-id="66713-166">选择**Blazor 应用**。</span><span class="sxs-lookup"><span data-stu-id="66713-166">Select **Blazor App**.</span></span> <span data-ttu-id="66713-167">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="66713-167">Select **Next**.</span></span>

   <span data-ttu-id="66713-168">5 \。</span><span class="sxs-lookup"><span data-stu-id="66713-168">5\.</span></span> <span data-ttu-id="66713-169">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="66713-169">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="66713-170">确认**位置**项正确或提供项目的位置。</span><span class="sxs-lookup"><span data-stu-id="66713-170">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="66713-171">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="66713-171">Select **Create**.</span></span>

   <span data-ttu-id="66713-172">6 \。</span><span class="sxs-lookup"><span data-stu-id="66713-172">6\.</span></span> <span data-ttu-id="66713-173">对于 "Blazor WebAssembly 体验"，请选择 " **Blazor WebAssembly 应用**" 模板。</span><span class="sxs-lookup"><span data-stu-id="66713-173">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="66713-174">对于 Blazor 服务器体验，请选择**Blazor 服务器应用程序**模板。</span><span class="sxs-lookup"><span data-stu-id="66713-174">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="66713-175">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="66713-175">Select **Create**.</span></span> <span data-ttu-id="66713-176">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="66713-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66713-177">7 \。</span><span class="sxs-lookup"><span data-stu-id="66713-177">7\.</span></span> <span data-ttu-id="66713-178">按 F5 运行应用。</span><span class="sxs-lookup"><span data-stu-id="66713-178">Press **F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="66713-179">如果安装了 Blazor Visual Studio extension for the ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本，则可以卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="66713-179">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="66713-180">现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。</span><span class="sxs-lookup"><span data-stu-id="66713-180">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-codetabvisual-studio-code"></a>[<span data-ttu-id="66713-181">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="66713-181">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="66713-182">1 \。</span><span class="sxs-lookup"><span data-stu-id="66713-182">1\.</span></span> <span data-ttu-id="66713-183">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="66713-183">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="66713-184">2。</span><span class="sxs-lookup"><span data-stu-id="66713-184">2\.</span></span> <span data-ttu-id="66713-185">安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。</span><span class="sxs-lookup"><span data-stu-id="66713-185">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="66713-186">3。</span><span class="sxs-lookup"><span data-stu-id="66713-186">3\.</span></span> <span data-ttu-id="66713-187">对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-187">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="66713-188">对于 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-188">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="66713-189">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="66713-189">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66713-190">4 \。</span><span class="sxs-lookup"><span data-stu-id="66713-190">4\.</span></span> <span data-ttu-id="66713-191">在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。</span><span class="sxs-lookup"><span data-stu-id="66713-191">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="66713-192">5 \。</span><span class="sxs-lookup"><span data-stu-id="66713-192">5\.</span></span> <span data-ttu-id="66713-193">对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="66713-193">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="66713-194">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="66713-194">Select **Yes**.</span></span>

   <span data-ttu-id="66713-195">6 \。</span><span class="sxs-lookup"><span data-stu-id="66713-195">6\.</span></span> <span data-ttu-id="66713-196">如果使用的是 Blazor 服务器应用，请使用 Visual Studio Code 调试程序运行该应用程序。</span><span class="sxs-lookup"><span data-stu-id="66713-196">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="66713-197">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="66713-197">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="66713-198">7 \。</span><span class="sxs-lookup"><span data-stu-id="66713-198">7\.</span></span> <span data-ttu-id="66713-199">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="66713-199">In a browser, navigate to `https://localhost:5001`.</span></span>

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="66713-200">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="66713-200">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="66713-201">对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-201">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="66713-202">对于 Blazor 服务器体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="66713-202">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="66713-203">有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="66713-203">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="66713-204">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="66713-204">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

::: moniker-end

<span data-ttu-id="66713-205">边栏中的选项卡上提供了多个页面：</span><span class="sxs-lookup"><span data-stu-id="66713-205">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="66713-206">Home</span><span class="sxs-lookup"><span data-stu-id="66713-206">Home</span></span>
* <span data-ttu-id="66713-207">计数器</span><span class="sxs-lookup"><span data-stu-id="66713-207">Counter</span></span>
* <span data-ttu-id="66713-208">提取数据</span><span class="sxs-lookup"><span data-stu-id="66713-208">Fetch data</span></span>

<span data-ttu-id="66713-209">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="66713-209">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="66713-210">在网页中递增计数器通常需要编写 JavaScript，但使用 Blazor 时，可以使用C#。</span><span class="sxs-lookup"><span data-stu-id="66713-210">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="66713-211">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="66713-211">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="66713-212">在浏览器中对 `/counter` 的请求（由顶部的 `@page` 指令指定）导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="66713-212">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="66713-213">组件呈现为呈现树的内存中表示形式，然后可以使用它以一种灵活而高效的方式更新 UI。</span><span class="sxs-lookup"><span data-stu-id="66713-213">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="66713-214">每次选择 "**单击我**" 按钮时：</span><span class="sxs-lookup"><span data-stu-id="66713-214">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="66713-215">引发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="66713-215">The `onclick` event is fired.</span></span>
* <span data-ttu-id="66713-216">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="66713-216">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="66713-217">`currentCount` 递增。</span><span class="sxs-lookup"><span data-stu-id="66713-217">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="66713-218">再次呈现该组件。</span><span class="sxs-lookup"><span data-stu-id="66713-218">The component is rendered again.</span></span>

<span data-ttu-id="66713-219">运行时将新内容与以前的内容进行比较，并仅将更改的内容应用到文档对象模型（DOM）。</span><span class="sxs-lookup"><span data-stu-id="66713-219">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="66713-220">使用 HTML 语法将组件添加到其他组件。</span><span class="sxs-lookup"><span data-stu-id="66713-220">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="66713-221">例如，通过向 `Index` 组件添加 `<Counter />` 元素，将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="66713-221">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="66713-222">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="66713-222">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="66713-223">运行应用。</span><span class="sxs-lookup"><span data-stu-id="66713-223">Run the app.</span></span> <span data-ttu-id="66713-224">主页具有由 `Counter` 组件提供的自己的计数器。</span><span class="sxs-lookup"><span data-stu-id="66713-224">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="66713-225">使用特性或[子内容](xref:blazor/components#child-content)指定组件参数，这些参数允许你设置子组件的属性。</span><span class="sxs-lookup"><span data-stu-id="66713-225">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="66713-226">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="66713-226">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="66713-227">使用 `[Parameter]` 属性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="66713-227">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="66713-228">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="66713-228">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="66713-229">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="66713-229">*Pages/Counter.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="66713-230">使用特性指定 `Index` 组件的 `<Counter>` 元素中的 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="66713-230">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="66713-231">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="66713-231">*Pages/Index.razor*:</span></span>

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="66713-232">运行应用。</span><span class="sxs-lookup"><span data-stu-id="66713-232">Run the app.</span></span> <span data-ttu-id="66713-233">每次选择 "**单击我**" 按钮时，`Index` 组件都有其自己的计数器，每次增加10个。</span><span class="sxs-lookup"><span data-stu-id="66713-233">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="66713-234">`/counter` 的 `Counter` 组件（*Counter*）继续递增1。</span><span class="sxs-lookup"><span data-stu-id="66713-234">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="66713-235">后续步骤</span><span class="sxs-lookup"><span data-stu-id="66713-235">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="66713-236">其他资源</span><span class="sxs-lookup"><span data-stu-id="66713-236">Additional resources</span></span>

* <xref:signalr/introduction>
