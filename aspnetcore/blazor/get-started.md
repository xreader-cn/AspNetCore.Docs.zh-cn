---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选的工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 05/31/2020
no-loc:
- Blazor
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: 08229283882928c4cc733de19840d25872846c97
ms.sourcegitcommit: cd73744bd75fdefb31d25ab906df237f07ee7a0a
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/05/2020
ms.locfileid: "84452026"
---
# <a name="get-started-with-aspnet-core-blazor"></a><span data-ttu-id="94888-103">ASP.NET Core Blazor 入门</span><span class="sxs-lookup"><span data-stu-id="94888-103">Get started with ASP.NET Core Blazor</span></span>

<span data-ttu-id="94888-104">作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)</span><span class="sxs-lookup"><span data-stu-id="94888-104">By [Daniel Roth](https://github.com/danroth27) and [Luke Latham](https://github.com/guardrex)</span></span>

<span data-ttu-id="94888-105">若要开始使用 Blazor，请按照所选工具的指南进行操作：</span><span class="sxs-lookup"><span data-stu-id="94888-105">To get started with Blazor, follow the guidance for your choice of tooling:</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="94888-106">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="94888-106">Visual Studio</span></span>](#tab/visual-studio)

1. <span data-ttu-id="94888-107">安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。</span><span class="sxs-lookup"><span data-stu-id="94888-107">Install the latest version of [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/) with the **ASP.NET and web development** workload.</span></span>

1. <span data-ttu-id="94888-108">创建新项目。</span><span class="sxs-lookup"><span data-stu-id="94888-108">Create a new project.</span></span>

1. <span data-ttu-id="94888-109">选择“Blazor 应用”。</span><span class="sxs-lookup"><span data-stu-id="94888-109">Select **Blazor App**.</span></span> <span data-ttu-id="94888-110">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="94888-110">Select **Next**.</span></span>

1. <span data-ttu-id="94888-111">在“项目名称”字段提供项目名称，或接受默认项目名称。</span><span class="sxs-lookup"><span data-stu-id="94888-111">Provide a project name in the **Project name** field or accept the default project name.</span></span> <span data-ttu-id="94888-112">确认“位置”条目正确无误或为项目提供位置。</span><span class="sxs-lookup"><span data-stu-id="94888-112">Confirm the **Location** entry is correct or provide a location for the project.</span></span> <span data-ttu-id="94888-113">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="94888-113">Select **Create**.</span></span>

1. <span data-ttu-id="94888-114">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="94888-114">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="94888-115">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="94888-115">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="94888-116">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="94888-116">Select **Create**.</span></span>

   <span data-ttu-id="94888-117">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="94888-117">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="94888-118">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="94888-118">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-code"></a>[<span data-ttu-id="94888-119">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="94888-119">Visual Studio Code</span></span>](#tab/visual-studio-code)

1. <span data-ttu-id="94888-120">安装最新版本的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="94888-120">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="94888-121">如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：</span><span class="sxs-lookup"><span data-stu-id="94888-121">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="94888-122">安装最新版本的 [Visual Studio Code](https://code.visualstudio.com/)。</span><span class="sxs-lookup"><span data-stu-id="94888-122">Install the latest version of [Visual Studio Code](https://code.visualstudio.com/).</span></span>

1. <span data-ttu-id="94888-123">安装最新的[适用于 Visual Studio Code 的 C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)和 [JavaScript 调试程序（Nightly 版）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)，并将 `debug.javascript.usePreview` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="94888-123">Install the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

  <span data-ttu-id="94888-124">若要使用 VS Code UI 将 `debug.javascript.usePreview` 设置为 `true`，请打开“文件” > “首选项” > “设置”，并搜索 `debug javascript use preview`。  </span><span class="sxs-lookup"><span data-stu-id="94888-124">To set `debug.javascript.usePreview` to `true` using the VS Code UI, open **File** > **Preferences** > **Settings** and search for `debug javascript use preview`.</span></span> <span data-ttu-id="94888-125">选中“为 Node.js 和 Chrome 使用新的预览版 JavaScript 调试器”复选框。</span><span class="sxs-lookup"><span data-stu-id="94888-125">Select the check box for **Use the new in-preview JavaScript debugger for Node.js and Chrome**.</span></span>

1. <span data-ttu-id="94888-126">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="94888-126">For a Blazor WebAssembly experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   <span data-ttu-id="94888-127">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="94888-127">For a Blazor Server experience, execute the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   <span data-ttu-id="94888-128">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="94888-128">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="94888-129">在 Visual Studio Code 中打开 *WebApplication1* 文件夹。</span><span class="sxs-lookup"><span data-stu-id="94888-129">Open the *WebApplication1* folder in Visual Studio Code.</span></span>

1. <span data-ttu-id="94888-130">IDE 要求添加资产以用于生成和调试项目。</span><span class="sxs-lookup"><span data-stu-id="94888-130">The IDE requests that you add assets to build and debug the project.</span></span> <span data-ttu-id="94888-131">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="94888-131">Select **Yes**.</span></span>

1. <span data-ttu-id="94888-132">按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。</span><span class="sxs-lookup"><span data-stu-id="94888-132">Press <kbd>Ctrl</kbd>+<kbd>F5</kbd> to run the app.</span></span>

# <a name="visual-studio-for-mac"></a>[<span data-ttu-id="94888-133">Visual Studio for Mac</span><span class="sxs-lookup"><span data-stu-id="94888-133">Visual Studio for Mac</span></span>](#tab/visual-studio-mac)

1. <span data-ttu-id="94888-134">安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。</span><span class="sxs-lookup"><span data-stu-id="94888-134">Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/).</span></span>

1. <span data-ttu-id="94888-135">选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。</span><span class="sxs-lookup"><span data-stu-id="94888-135">Select **File** > **New Solution** or create a **New** project from the **Start Window**.</span></span>

1. <span data-ttu-id="94888-136">在边栏中，选择“Web 和控制台” > “应用”。 </span><span class="sxs-lookup"><span data-stu-id="94888-136">In the sidebar, select **Web and Console** > **App**.</span></span>

   <span data-ttu-id="94888-137">若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="94888-137">For a Blazor WebAssembly experience, choose the **Blazor WebAssembly App** template.</span></span> <span data-ttu-id="94888-138">若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。</span><span class="sxs-lookup"><span data-stu-id="94888-138">For a Blazor Server experience, choose the **Blazor Server App** template.</span></span> <span data-ttu-id="94888-139">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="94888-139">Select **Next**.</span></span>

   <span data-ttu-id="94888-140">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="94888-140">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="94888-141">确认以下配置：</span><span class="sxs-lookup"><span data-stu-id="94888-141">Confirm the following configurations:</span></span>

   * <span data-ttu-id="94888-142">“目标框架”设置为“.NET Core 3.1” 。</span><span class="sxs-lookup"><span data-stu-id="94888-142">**Target Framework** set to **.NET Core 3.1**.</span></span>
   * <span data-ttu-id="94888-143">“身份验证”设置为“无身份验证” 。</span><span class="sxs-lookup"><span data-stu-id="94888-143">**Authentication** set to **No Authentication**.</span></span>
   
   <span data-ttu-id="94888-144">选择“下一步”。</span><span class="sxs-lookup"><span data-stu-id="94888-144">Select **Next**.</span></span>

1. <span data-ttu-id="94888-145">在“项目名称”字段中，将应用命名为 `WebApplication1`。</span><span class="sxs-lookup"><span data-stu-id="94888-145">In the **Project Name** field, name the app `WebApplication1`.</span></span> <span data-ttu-id="94888-146">选择“创建”。</span><span class="sxs-lookup"><span data-stu-id="94888-146">Select **Create**.</span></span>

1. <span data-ttu-id="94888-147">选择“运行” > “启动而不调试”以不使用调试程序运行应用 。</span><span class="sxs-lookup"><span data-stu-id="94888-147">Select **Run** > **Start Without Debugging** to run the app *without the debugger*.</span></span> <span data-ttu-id="94888-148">使用“运行” > “开始调试”运行应用，或者使用“运行 (&#9654;)”按钮通过调试程序运行应用。 </span><span class="sxs-lookup"><span data-stu-id="94888-148">Run the app with **Run** > **Start Debugging** or the Run (&#9654;) button to run the app *with the debugger*.</span></span>

<span data-ttu-id="94888-149">如果出现信任开发证书的提示，请信任证书并继续操作。</span><span class="sxs-lookup"><span data-stu-id="94888-149">If a prompt appears to trust the development certificate, trust the certificate and continue.</span></span> <span data-ttu-id="94888-150">信任证书需要使用用户密码和密钥链密码。</span><span class="sxs-lookup"><span data-stu-id="94888-150">The user and keychain passwords are required to trust the certificate.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="94888-151">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="94888-151">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="94888-152">安装最新版本的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。</span><span class="sxs-lookup"><span data-stu-id="94888-152">Install the latest version of the [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1).</span></span> <span data-ttu-id="94888-153">如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：</span><span class="sxs-lookup"><span data-stu-id="94888-153">If you previously installed the SDK, you can determine your installed version by executing the following command in a command shell:</span></span>

   ```dotnetcli
   dotnet --version
   ```

1. <span data-ttu-id="94888-154">若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="94888-154">For a Blazor WebAssembly experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="94888-155">若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：</span><span class="sxs-lookup"><span data-stu-id="94888-155">For a Blazor Server experience, execute the following commands in a command shell:</span></span>

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   <span data-ttu-id="94888-156">有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 </span><span class="sxs-lookup"><span data-stu-id="94888-156">For information on the two Blazor hosting models, *Blazor WebAssembly* and *Blazor Server*, see <xref:blazor/hosting-models>.</span></span>

1. <span data-ttu-id="94888-157">在浏览器中导航到 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="94888-157">In a browser, navigate to `https://localhost:5001`.</span></span>

---

<span data-ttu-id="94888-158">侧栏中的选项卡提供多个页面：</span><span class="sxs-lookup"><span data-stu-id="94888-158">Multiple pages are available from tabs in the sidebar:</span></span>

* <span data-ttu-id="94888-159">Home</span><span class="sxs-lookup"><span data-stu-id="94888-159">Home</span></span>
* <span data-ttu-id="94888-160">计数器</span><span class="sxs-lookup"><span data-stu-id="94888-160">Counter</span></span>
* <span data-ttu-id="94888-161">提取数据</span><span class="sxs-lookup"><span data-stu-id="94888-161">Fetch data</span></span>

<span data-ttu-id="94888-162">在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。</span><span class="sxs-lookup"><span data-stu-id="94888-162">On the Counter page, select the **Click me** button to increment the counter without a page refresh.</span></span> <span data-ttu-id="94888-163">递增网页的计数器值通常需要编写 JavaScript，但借助 Blazor，可使用 C#。</span><span class="sxs-lookup"><span data-stu-id="94888-163">Incrementing a counter in a webpage normally requires writing JavaScript, but with Blazor you can use C#.</span></span>

<span data-ttu-id="94888-164">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="94888-164">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

<span data-ttu-id="94888-165">浏览器中针对 `/counter` 的请求（由顶部的 `@page` 指令指定）会导致 `Counter` 组件呈现其内容。</span><span class="sxs-lookup"><span data-stu-id="94888-165">A request for `/counter` in the browser, as specified by the `@page` directive at the top, causes the `Counter` component to render its content.</span></span> <span data-ttu-id="94888-166">组件呈现为呈现树的内存中表现形式，之后可使用它灵活高效地更新 UI。</span><span class="sxs-lookup"><span data-stu-id="94888-166">Components render into an in-memory representation of the render tree that can then be used to update the UI in a flexible and efficient way.</span></span>

<span data-ttu-id="94888-167">每次选择“单击我”按钮时会出现以下情况：</span><span class="sxs-lookup"><span data-stu-id="94888-167">Each time the **Click me** button is selected:</span></span>

* <span data-ttu-id="94888-168">触发 `onclick` 事件。</span><span class="sxs-lookup"><span data-stu-id="94888-168">The `onclick` event is fired.</span></span>
* <span data-ttu-id="94888-169">调用 `IncrementCount` 方法。</span><span class="sxs-lookup"><span data-stu-id="94888-169">The `IncrementCount` method is called.</span></span>
* <span data-ttu-id="94888-170">递增 `currentCount` 的值。</span><span class="sxs-lookup"><span data-stu-id="94888-170">The `currentCount` is incremented.</span></span>
* <span data-ttu-id="94888-171">再次呈现组件。</span><span class="sxs-lookup"><span data-stu-id="94888-171">The component is rendered again.</span></span>

<span data-ttu-id="94888-172">运行时将新内容与之前内容进行比较，且仅将已更改内容应用于文档对象模型 (DOM)。</span><span class="sxs-lookup"><span data-stu-id="94888-172">The runtime compares the new content to the previous content and only applies the changed content to the Document Object Model (DOM).</span></span>

<span data-ttu-id="94888-173">使用 HTML 语法将组件添加到另一个组件。</span><span class="sxs-lookup"><span data-stu-id="94888-173">Add a component to another component using HTML syntax.</span></span> <span data-ttu-id="94888-174">例如，通过将 `<Counter />` 元素添加到 `Index` 组件，可将 `Counter` 组件添加到应用的主页。</span><span class="sxs-lookup"><span data-stu-id="94888-174">For example, add the `Counter` component to the app's homepage by adding a `<Counter />` element to the `Index` component.</span></span>

<span data-ttu-id="94888-175">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="94888-175">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

<span data-ttu-id="94888-176">运行应用。</span><span class="sxs-lookup"><span data-stu-id="94888-176">Run the app.</span></span> <span data-ttu-id="94888-177">主页拥有其自己的计数器，该计数器由 `Counter` 组件提供。</span><span class="sxs-lookup"><span data-stu-id="94888-177">The homepage has its own counter provided by the `Counter` component.</span></span>

<span data-ttu-id="94888-178">组件参数使用特性或[子内容](xref:blazor/components#child-content)指定，允许在子组件上设置属性。</span><span class="sxs-lookup"><span data-stu-id="94888-178">Component parameters are specified using attributes or [child content](xref:blazor/components#child-content), which allow you to set properties on the child component.</span></span> <span data-ttu-id="94888-179">若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：</span><span class="sxs-lookup"><span data-stu-id="94888-179">To add a parameter to the `Counter` component, update the component's `@code` block:</span></span>

* <span data-ttu-id="94888-180">使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性为 `IncrementAmount` 添加公共属性。</span><span class="sxs-lookup"><span data-stu-id="94888-180">Add a public property for `IncrementAmount` with a [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) attribute.</span></span>
* <span data-ttu-id="94888-181">增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="94888-181">Change the `IncrementCount` method to use the `IncrementAmount` when increasing the value of `currentCount`.</span></span>

<span data-ttu-id="94888-182">*Pages/Counter.razor*：</span><span class="sxs-lookup"><span data-stu-id="94888-182">*Pages/Counter.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

<span data-ttu-id="94888-183">使用特性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount`。</span><span class="sxs-lookup"><span data-stu-id="94888-183">Specify the `IncrementAmount` in the `Index` component's `<Counter>` element using an attribute.</span></span>

<span data-ttu-id="94888-184">*Pages/Index.razor*：</span><span class="sxs-lookup"><span data-stu-id="94888-184">*Pages/Index.razor*:</span></span>

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

<span data-ttu-id="94888-185">运行应用。</span><span class="sxs-lookup"><span data-stu-id="94888-185">Run the app.</span></span> <span data-ttu-id="94888-186">`Index` 组件拥有其自己的计数器，每次选择“单击我”按钮时，计数器值递增 10。</span><span class="sxs-lookup"><span data-stu-id="94888-186">The `Index` component has its own counter that increments by ten each time the **Click me** button is selected.</span></span> <span data-ttu-id="94888-187">`/counter` 处 `Counter` 组件 (*Counter.razor*) 的值继续递增 1。</span><span class="sxs-lookup"><span data-stu-id="94888-187">The `Counter` component (*Counter.razor*) at `/counter` continues to increment by one.</span></span>

## <a name="next-steps"></a><span data-ttu-id="94888-188">后续步骤</span><span class="sxs-lookup"><span data-stu-id="94888-188">Next steps</span></span>

<span data-ttu-id="94888-189">逐步生成 Blazor 应用：</span><span class="sxs-lookup"><span data-stu-id="94888-189">Build a Blazor app step-by-step:</span></span>

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a><span data-ttu-id="94888-190">其他资源</span><span class="sxs-lookup"><span data-stu-id="94888-190">Additional resources</span></span>

* <xref:blazor/templates>
* <xref:signalr/introduction>
