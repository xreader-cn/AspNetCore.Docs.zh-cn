---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选的工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/10/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 89c7529d2b8ec97db731f7c7268e19937c398115
ms.sourcegitcommit: 98bcf5fe210931e3eb70f82fd675d8679b33f5d6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/11/2020
ms.locfileid: "79083236"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="51fc4-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="51fc4-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="51fc4-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="51fc4-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="51fc4-105">Blazor 入门：</span><span class="sxs-lookup"><span data-stu-id="51fc4-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="51fc4-106">安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="51fc4-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="51fc4-107">（可选）安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 模板：</span><span class="sxs-lookup"><span data-stu-id="51fc4-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="51fc4-108">安装 [.NET Core 3.1.102 或更高版本（预览版）SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="51fc4-108">Install the [.NET Core 3.1.102 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="51fc4-109">在命令行界面中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="51fc4-109">Run the following command in a command shell.</span></span> <span data-ttu-id="51fc4-110">当 Blazor WebAssembly 处于预览状态时，[ Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) 包具有预览版本。</span><span class="sxs-lookup"><span data-stu-id="51fc4-110">The [Microsoft.AspNetCore.Components.WebAssembly.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Components.WebAssembly.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview2.20160.5
   ```

   > [!NOTE]
   > <span data-ttu-id="51fc4-111">使用 3.2 预览版 2 Blazor WebAssembly 模板时需要 .NET Core SDK 版本 3.1.102 或更高版本  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-111">.NET Core SDK version 3.1.102 or later is **required** to use the 3.2 Preview 2 Blazor WebAssembly template.</span></span> <span data-ttu-id="51fc4-112">通过在命令行界面中运行 `dotnet --version` 来确认所安装的 .NET Core SDK 版本。</span><span class="sxs-lookup"><span data-stu-id="51fc4-112">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="51fc4-113">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="51fc4-113">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="51fc4-114">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="51fc4-114">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="51fc4-115">1\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-115">1\.</span></span> <span data-ttu-id="51fc4-116">安装 [Visual Studio 2019 版本 16.4 或更高版本](https://visualstudio.microsoft.com/vs/preview/)以及 **ASP.NET 和 Web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="51fc4-116">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="51fc4-117">2\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-117">2\.</span></span> <span data-ttu-id="51fc4-118">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="51fc4-118">Create a new project.</span></span>

   <span data-ttu-id="51fc4-119">3\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-119">3\.</span></span> <span data-ttu-id="51fc4-120">选择“Blazor 应用”  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-120">Select **Blazor App**.</span></span> <span data-ttu-id="51fc4-121">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-121">Select **Next**.</span></span>

   <span data-ttu-id="51fc4-122">4\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-122">4\.</span></span> <span data-ttu-id="51fc4-123">在“项目名称”字段提供项目名称，或接受默认项目名称  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-123">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="51fc4-124">确认“位置”  条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="51fc4-124">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="51fc4-125">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-125">Select **Create**.</span></span>

   <span data-ttu-id="51fc4-126">5\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-126">5\.</span></span> <span data-ttu-id="51fc4-127">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-127">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="51fc4-128">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-128">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="51fc4-129">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-129">Select **Create**.</span></span> <span data-ttu-id="51fc4-130">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="51fc4-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span> <span data-ttu-id="51fc4-131">如果 Blazor WebAssembly 模板不存在，请返回上一步并重新安装模板。</span><span class="sxs-lookup"><span data-stu-id="51fc4-131">If the Blazor WebAssembly template isn't present, return to the previous step and reinstall the template.</span></span>

   <span data-ttu-id="51fc4-132">6\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-132">6\.</span></span> <span data-ttu-id="51fc4-133">按 Ctrl+F5 运行应用   。</span><span class="sxs-lookup"><span data-stu-id="51fc4-133">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="51fc4-134">如果为 ASP.NET Core Blazor 之前的预览版本（预览版 6 或更早版本）安装了 Blazor Visual Studio 扩展，则可卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="51fc4-134">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="51fc4-135">现在，在命令行界面中安装 Blazor 模板即可在 Visual Studio 中显示这些模板。</span><span class="sxs-lookup"><span data-stu-id="51fc4-135">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="51fc4-136">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="51fc4-136">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="51fc4-137">1\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-137">1\.</span></span> <span data-ttu-id="51fc4-138">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="51fc4-138">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="51fc4-139">2\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-139">2\.</span></span> <span data-ttu-id="51fc4-140">安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="51fc4-140">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp).</span></span>

   <span data-ttu-id="51fc4-141">3\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-141">3\.</span></span> <span data-ttu-id="51fc4-142">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="51fc4-142">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="51fc4-143">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="51fc4-143">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="51fc4-144">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="51fc4-144">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="51fc4-145">4\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-145">4\.</span></span> <span data-ttu-id="51fc4-146">在 Visual Studio Code 中打开 *WebApplication1* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="51fc4-146">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="51fc4-147">5\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-147">5\.</span></span> <span data-ttu-id="51fc4-148">对于 Blazor Server 项目，IDE 要求添加资产用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="51fc4-148">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="51fc4-149">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="51fc4-149">Select **Yes**.</span></span>

   <span data-ttu-id="51fc4-150">6\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-150">6\.</span></span> <span data-ttu-id="51fc4-151">如果使用 Blazor Server 应用，请使用 Visual Studio Code 调试程序运行该应用。</span><span class="sxs-lookup"><span data-stu-id="51fc4-151">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="51fc4-152">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="51fc4-152">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="51fc4-153">7\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-153">7\.</span></span> <span data-ttu-id="51fc4-154">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="51fc4-154">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="51fc4-155">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="51fc4-155">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="51fc4-156">1\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-156">1\.</span></span> <span data-ttu-id="51fc4-157">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="51fc4-157">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="51fc4-158">2\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-158">2\.</span></span> <span data-ttu-id="51fc4-159">选择“文件” > “新建解决方案”或创建“新项目”    。</span><span class="sxs-lookup"><span data-stu-id="51fc4-159">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="51fc4-160">3\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-160">3\.</span></span> <span data-ttu-id="51fc4-161">在边栏中选择“.NET Core” > “应用”   。</span><span class="sxs-lookup"><span data-stu-id="51fc4-161">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="51fc4-162">4\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-162">4\.</span></span> <span data-ttu-id="51fc4-163">选择“Blazor Server 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-163">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="51fc4-164">目前，Visual Studio for Mac 中仅提供 Blazor Server 模板。</span><span class="sxs-lookup"><span data-stu-id="51fc4-164">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="51fc4-165">若要获得 Blazor WebAssembly 体验，请按照“.NET Core CLI”选项卡中的说明进行操作  。选择 Blazor Server 模板后，选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-165">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="51fc4-166">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="51fc4-166">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="51fc4-167">5\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-167">5\.</span></span> <span data-ttu-id="51fc4-168">将“目标框架”设置为“.NET Core 3.1”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="51fc4-168">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="51fc4-169">6\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-169">6\.</span></span> <span data-ttu-id="51fc4-170">在“项目名称”字段中，将应用命名为 `WebApplication1`  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-170">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="51fc4-171">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-171">Select **Create**.</span></span>

   <span data-ttu-id="51fc4-172">7\.</span><span class="sxs-lookup"><span data-stu-id="51fc4-172">7\.</span></span> <span data-ttu-id="51fc4-173">选择“运行” > “运行而不调试”以*不使用调试程序*运行应用   。</span><span class="sxs-lookup"><span data-stu-id="51fc4-173">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="51fc4-174">使用“开始调试”运行应用，以*使用调试程序*运行应用  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-174">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="51fc4-175">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="51fc4-175">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="51fc4-176">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="51fc4-176">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="51fc4-177">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="51fc4-177">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="51fc4-178">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="51fc4-178">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="51fc4-179">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="51fc4-179">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="51fc4-180">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="51fc4-180">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="51fc4-181">侧栏中的选项卡提供多个页面：</span><span class="sxs-lookup"><span data-stu-id="51fc4-181">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="51fc4-182">Home</span><span class="sxs-lookup"><span data-stu-id="51fc4-182">Home</span></span>
* <span data-ttu-id="51fc4-183">计数器</span><span class="sxs-lookup"><span data-stu-id="51fc4-183">Counter</span></span>
* <span data-ttu-id="51fc4-184">提取数据</span><span class="sxs-lookup"><span data-stu-id="51fc4-184">Fetch data</span></span>

<span data-ttu-id="51fc4-185">在“计数器”页上，选择“单击我”  按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="51fc4-185">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="51fc4-186">递增网页的计数器值通常需要编写 JavaScript，但借助 Blazor，可使用 C#。</span><span class="sxs-lookup"><span data-stu-id="51fc4-186">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="51fc4-187">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="51fc4-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="51fc4-188">浏览器中针对 `/counter` 的请求（由顶部的 `@page` 指令指定）会导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="51fc4-188">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="51fc4-189">组件呈现为呈现树的内存中表现形式，之后可使用它灵活高效地更新 UI。</span><span class="sxs-lookup"><span data-stu-id="51fc4-189">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="51fc4-190">每次选择“单击我”按钮时会出现以下情况  ：</span><span class="sxs-lookup"><span data-stu-id="51fc4-190">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="51fc4-191">触发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="51fc4-191">The `onclick` event is fired.</span></span>
* <span data-ttu-id="51fc4-192">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="51fc4-192">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="51fc4-193">递增 `currentCount` 的值。</span><span class="sxs-lookup"><span data-stu-id="51fc4-193">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="51fc4-194">再次呈现组件。</span><span class="sxs-lookup"><span data-stu-id="51fc4-194">The component is rendered again.</span></span>

<span data-ttu-id="51fc4-195">运行时将新内容与之前内容进行比较，且仅将已更改内容应用于文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="51fc4-195">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="51fc4-196">使用 HTML 语法将组件添加到另一个组件。</span><span class="sxs-lookup"><span data-stu-id="51fc4-196">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="51fc4-197">例如，通过将 `<Counter />` 元素添加到 `Index` 组件，可将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="51fc4-197">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="51fc4-198">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="51fc4-198">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="51fc4-199">运行应用。</span><span class="sxs-lookup"><span data-stu-id="51fc4-199">Run the app.</span></span> <span data-ttu-id="51fc4-200">主页拥有其自己的计数器，该计数器由 `Counter` 组件提供。</span><span class="sxs-lookup"><span data-stu-id="51fc4-200">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="51fc4-201">组件参数使用特性或[子内容](xref:blazor/components#child-content)指定，允许在子组件上设置属性。</span><span class="sxs-lookup"><span data-stu-id="51fc4-201">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="51fc4-202">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="51fc4-202">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="51fc4-203">使用 `[Parameter]` 特性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="51fc4-203">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="51fc4-204">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="51fc4-204">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="51fc4-205">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="51fc4-205">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="51fc4-206">使用特性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="51fc4-206">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="51fc4-207">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="51fc4-207">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="51fc4-208">运行应用。</span><span class="sxs-lookup"><span data-stu-id="51fc4-208">Run the app.</span></span> <span data-ttu-id="51fc4-209">`Index` 组件拥有其自己的计数器，每次选择“单击我”按钮时，计数器值递增 10  。</span><span class="sxs-lookup"><span data-stu-id="51fc4-209">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="51fc4-210">`/counter` 处 `Counter` 组件 (*Counter.razor*) 的值继续递增 1。</span><span class="sxs-lookup"><span data-stu-id="51fc4-210">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="51fc4-211">后续步骤</span><span class="sxs-lookup"><span data-stu-id="51fc4-211">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="51fc4-212">其他资源</span><span class="sxs-lookup"><span data-stu-id="51fc4-212">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
