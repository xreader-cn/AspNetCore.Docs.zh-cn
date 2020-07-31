---
title: 调试 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何调试 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 07/27/2020
no-loc:
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: blazor/debug
ms.openlocfilehash: b4199c3a99af5875c5d9a87f29f7c7e2758ffd71
ms.sourcegitcommit: 5a36758cca2861aeb10840093e46d273a6e6e91d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/28/2020
ms.locfileid: "87303555"
---
# <a name="debug-aspnet-core-no-locblazor-webassembly"></a><span data-ttu-id="e00c7-103">调试 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e00c7-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="e00c7-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="e00c7-104">Daniel Roth</span></span>](https://github.com/danroth27)

<span data-ttu-id="e00c7-105">可以使用基于 Chromium 的浏览器 (Edge/Chrome) 中的浏览器开发工具调试 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span> <span data-ttu-id="e00c7-106">也可以使用 Visual Studio 或 Visual Studio Code 调试应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-106">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="e00c7-107">可用方案包括：</span><span class="sxs-lookup"><span data-stu-id="e00c7-107">Available scenarios include:</span></span>

* <span data-ttu-id="e00c7-108">设置和删除断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="e00c7-109">使用 Visual Studio 和 Visual Studio Code 中的调试支持来运行应用（<kbd>F5</kbd> 支持）。</span><span class="sxs-lookup"><span data-stu-id="e00c7-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="e00c7-110">单步执行代码 (<kbd>F10</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="e00c7-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="e00c7-111">在浏览器中使用 <kbd>F8</kbd> 恢复代码执行，在 Visual Studio 或 Visual Studio Code 中使用 <kbd>F5</kbd> 恢复代码执行。</span><span class="sxs-lookup"><span data-stu-id="e00c7-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="e00c7-112">在“局部变量”视图中，观察局部变量的值。</span><span class="sxs-lookup"><span data-stu-id="e00c7-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="e00c7-113">查看调用堆栈，包括从 JavaScript 到 .NET 以及从 .NET 到 JavaScript 的调用链。</span><span class="sxs-lookup"><span data-stu-id="e00c7-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="e00c7-114">目前，无法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e00c7-114">For now, you *can't*:</span></span>

* <span data-ttu-id="e00c7-115">出现未经处理的异常时中断。</span><span class="sxs-lookup"><span data-stu-id="e00c7-115">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="e00c7-116">在应用启动期间命中断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-116">Hit breakpoints during app startup.</span></span>

<span data-ttu-id="e00c7-117">在即将发布的版本中，我们将继续改进调试体验。</span><span class="sxs-lookup"><span data-stu-id="e00c7-117">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="e00c7-118">先决条件</span><span class="sxs-lookup"><span data-stu-id="e00c7-118">Prerequisites</span></span>

<span data-ttu-id="e00c7-119">调试需要使用以下浏览器之一：</span><span class="sxs-lookup"><span data-stu-id="e00c7-119">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="e00c7-120">Google Chrome（版本 70 或更高版本）（默认）</span><span class="sxs-lookup"><span data-stu-id="e00c7-120">Google Chrome (version 70 or later) (default)</span></span>
* <span data-ttu-id="e00c7-121">Microsoft Edge（版本 80 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="e00c7-121">Microsoft Edge (version 80 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="e00c7-122">在 Visual Studio 和 Visual Studio Code 中启用调试</span><span class="sxs-lookup"><span data-stu-id="e00c7-122">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="e00c7-123">若要为现有 Blazor WebAssembly 应用启用调试，请更新启动项目中的 `launchSettings.json` 文件，使每个启动配置文件包含以下 `inspectUri` 属性：</span><span class="sxs-lookup"><span data-stu-id="e00c7-123">To enable debugging for an existing Blazor WebAssembly app, update the `launchSettings.json` file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="e00c7-124">更新后，`launchSettings.json` 文件应类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="e00c7-124">Once updated, the `launchSettings.json` file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="e00c7-125">`inspectUri` 属性具有以下作用：</span><span class="sxs-lookup"><span data-stu-id="e00c7-125">The `inspectUri` property:</span></span>

* <span data-ttu-id="e00c7-126">使 IDE 能够检测到该应用为 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-126">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="e00c7-127">指示脚本调试基础结构通过 Blazor 的调试代理连接到浏览器。</span><span class="sxs-lookup"><span data-stu-id="e00c7-127">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

<span data-ttu-id="e00c7-128">已启动的浏览器 (`browserInspectUri`) 上 WebSocket 协议 (`wsProtocol`)、主机 (`url.hostname`)、端口 (`url.port`) 和检查器 URI 的占位符值由框架提供。</span><span class="sxs-lookup"><span data-stu-id="e00c7-128">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="e00c7-129">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="e00c7-129">Visual Studio</span></span>

<span data-ttu-id="e00c7-130">要在 Visual Studio 中调试 Blazor WebAssembly 应用，请按以下步骤执行：</span><span class="sxs-lookup"><span data-stu-id="e00c7-130">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="e00c7-131">创建新的 ASP.NET Core 托管 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-131">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="e00c7-132">按 <kbd>F5</kbd> 在调试器中运行应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-132">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="e00c7-133">在 `IncrementCount` 方法的 `Pages/Counter.razor` 中设置断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-133">Set a breakpoint in `Pages/Counter.razor` in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="e00c7-134">浏览到“`Counter`”选项卡，选择该按钮以命中断点：</span><span class="sxs-lookup"><span data-stu-id="e00c7-134">Browse to the **`Counter`** tab and select the button to hit the breakpoint:</span></span>

   ![调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="e00c7-136">查看局部变量窗口中 `currentCount` 字段的值：</span><span class="sxs-lookup"><span data-stu-id="e00c7-136">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![查看局部变量](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="e00c7-138">按 <kbd>F5</kbd> 继续执行。</span><span class="sxs-lookup"><span data-stu-id="e00c7-138">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="e00c7-139">调试 Blazor WebAssembly 应用时，还可以调试服务器代码：</span><span class="sxs-lookup"><span data-stu-id="e00c7-139">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="e00c7-140">在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 的“`Pages/FetchData.razor`”页中设置断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-140">Set a breakpoint in the `Pages/FetchData.razor` page in <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A>.</span></span>
1. <span data-ttu-id="e00c7-141">在 `Get` 操作方法的 `WeatherForecastController` 中设置一个断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-141">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="e00c7-142">浏览到“`Fetch Data`”选项卡，在 `FetchData` 组件中的首个断点向服务器发出 HTTP 请求前命中该断点：</span><span class="sxs-lookup"><span data-stu-id="e00c7-142">Browse to the **`Fetch Data`** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![调试提取数据](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="e00c7-144">按 <kbd>F5</kbd> 继续执行，然后在服务器上命中 `WeatherForecastController` 中的断点：</span><span class="sxs-lookup"><span data-stu-id="e00c7-144">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![调试服务器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="e00c7-146">再次按 <kbd>F5</kbd> 继续执行，查看是否呈现天气预报表。</span><span class="sxs-lookup"><span data-stu-id="e00c7-146">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="e00c7-147">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="e00c7-147">Visual Studio Code</span></span>

### <a name="debug-standalone-no-locblazor-webassembly"></a><span data-ttu-id="e00c7-148">调试独立 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e00c7-148">Debug standalone Blazor WebAssembly</span></span>

1. <span data-ttu-id="e00c7-149">在 VS Code 中打开独立 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-149">Open the standalone Blazor WebAssembly app in VS Code.</span></span>

   <span data-ttu-id="e00c7-150">可能会收到以下指明还需要其他设置才能启用调试的通知：</span><span class="sxs-lookup"><span data-stu-id="e00c7-150">You may receive the following notification that additional setup is required to enable debugging:</span></span>
   
   ![其他设置要求](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)
   
   <span data-ttu-id="e00c7-152">如果收到通知，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="e00c7-152">If you receive the notification:</span></span>

   * <span data-ttu-id="e00c7-153">确认是否已安装最新的[适用于 Visual Studio Code 的 C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)。</span><span class="sxs-lookup"><span data-stu-id="e00c7-153">Confirm that the latest [C# for Visual Studio Code extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) is installed.</span></span> <span data-ttu-id="e00c7-154">若要检查是否已安装此扩展，请在菜单栏中依次打开“视图” > “扩展”，或选择“活动”边栏中的“扩展”图标。</span><span class="sxs-lookup"><span data-stu-id="e00c7-154">To inspect the installed extensions, open **View** > **Extensions** from the menu bar or select the **Extensions** icon in the **Activity** sidebar.</span></span>
   * <span data-ttu-id="e00c7-155">确认已启用 JavaScript 预览调试。</span><span class="sxs-lookup"><span data-stu-id="e00c7-155">Confirm that JavaScript preview debugging is enabled.</span></span> <span data-ttu-id="e00c7-156">在菜单栏中打开设置（依次选择“文件” > “首选项” > “设置”）。</span><span class="sxs-lookup"><span data-stu-id="e00c7-156">Open the settings from the menu bar (**File** > **Preferences** > **Settings**).</span></span> <span data-ttu-id="e00c7-157">使用关键字 `debug preview` 进行搜索。</span><span class="sxs-lookup"><span data-stu-id="e00c7-157">Search using the keywords `debug preview`.</span></span> <span data-ttu-id="e00c7-158">在搜索结果中，确认是否已选中“调试”>“JavaScript:使用预览”复选框。</span><span class="sxs-lookup"><span data-stu-id="e00c7-158">In the search results, confirm that the check box for **Debug > JavaScript: Use Preview** is checked.</span></span>
   * <span data-ttu-id="e00c7-159">重载窗口。</span><span class="sxs-lookup"><span data-stu-id="e00c7-159">Reload the window.</span></span>

1. <span data-ttu-id="e00c7-160">使用 <kbd>F5</kbd> 键盘快捷方式或菜单项启动调试。</span><span class="sxs-lookup"><span data-stu-id="e00c7-160">Start debugging using the <kbd>F5</kbd> keyboard shortcut or the menu item.</span></span>

1. <span data-ttu-id="e00c7-161">出现提示时，选择“Blazor WebAssembly 调试”选项以启动调试。</span><span class="sxs-lookup"><span data-stu-id="e00c7-161">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

   ![可用调试选项列表](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="e00c7-163">此时会启动独立应用，并打开调试浏览器。</span><span class="sxs-lookup"><span data-stu-id="e00c7-163">The standalone app is launched, and a debugging browser is opened.</span></span>

1. <span data-ttu-id="e00c7-164">在 `Counter` 组件的 `IncrementCount` 方法中设置一个断点，然后选择该按钮命中该断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-164">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code 中的调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-no-locblazor-webassembly"></a><span data-ttu-id="e00c7-166">调试托管 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="e00c7-166">Debug hosted Blazor WebAssembly</span></span>

1. <span data-ttu-id="e00c7-167">在 VS Code 中打开托管 Blazor WebAssembly 应用的“解决方案”文件夹。</span><span class="sxs-lookup"><span data-stu-id="e00c7-167">Open the hosted Blazor WebAssembly app's solution folder in VS Code.</span></span>

1. <span data-ttu-id="e00c7-168">如果没有为项目设置启动配置，将显示以下通知。</span><span class="sxs-lookup"><span data-stu-id="e00c7-168">If there's no launch configuration set for the project, the following notification appears.</span></span> <span data-ttu-id="e00c7-169">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="e00c7-169">Select **Yes**.</span></span>

   ![添加所需资产](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="e00c7-171">在窗口顶部的命令面板中，选择托管解决方案内的“服务器”项目。</span><span class="sxs-lookup"><span data-stu-id="e00c7-171">In the command palette at the top of the window, select the *Server* project within the hosted solution.</span></span>

<span data-ttu-id="e00c7-172">这会生成一个 `launch.json` 文件，其中包含用于启动调试器的启动配置。</span><span class="sxs-lookup"><span data-stu-id="e00c7-172">A `launch.json` file is generated with the launch configuration for launching the debugger.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="e00c7-173">附加到现有的调试会话</span><span class="sxs-lookup"><span data-stu-id="e00c7-173">Attach to an existing debugging session</span></span>

<span data-ttu-id="e00c7-174">若要附加到正在运行的 Blazor 应用，请创建一个具有以下配置的 `launch.json` 文件：</span><span class="sxs-lookup"><span data-stu-id="e00c7-174">To attach to a running Blazor app, create a `launch.json` file with the following configuration:</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="e00c7-175">只有独立应用才支持附加到调试会话。</span><span class="sxs-lookup"><span data-stu-id="e00c7-175">Attaching to a debugging session is only supported for standalone apps.</span></span> <span data-ttu-id="e00c7-176">若要使用完整堆栈调试，必须从 VS Code 启动应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-176">To use full-stack debugging, you must launch the app from VS Code.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="e00c7-177">启动配置选项</span><span class="sxs-lookup"><span data-stu-id="e00c7-177">Launch configuration options</span></span>

<span data-ttu-id="e00c7-178">`blazorwasm` 调试类型 (`.vscode/launch.json`) 支持以下启动配置选项。</span><span class="sxs-lookup"><span data-stu-id="e00c7-178">The following launch configuration options are supported for the `blazorwasm` debug type (`.vscode/launch.json`).</span></span>

| <span data-ttu-id="e00c7-179">选项</span><span class="sxs-lookup"><span data-stu-id="e00c7-179">Option</span></span>    | <span data-ttu-id="e00c7-180">描述</span><span class="sxs-lookup"><span data-stu-id="e00c7-180">Description</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="e00c7-181">使用 `launch` 启动调试会话并将其附加到 Blazor WebAssembly 应用，或使用 `attach` 将调试会话附加到已运行的应用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-181">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `url`     | <span data-ttu-id="e00c7-182">调试时要在浏览器中打开的 URL。</span><span class="sxs-lookup"><span data-stu-id="e00c7-182">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="e00c7-183">默认为 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="e00c7-183">Defaults to `https://localhost:5001`.</span></span> |
| `browser` | <span data-ttu-id="e00c7-184">要为调试会话启动的浏览器。</span><span class="sxs-lookup"><span data-stu-id="e00c7-184">The browser to launch for the debugging session.</span></span> <span data-ttu-id="e00c7-185">设置为 `edge` 或 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="e00c7-185">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="e00c7-186">默认为 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="e00c7-186">Defaults to `chrome`.</span></span> |
| `trace`   | <span data-ttu-id="e00c7-187">用于从 JS 调试程序生成日志。</span><span class="sxs-lookup"><span data-stu-id="e00c7-187">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="e00c7-188">设置为 `true` 即可生成日志。</span><span class="sxs-lookup"><span data-stu-id="e00c7-188">Set to `true` to generate logs.</span></span> |
| `hosted`  | <span data-ttu-id="e00c7-189">如果要启动和调试托管的 Blazor WebAssembly 应用，则必须设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="e00c7-189">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> |
| `webRoot` | <span data-ttu-id="e00c7-190">指定 Web 服务器的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="e00c7-190">Specifies the absolute path of the web server.</span></span> <span data-ttu-id="e00c7-191">如果应用是从子路由中提供的，则应设置它。</span><span class="sxs-lookup"><span data-stu-id="e00c7-191">Should be set if an app is served from a sub-route.</span></span> |
| `timeout` | <span data-ttu-id="e00c7-192">等待调试会话附加完成的毫秒数。</span><span class="sxs-lookup"><span data-stu-id="e00c7-192">The number of milliseconds to wait for the debugging session to attach.</span></span> <span data-ttu-id="e00c7-193">默认值为 30,000 毫秒（30 秒）。</span><span class="sxs-lookup"><span data-stu-id="e00c7-193">Defaults to 30,000 milliseconds (30 seconds).</span></span> |
| `program` | <span data-ttu-id="e00c7-194">为运行托管应用的服务器而对可执行文件进行的引用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-194">A reference to the executable to run the server of the hosted app.</span></span> <span data-ttu-id="e00c7-195">如果 `hosted` 是 `true`，则必须设置它。</span><span class="sxs-lookup"><span data-stu-id="e00c7-195">Must be set if `hosted` is `true`.</span></span> |
| `cwd`     | <span data-ttu-id="e00c7-196">要在其中启动应用的工作目录。</span><span class="sxs-lookup"><span data-stu-id="e00c7-196">The working directory to launch the app under.</span></span> <span data-ttu-id="e00c7-197">如果 `hosted` 是 `true`，则必须设置它。</span><span class="sxs-lookup"><span data-stu-id="e00c7-197">Must be set if `hosted` is `true`.</span></span> |
| `env`     | <span data-ttu-id="e00c7-198">要提供给已启动进程的环境变量。</span><span class="sxs-lookup"><span data-stu-id="e00c7-198">The environment variables to provide to the launched process.</span></span> <span data-ttu-id="e00c7-199">仅当 `hosted` 设置为 `true` 时才适用。</span><span class="sxs-lookup"><span data-stu-id="e00c7-199">Only applicable if `hosted` is set to `true`.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="e00c7-200">启动配置示例</span><span class="sxs-lookup"><span data-stu-id="e00c7-200">Example launch configurations</span></span>

#### <a name="launch-and-debug-a-standalone-no-locblazor-webassembly-app"></a><span data-ttu-id="e00c7-201">启动并调试独立 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="e00c7-201">Launch and debug a standalone Blazor WebAssembly app</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="e00c7-202">在指定的 URL 附加到正在运行的应用</span><span class="sxs-lookup"><span data-stu-id="e00c7-202">Attach to a running app at a specified URL</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-no-locblazor-webassembly-app-with-microsoft-edge"></a><span data-ttu-id="e00c7-203">使用 Microsoft Edge 启动并调试托管 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="e00c7-203">Launch and debug a hosted Blazor WebAssembly app with Microsoft Edge</span></span>

<span data-ttu-id="e00c7-204">浏览器默认配置为 Google Chrome。</span><span class="sxs-lookup"><span data-stu-id="e00c7-204">Browser configuration defaults to Google Chrome.</span></span> <span data-ttu-id="e00c7-205">使用 Microsoft Edge 进行调试时，将 `browser` 设置为 `edge`。</span><span class="sxs-lookup"><span data-stu-id="e00c7-205">When using Microsoft Edge for debugging, set `browser` to `edge`.</span></span> <span data-ttu-id="e00c7-206">若要使用 Google Chrome，要么不要设置 `browser` 选项，要么将选项值设置为 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="e00c7-206">To use Google Chrome, either don't set the `browser` option or set the option's value to `chrome`.</span></span>

```json
{
  "name": "Launch and Debug Hosted Blazor WebAssembly App",
  "type": "blazorwasm",
  "request": "launch",
  "hosted": true,
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}/Server",
  "browser": "edge"
}
```

<span data-ttu-id="e00c7-207">在前面的示例中，`MyHostedApp.Server.dll` 是“服务器”应用的程序集。</span><span class="sxs-lookup"><span data-stu-id="e00c7-207">In the preceding example, `MyHostedApp.Server.dll` is the *Server* app's assembly.</span></span> <span data-ttu-id="e00c7-208">在“解决方案”文件夹中，“`.vscode`”文件夹位于“`Client`”、“`Server`”和“`Shared`”文件夹旁边。</span><span class="sxs-lookup"><span data-stu-id="e00c7-208">The `.vscode` folder is located in the solution's folder next to the `Client`, `Server`, and `Shared` folders.</span></span>

## <a name="debug-in-the-browser"></a><span data-ttu-id="e00c7-209">在浏览器中调试</span><span class="sxs-lookup"><span data-stu-id="e00c7-209">Debug in the browser</span></span>

1. <span data-ttu-id="e00c7-210">在开发环境中运行该应用的调试版本。</span><span class="sxs-lookup"><span data-stu-id="e00c7-210">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="e00c7-211">启动浏览器并导航到应用程序的 URL（例如 `https://localhost:5001`）。</span><span class="sxs-lookup"><span data-stu-id="e00c7-211">Launch a browser and navigate to the app's URL (for example, `https://localhost:5001`).</span></span>

1. <span data-ttu-id="e00c7-212">在浏览器中，尝试通过按 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd> 启动远程调试。</span><span class="sxs-lookup"><span data-stu-id="e00c7-212">In the browser, attempt to commence remote debugging by pressing <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

   <span data-ttu-id="e00c7-213">浏览器必须在已启用远程调试的情况下运行，而这并不是默认设置。</span><span class="sxs-lookup"><span data-stu-id="e00c7-213">The browser must be running with remote debugging enabled, which isn't the default.</span></span> <span data-ttu-id="e00c7-214">如果远程调试处于禁用状态，将呈现“无法找到可调试的浏览器选项卡”错误页，并且其中包含关于在调试端口打开的情况下启动浏览器的说明。</span><span class="sxs-lookup"><span data-stu-id="e00c7-214">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is rendered with instructions for launching the browser with the debugging port open.</span></span> <span data-ttu-id="e00c7-215">按照适用于你的浏览器的说明操作，接下来将打开一个新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="e00c7-215">Follow the instructions for your browser, which opens a new browser window.</span></span> <span data-ttu-id="e00c7-216">关闭上一个浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="e00c7-216">Close the previous browser window.</span></span>

1. <span data-ttu-id="e00c7-217">在启用远程调试的情况下运行浏览器后，按调试键盘快捷方式 (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) 会打开新的调试程序标签页。</span><span class="sxs-lookup"><span data-stu-id="e00c7-217">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut (<kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>) opens a new debugger tab.</span></span>

1. <span data-ttu-id="e00c7-218">片刻后，“源”选项卡显示 `file://` 节点中应用的 .NET 程序集的列表。</span><span class="sxs-lookup"><span data-stu-id="e00c7-218">After a moment, the **Sources** tab shows a list of the app's .NET assemblies within the `file://` node.</span></span>

1. <span data-ttu-id="e00c7-219">在组件代码（`.razor` 文件）和 C# 代码文件 (`.cs`) 中，当代码执行时将命中你设置的断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-219">In component code (`.razor` files) and C# code files (`.cs`), breakpoints that you set are hit when code executes.</span></span> <span data-ttu-id="e00c7-220">命中断点后，正常单步执行代码 (<kbd>F10</kbd>) 或恢复代码执行 (<kbd>F8</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="e00c7-220">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

<span data-ttu-id="e00c7-221">Blazor 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。</span><span class="sxs-lookup"><span data-stu-id="e00c7-221">Blazor provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="e00c7-222">按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。</span><span class="sxs-lookup"><span data-stu-id="e00c7-222">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="e00c7-223">代理连接到要调试的浏览器窗口（因此需要启用远程调试）。</span><span class="sxs-lookup"><span data-stu-id="e00c7-223">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="e00c7-224">浏览器源映射</span><span class="sxs-lookup"><span data-stu-id="e00c7-224">Browser source maps</span></span>

<span data-ttu-id="e00c7-225">浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。</span><span class="sxs-lookup"><span data-stu-id="e00c7-225">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="e00c7-226">但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="e00c7-226">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="e00c7-227">相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。</span><span class="sxs-lookup"><span data-stu-id="e00c7-227">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="e00c7-228">疑难解答</span><span class="sxs-lookup"><span data-stu-id="e00c7-228">Troubleshoot</span></span>

<span data-ttu-id="e00c7-229">如果遇到错误，以下提示可能有所帮助：</span><span class="sxs-lookup"><span data-stu-id="e00c7-229">If you're running into errors, the following tips may help:</span></span>

* <span data-ttu-id="e00c7-230">在“调试程序”选项卡中，在浏览器中打开开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="e00c7-230">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="e00c7-231">在控制台中，执行 `localStorage.clear()` 以删除所有断点。</span><span class="sxs-lookup"><span data-stu-id="e00c7-231">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
* <span data-ttu-id="e00c7-232">确认你已安装并信任 ASP.NET Core HTTPS 开发证书。</span><span class="sxs-lookup"><span data-stu-id="e00c7-232">Confirm that you've installed and trusted the ASP.NET Core HTTPS development certificate.</span></span> <span data-ttu-id="e00c7-233">有关详细信息，请参阅 <xref:security/enforcing-ssl#troubleshoot-certificate-problems>。</span><span class="sxs-lookup"><span data-stu-id="e00c7-233">For more information, see <xref:security/enforcing-ssl#troubleshoot-certificate-problems>.</span></span>
* <span data-ttu-id="e00c7-234">Visual Studio 要求在“工具” > “选项” > “调试” > “常规”中选择“对 ASP.NET 启用 JavaScript 调试(Chrome、Edge 和 IE)”选项。</span><span class="sxs-lookup"><span data-stu-id="e00c7-234">Visual Studio requires the **Enable JavaScript debugging for ASP.NET (Chrome, Edge and IE)** option in **Tools** > **Options** > **Debugging** > **General**.</span></span> <span data-ttu-id="e00c7-235">这是 Visual Studio 的默认设置。</span><span class="sxs-lookup"><span data-stu-id="e00c7-235">This is the default setting for Visual Studio.</span></span> <span data-ttu-id="e00c7-236">如果调试不起作用，请确认已选中该选项。</span><span class="sxs-lookup"><span data-stu-id="e00c7-236">If debugging isn't working, confirm that the option is selected.</span></span>
