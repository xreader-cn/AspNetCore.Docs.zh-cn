---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选的工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 04/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 2f10b00adce31c020d46d107c087159c17341beb
ms.sourcegitcommit: 7bb14d005155a5044c7902a08694ee8ccb20c113
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/24/2020
ms.locfileid: "82111066"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="151d7-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="151d7-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="151d7-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="151d7-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="151d7-105">若要开始使用 Blazor，请按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="151d7-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="151d7-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="151d7-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="151d7-107">若要创建 Blazor Server 应用，请安装带有 ASP.NET 和 Web 开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) 的最新版本  。</span><span class="sxs-lookup"><span data-stu-id="151d7-107">To create Blazor Server apps, install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="151d7-108">若要创建 Blazor Server 应用和 Blazor WebAssembly 应用，请安装带有 ASP.NET 和 Web 开发工作负载的 [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) 的最新预览版  。</span><span class="sxs-lookup"><span data-stu-id="151d7-108">To create Blazor Server and Blazor WebAssembly apps, install the latest preview of [Visual Studio 2019](https://visualstudio.microsoft.com/vs/preview/) with the **ASP.NET and web development** workload.</span></span>

   <span data-ttu-id="151d7-109">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>   。</span><span class="sxs-lookup"><span data-stu-id="151d7-109">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="151d7-110">通过运行以下命令来安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 预览版模板：</span><span class="sxs-lookup"><span data-stu-id="151d7-110">Install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

1. <span data-ttu-id="151d7-111">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="151d7-111">Create a new project.</span></span>

1. <span data-ttu-id="151d7-112">选择“Blazor 应用”  。</span><span class="sxs-lookup"><span data-stu-id="151d7-112">Select **Blazor App**.</span></span> <span data-ttu-id="151d7-113">选择“下一步”  。</span><span class="sxs-lookup"><span data-stu-id="151d7-113">Select **Next**.</span></span>

1. <span data-ttu-id="151d7-114">在“项目名称”字段提供项目名称，或接受默认项目名称  。</span><span class="sxs-lookup"><span data-stu-id="151d7-114">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="151d7-115">确认“位置”  条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="151d7-115">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="151d7-116">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="151d7-116">Select **Create**.</span></span>

1. <span data-ttu-id="151d7-117">若要获得 Blazor WebAssembly 体验（Visual Studio 16.6 预览版 2 或更高版本），请选择“Blazor WebAssembly 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="151d7-117">For a Blazor WebAssembly experience (Visual Studio 16.6 Preview 2 or later), choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="151d7-118">若要获得 Blazor Server 体验（Visual Studio 16.4 或更高版本），请选择“Blazor Server 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="151d7-118">For a Blazor Server experience (Visual Studio 16.4 or later), choose the **Blazor Server App** template.</span></span> <span data-ttu-id="151d7-119">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="151d7-119">Select **Create**.</span></span>

1. <span data-ttu-id="151d7-120">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="151d7-120">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="151d7-121">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="151d7-121">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="151d7-122">安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="151d7-122">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="151d7-123">或者，通过运行以下命令安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 预览版模板：</span><span class="sxs-lookup"><span data-stu-id="151d7-123">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="151d7-124">要使用 3.2 预览版 4 的 Blazor WebAssembly 模板，需要 [.NET Core SDK 版本 3.1.201 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)  。</span><span class="sxs-lookup"><span data-stu-id="151d7-124">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="151d7-125">通过在命令行界面中运行 `dotnet --version` 来确认所安装的 .NET Core SDK 版本。</span><span class="sxs-lookup"><span data-stu-id="151d7-125">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="151d7-126">安装 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="151d7-126">Install [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="151d7-127">安装最新的[适用于 Visual Studio Code 的 C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)和 [JavaScript 调试程序（Nightly 版）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)，并将 `debug.javascript.usePreview` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="151d7-127">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

1. <span data-ttu-id="151d7-128">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="151d7-128">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="151d7-129">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="151d7-129">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="151d7-130">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="151d7-130">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="151d7-131">在 Visual Studio Code 中打开 *WebApplication1* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="151d7-131">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="151d7-132">IDE 要求添加资产以用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="151d7-132">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="151d7-133">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="151d7-133">Select **Yes**.</span></span>

1. <span data-ttu-id="151d7-134">在 Blazor Server 中，请使用 Visual Studio Code 调试程序运行该应用。</span><span class="sxs-lookup"><span data-stu-id="151d7-134">With Blazor Server, run the app using the Visual Studio Code debugger.</span></span>

   <span data-ttu-id="151d7-135">在 Blazor Server 中，使用“.NET Core 启动(Blazor 独立版)”启动配置来启动应用，然后使用“.NET Core 在 Chrome 中调试 Blazor WebAssembly”启动配置来启动浏览器（需使用 Chrome）   。</span><span class="sxs-lookup"><span data-stu-id="151d7-135">With Blazor WebAssembly, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration and then start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="151d7-136">有关详细信息，请参阅 <xref:blazor/debug#visual-studio-code>。</span><span class="sxs-lookup"><span data-stu-id="151d7-136">For more information, see <xref:blazor/debug#visual-studio-code>.</span></span>

1. <span data-ttu-id="151d7-137">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="151d7-137">In a browser, navigate to `https://localhost:5001`.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="151d7-138">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="151d7-138">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

<span data-ttu-id="151d7-139">Visual Studio for Mac 支持 Blazor Server。</span><span class="sxs-lookup"><span data-stu-id="151d7-139">Blazor Server is supported in Visual Studio for Mac.</span></span> <span data-ttu-id="151d7-140">目前不支持 Blazor WebAssembly。</span><span class="sxs-lookup"><span data-stu-id="151d7-140">Blazor WebAssembly isn't supported at this time.</span></span> <span data-ttu-id="151d7-141">若要在 macOS 上生成 Blazor WebAssembly 应用，请按照 .NET Core CLI 选项卡上的指导进行操作  。</span><span class="sxs-lookup"><span data-stu-id="151d7-141">To build Blazor WebAssembly apps on macOS, follow the guidance on the **.NET Core CLI** tab.</span></span>

1. <span data-ttu-id="151d7-142">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="151d7-142">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="151d7-143">选择“文件” > “新建解决方案”或创建“新项目”    。</span><span class="sxs-lookup"><span data-stu-id="151d7-143">Select **File** > **New Solution** or create a **New Project**.</span></span>

1. <span data-ttu-id="151d7-144">在边栏中选择“.NET Core” > “应用”   。</span><span class="sxs-lookup"><span data-stu-id="151d7-144">In the sidebar, select **.NET Core** > **App**.</span></span>

1. <span data-ttu-id="151d7-145">选择“Blazor Server 应用”模板  。</span><span class="sxs-lookup"><span data-stu-id="151d7-145">Select the **Blazor Server App** template.</span></span> <span data-ttu-id="151d7-146">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="151d7-146">Select **Create**.</span></span>

   <span data-ttu-id="151d7-147">有关 Blazor Server 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="151d7-147">For information on the Blazor Server hosting model, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="151d7-148">将“目标框架”设置为“.NET Core 3.1”，然后选择“下一步”    。</span><span class="sxs-lookup"><span data-stu-id="151d7-148">Set the **Target Framework** to **.NET Core 3.1** and select **Next**.</span></span>

1. <span data-ttu-id="151d7-149">在“项目名称”字段中，将应用命名为 `WebApplication1` 。</span><span class="sxs-lookup"><span data-stu-id="151d7-149">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="151d7-150">选择“创建”  。</span><span class="sxs-lookup"><span data-stu-id="151d7-150">Select **Create**.</span></span>

1. <span data-ttu-id="151d7-151">选择“运行” > “运行而不调试”以*不使用调试程序*运行应用   。</span><span class="sxs-lookup"><span data-stu-id="151d7-151">Select **Run** > **Run Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="151d7-152">使用“开始调试”运行应用，以*使用调试程序*运行应用  。</span><span class="sxs-lookup"><span data-stu-id="151d7-152">Run the app with **Start Debugging** to run the app *with the debugger*.</span></span>

<span data-ttu-id="151d7-153">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="151d7-153">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="151d7-154">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="151d7-154">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="151d7-155">安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="151d7-155">Install the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span>

1. <span data-ttu-id="151d7-156">或者，通过运行以下命令安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 预览版模板：</span><span class="sxs-lookup"><span data-stu-id="151d7-156">Optionally install the [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) preview template by running the following command:</span></span>

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Components.WebAssembly.Templates::3.2.0-preview5.20216.8
   ```

   > [!NOTE]
   > <span data-ttu-id="151d7-157">要使用 3.2 预览版 4 的 Blazor WebAssembly 模板，需要 [.NET Core SDK 版本 3.1.201 或更高版本](https://dotnet.microsoft.com/download/dotnet-core/3.1)  。</span><span class="sxs-lookup"><span data-stu-id="151d7-157">The [.NET Core SDK version 3.1.201 or later](https://dotnet.microsoft.com/download/dotnet-core/3.1) is **required** to use the 3.2 Preview 4 Blazor WebAssembly template.</span></span> <span data-ttu-id="151d7-158">通过在命令行界面中运行 `dotnet --version` 来确认所安装的 .NET Core SDK 版本。</span><span class="sxs-lookup"><span data-stu-id="151d7-158">Confirm the installed .NET Core SDK version by running `dotnet --version` in a command shell.</span></span>

1. <span data-ttu-id="151d7-159">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="151d7-159">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="151d7-160">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="151d7-160">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="151d7-161">有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。</span><span class="sxs-lookup"><span data-stu-id="151d7-161">For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="151d7-162">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="151d7-162">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="151d7-163">侧栏中的选项卡提供多个页面：</span><span class="sxs-lookup"><span data-stu-id="151d7-163">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="151d7-164">Home</span><span class="sxs-lookup"><span data-stu-id="151d7-164">Home</span></span>
* <span data-ttu-id="151d7-165">计数器</span><span class="sxs-lookup"><span data-stu-id="151d7-165">Counter</span></span>
* <span data-ttu-id="151d7-166">提取数据</span><span class="sxs-lookup"><span data-stu-id="151d7-166">Fetch data</span></span>

<span data-ttu-id="151d7-167">在“计数器”页上，选择“单击我”  按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="151d7-167">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="151d7-168">递增网页的计数器值通常需要编写 JavaScript，但借助 Blazor，可使用 C#。</span><span class="sxs-lookup"><span data-stu-id="151d7-168">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="151d7-169">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="151d7-169">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="151d7-170">浏览器中针对 `/counter` 的请求（由顶部的 `@page` 指令指定）会导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="151d7-170">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="151d7-171">组件呈现为呈现树的内存中表现形式，之后可使用它灵活高效地更新 UI。</span><span class="sxs-lookup"><span data-stu-id="151d7-171">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="151d7-172">每次选择“单击我”按钮时会出现以下情况  ：</span><span class="sxs-lookup"><span data-stu-id="151d7-172">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="151d7-173">触发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="151d7-173">The `onclick` event is fired.</span></span>
* <span data-ttu-id="151d7-174">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="151d7-174">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="151d7-175">递增 `currentCount` 的值。</span><span class="sxs-lookup"><span data-stu-id="151d7-175">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="151d7-176">再次呈现组件。</span><span class="sxs-lookup"><span data-stu-id="151d7-176">The component is rendered again.</span></span>

<span data-ttu-id="151d7-177">运行时将新内容与之前内容进行比较，且仅将已更改内容应用于文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="151d7-177">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="151d7-178">使用 HTML 语法将组件添加到另一个组件。</span><span class="sxs-lookup"><span data-stu-id="151d7-178">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="151d7-179">例如，通过将 `<Counter />` 元素添加到 `Index` 组件，可将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="151d7-179">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="151d7-180">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="151d7-180">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="151d7-181">运行应用。</span><span class="sxs-lookup"><span data-stu-id="151d7-181">Run the app.</span></span> <span data-ttu-id="151d7-182">主页拥有其自己的计数器，该计数器由 `Counter` 组件提供。</span><span class="sxs-lookup"><span data-stu-id="151d7-182">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="151d7-183">组件参数使用特性或[子内容](xref:blazor/components#child-content)指定，允许在子组件上设置属性。</span><span class="sxs-lookup"><span data-stu-id="151d7-183">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="151d7-184">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="151d7-184">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="151d7-185">使用 `[Parameter]` 特性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="151d7-185">Add a public property for `IncrementAmount` with a `[Parameter]` attribute.</span></span>
* <span data-ttu-id="151d7-186">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="151d7-186">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="151d7-187">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="151d7-187">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="151d7-188">使用特性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="151d7-188">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="151d7-189">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="151d7-189">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="151d7-190">运行应用。</span><span class="sxs-lookup"><span data-stu-id="151d7-190">Run the app.</span></span> <span data-ttu-id="151d7-191">`Index` 组件拥有其自己的计数器，每次选择“单击我”按钮时，计数器值递增 10  。</span><span class="sxs-lookup"><span data-stu-id="151d7-191">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="151d7-192">`/counter` 处 `Counter` 组件 (*Counter.razor*) 的值继续递增 1。</span><span class="sxs-lookup"><span data-stu-id="151d7-192">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="151d7-193">后续步骤</span><span class="sxs-lookup"><span data-stu-id="151d7-193">Next steps</span></span>

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="151d7-194">其他资源</span><span class="sxs-lookup"><span data-stu-id="151d7-194">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
