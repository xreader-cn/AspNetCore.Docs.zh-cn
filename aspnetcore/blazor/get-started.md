---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选的工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: bd33d874b3d6122f2ab820e9b147b0e62ba03a58
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648630"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="0c887-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="0c887-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="0c887-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="0c887-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="0c887-105">Blazor 入门：</span><span class="sxs-lookup"><span data-stu-id="0c887-105">Get started with Blazor:</span></span>

1. <span data-ttu-id="0c887-106">安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="0c887-106">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="0c887-107">（可选）安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 模板：</span><span class="sxs-lookup"><span data-stu-id="0c887-107">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) template:</span></span>
   * <span data-ttu-id="0c887-108">安装 [.NET Core 3.1 或更高版本（预览版）SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="0c887-108">Install the [.NET Core 3.1 or later (Preview) SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>
   * <span data-ttu-id="0c887-109">在命令行界面中运行以下命令。</span><span class="sxs-lookup"><span data-stu-id="0c887-109">Run the following command in a command shell.</span></span> <span data-ttu-id="0c887-110">当 Blazor WebAssembly 处于预览状态时，[ Microsoft.AspNetCore.Blazor.Templates ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 包具有预览版本。</span><span class="sxs-lookup"><span data-stu-id="0c887-110">The [Microsoft.AspNetCore.Blazor.Templates](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) package has a preview version while Blazor WebAssembly is in preview.</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
   ```

1. <span data-ttu-id="0c887-111">按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="0c887-111">Follow the guidance for your choice of tooling:</span></span>

   # <a name="visual-studio"></a>[<span data-ttu-id="0c887-112">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="0c887-112">Visual Studio</span></span>](#tab/visual-studio)

   <span data-ttu-id="0c887-113">1\.</span><span class="sxs-lookup"><span data-stu-id="0c887-113">1\.</span></span> <span data-ttu-id="0c887-114">安装 [Visual Studio 2019 版本 16.4 或更高版本](https://visualstudio.microsoft.com/vs/preview/)以及 **ASP.NET 和 Web 开发**工作负荷。</span><span class="sxs-lookup"><span data-stu-id="0c887-114">Install [Visual Studio 2019 version 16.4 or later](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="0c887-115">2\.</span><span class="sxs-lookup"><span data-stu-id="0c887-115">2\.</span></span> <span data-ttu-id="0c887-116">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="0c887-116">Create a new project.</span></span>

   <span data-ttu-id="0c887-117">3\.</span><span class="sxs-lookup"><span data-stu-id="0c887-117">3\.</span></span> <span data-ttu-id="0c887-118">选择“Blazor 应用”  。</span><span class="sxs-lookup"><span data-stu-id="0c887-118">Select **Blazor App**.</span></span> <span data-ttu-id="0c887-119">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="0c887-119">Select **Next**.</span></span>

   <span data-ttu-id="0c887-120">4\.</span><span class="sxs-lookup"><span data-stu-id="0c887-120">4\.</span></span> <span data-ttu-id="0c887-121">在“项目名称”字段提供项目名称，或接受默认项目名称  。</span><span class="sxs-lookup"><span data-stu-id="0c887-121">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="0c887-122">确认“位置”  条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="0c887-122">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="0c887-123">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="0c887-123">Select **Create**.</span></span>

   <span data-ttu-id="0c887-124">5\.</span><span class="sxs-lookup"><span data-stu-id="0c887-124">5\.</span></span> <span data-ttu-id="0c887-125">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="0c887-125">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="0c887-126">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="0c887-126">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="0c887-127">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="0c887-127">Select **Create**.</span></span> <span data-ttu-id="0c887-128">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="0c887-128">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="0c887-129">6\.</span><span class="sxs-lookup"><span data-stu-id="0c887-129">6\.</span></span> <span data-ttu-id="0c887-130">按 Ctrl+F5 运行应用   。</span><span class="sxs-lookup"><span data-stu-id="0c887-130">Press **Ctrl**+**F5** to run the app.</span></span>

   > [!NOTE]
   > <span data-ttu-id="0c887-131">如果为 ASP.NET Core Blazor 之前的预览版本（预览版 6 或更早版本）安装了 Blazor Visual Studio 扩展，则可卸载该扩展。</span><span class="sxs-lookup"><span data-stu-id="0c887-131">If you installed the Blazor Visual Studio extension for a prior preview release of ASP.NET Core Blazor (Preview 6 or earlier), you can uninstall the extension.</span></span> <span data-ttu-id="0c887-132">现在，在命令行界面中安装 Blazor 模板即可在 Visual Studio 中显示这些模板。</span><span class="sxs-lookup"><span data-stu-id="0c887-132">Installing the Blazor templates in a command shell is now sufficient to surface the templates in Visual Studio.</span></span>

   # <a name="visual-studio-code"></a>[<span data-ttu-id="0c887-133">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="0c887-133">Visual Studio Code</span></span>](#tab/visual-studio-code)

   <span data-ttu-id="0c887-134">1\.</span><span class="sxs-lookup"><span data-stu-id="0c887-134">1\.</span></span> <span data-ttu-id="0c887-135">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="0c887-135">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

   <span data-ttu-id="0c887-136">2\.</span><span class="sxs-lookup"><span data-stu-id="0c887-136">2\.</span></span> <span data-ttu-id="0c887-137">安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。</span><span class="sxs-lookup"><span data-stu-id="0c887-137">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp).</span></span>

   <span data-ttu-id="0c887-138">3\.</span><span class="sxs-lookup"><span data-stu-id="0c887-138">3\.</span></span> <span data-ttu-id="0c887-139">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0c887-139">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      <span data-ttu-id="0c887-140">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0c887-140">For a Blazor Server experience, execute the following command in a command shell:</span></span>

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      <span data-ttu-id="0c887-141">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="0c887-141">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="0c887-142">4\.</span><span class="sxs-lookup"><span data-stu-id="0c887-142">4\.</span></span> <span data-ttu-id="0c887-143">在 Visual Studio Code 中打开 *WebApplication1* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="0c887-143">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

   <span data-ttu-id="0c887-144">5\.</span><span class="sxs-lookup"><span data-stu-id="0c887-144">5\.</span></span> <span data-ttu-id="0c887-145">对于 Blazor Server 项目，IDE 要求添加资产用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="0c887-145">For a Blazor Server project, the IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="0c887-146">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="0c887-146">Select **Yes**.</span></span>

   <span data-ttu-id="0c887-147">6\.</span><span class="sxs-lookup"><span data-stu-id="0c887-147">6\.</span></span> <span data-ttu-id="0c887-148">如果使用 Blazor Server 应用，请使用 Visual Studio Code 调试程序运行该应用。</span><span class="sxs-lookup"><span data-stu-id="0c887-148">If using a Blazor Server app, run the app using the Visual Studio Code debugger.</span></span> <span data-ttu-id="0c887-149">如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹执行 `dotnet run`。</span><span class="sxs-lookup"><span data-stu-id="0c887-149">If using a Blazor WebAssembly app, execute `dotnet run` from the app's project folder.</span></span>

   <span data-ttu-id="0c887-150">7\.</span><span class="sxs-lookup"><span data-stu-id="0c887-150">7\.</span></span> <span data-ttu-id="0c887-151">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="0c887-151">In a browser, navigate to `https://localhost:5001`.</span></span>

   # <a name="visual-studio-for-mac"></a>[<span data-ttu-id="0c887-152">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="0c887-152">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

   <span data-ttu-id="0c887-153">1\.</span><span class="sxs-lookup"><span data-stu-id="0c887-153">1\.</span></span> <span data-ttu-id="0c887-154">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="0c887-154">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

   <span data-ttu-id="0c887-155">2\.</span><span class="sxs-lookup"><span data-stu-id="0c887-155">2\.</span></span> <span data-ttu-id="0c887-156">选择“文件” > “新建解决方案”或创建“新项目”    。</span><span class="sxs-lookup"><span data-stu-id="0c887-156">Select **File** > **New Solution** or create a **New Project**.</span></span>

   <span data-ttu-id="0c887-157">3\.</span><span class="sxs-lookup"><span data-stu-id="0c887-157">3\.</span></span> <span data-ttu-id="0c887-158">在边栏中选择“.NET Core” > “应用”   。</span><span class="sxs-lookup"><span data-stu-id="0c887-158">In the sidebar, select **.NET Core** > **App**.</span></span>

   <span data-ttu-id="0c887-159">4\.</span><span class="sxs-lookup"><span data-stu-id="0c887-159">4\.</span></span> <span data-ttu-id="0c887-160">选择“Blazor Server 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="0c887-160">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="0c887-161">目前，Visual Studio for Mac 中仅提供 Blazor Server 模板。</span><span class="sxs-lookup"><span data-stu-id="0c887-161">Only the Blazor Server template is available in Visual Studio for Mac at this time.</span></span> <span data-ttu-id="0c887-162">若要获得 Blazor WebAssembly 体验，请按照“.NET Core CLI”选项卡中的说明进行操作  。选择 Blazor Server 模板后，选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="0c887-162">For a Blazor WebAssembly experience, follow the instructions on the **.NET Core CLI** tab. After selecting the Blazor Server template, select **Next**.</span></span> <span data-ttu-id="0c887-163">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="0c887-163">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   <span data-ttu-id="0c887-164">5\.</span><span class="sxs-lookup"><span data-stu-id="0c887-164">5\.</span></span> <span data-ttu-id="0c887-165">将“目标框架”设置为“.NET Core 3.1”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="0c887-165">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

   <span data-ttu-id="0c887-166">6\.</span><span class="sxs-lookup"><span data-stu-id="0c887-166">6\.</span></span> <span data-ttu-id="0c887-167">在“项目名称”字段中，将应用命名为 `WebApplication1`  。</span><span class="sxs-lookup"><span data-stu-id="0c887-167">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="0c887-168">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="0c887-168">Select **Create**.</span></span>

   <span data-ttu-id="0c887-169">7\.</span><span class="sxs-lookup"><span data-stu-id="0c887-169">7\.</span></span> <span data-ttu-id="0c887-170">选择“运行” > “运行而不调试”以*不使用调试程序*运行应用   。</span><span class="sxs-lookup"><span data-stu-id="0c887-170">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="0c887-171">使用“开始调试”运行应用，以*使用调试程序*运行应用  。</span><span class="sxs-lookup"><span data-stu-id="0c887-171">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

   <span data-ttu-id="0c887-172">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="0c887-172">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

   # <a name="net-core-cli"></a>[<span data-ttu-id="0c887-173">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="0c887-173">.NET Core CLI</span></span>](#tab/netcore-cli/)

   <span data-ttu-id="0c887-174">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0c887-174">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="0c887-175">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="0c887-175">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="0c887-176">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="0c887-176">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

   <span data-ttu-id="0c887-177">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="0c887-177">In a browser, navigate to `https://localhost:5001`.</span></span>

   ---

<span data-ttu-id="0c887-178">侧栏中的选项卡提供多个页面：</span><span class="sxs-lookup"><span data-stu-id="0c887-178">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="0c887-179">Home</span><span class="sxs-lookup"><span data-stu-id="0c887-179">Home</span></span>
* <span data-ttu-id="0c887-180">计数器</span><span class="sxs-lookup"><span data-stu-id="0c887-180">Counter</span></span>
* <span data-ttu-id="0c887-181">提取数据</span><span class="sxs-lookup"><span data-stu-id="0c887-181">Fetch data</span></span>

<span data-ttu-id="0c887-182">在“计数器”页上，选择“单击我”  按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="0c887-182">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="0c887-183">递增网页的计数器值通常需要编写 JavaScript，但借助 Blazor，可使用 C#。</span><span class="sxs-lookup"><span data-stu-id="0c887-183">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="0c887-184">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="0c887-184">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="0c887-185">浏览器中针对 `/counter` 的请求（由顶部的 `@page` 指令指定）会导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="0c887-185">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="0c887-186">组件呈现为呈现树的内存中表现形式，之后可使用它灵活高效地更新 UI。</span><span class="sxs-lookup"><span data-stu-id="0c887-186">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="0c887-187">每次选择“单击我”按钮时会出现以下情况  ：</span><span class="sxs-lookup"><span data-stu-id="0c887-187">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="0c887-188">触发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="0c887-188">The `onclick` event is fired.</span></span>
* <span data-ttu-id="0c887-189">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="0c887-189">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="0c887-190">递增 `currentCount` 的值。</span><span class="sxs-lookup"><span data-stu-id="0c887-190">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="0c887-191">再次呈现组件。</span><span class="sxs-lookup"><span data-stu-id="0c887-191">The component is rendered again.</span></span>

<span data-ttu-id="0c887-192">运行时将新内容与之前内容进行比较，且仅将已更改内容应用于文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="0c887-192">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="0c887-193">使用 HTML 语法将组件添加到另一个组件。</span><span class="sxs-lookup"><span data-stu-id="0c887-193">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="0c887-194">例如，通过将 `<Counter />` 元素添加到 `Index` 组件，可将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="0c887-194">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="0c887-195">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="0c887-195">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="0c887-196">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0c887-196">Run the app.</span></span> <span data-ttu-id="0c887-197">主页拥有其自己的计数器，该计数器由 `Counter` 组件提供。</span><span class="sxs-lookup"><span data-stu-id="0c887-197">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="0c887-198">组件参数使用特性或[子内容](xref:blazor/components#child-content)指定，允许在子组件上设置属性。</span><span class="sxs-lookup"><span data-stu-id="0c887-198">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="0c887-199">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="0c887-199">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="0c887-200">使用 `[Parameter]` 特性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="0c887-200">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="0c887-201">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="0c887-201">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="0c887-202">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="0c887-202">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="0c887-203">使用特性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="0c887-203">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="0c887-204">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="0c887-204">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="0c887-205">运行应用。</span><span class="sxs-lookup"><span data-stu-id="0c887-205">Run the app.</span></span> <span data-ttu-id="0c887-206">`Index` 组件拥有其自己的计数器，每次选择“单击我”按钮时，计数器值递增 10  。</span><span class="sxs-lookup"><span data-stu-id="0c887-206">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="0c887-207">`/counter` 处 `Counter` 组件 (*Counter.razor*) 的值继续递增 1。</span><span class="sxs-lookup"><span data-stu-id="0c887-207">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="0c887-208">后续步骤</span><span class="sxs-lookup"><span data-stu-id="0c887-208">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="0c887-209">其他资源</span><span class="sxs-lookup"><span data-stu-id="0c887-209">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
