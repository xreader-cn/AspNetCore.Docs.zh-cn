---
title: "title:'调试 ASP.NET Core Blazor WebAssembly' author: guardrex description:'了解如何调试 Blazor 应用。'"
author: guardrex
description: "monikerRange: '>= aspnetcore-3.1' ms.author: riande ms.custom: mvc ms.date:2020 年 5 月 31 日 no-loc:"
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
uid: blazor/debug
ms.openlocfilehash: 193dc656c2ee0154f0ae534bc00f8dc29bab3258
ms.sourcegitcommit: 2e9973cea5c36d0dd64d5e946bb776b8bb73a4b6
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/01/2020
ms.locfileid: "84239201"
---
# <a name="debug-aspnet-core-blazor-webassembly"></a><span data-ttu-id="7414d-103">'Blazor'</span><span class="sxs-lookup"><span data-stu-id="7414d-103">'Blazor'</span></span>

<span data-ttu-id="7414d-104">'Identity'</span><span class="sxs-lookup"><span data-stu-id="7414d-104">'Identity'</span></span>

<span data-ttu-id="7414d-105">'Let's Encrypt'</span><span class="sxs-lookup"><span data-stu-id="7414d-105">'Let's Encrypt'</span></span> <span data-ttu-id="7414d-106">'Razor'</span><span class="sxs-lookup"><span data-stu-id="7414d-106">'Razor'</span></span>

<span data-ttu-id="7414d-107">'SignalR' uid: blazor/debug</span><span class="sxs-lookup"><span data-stu-id="7414d-107">'SignalR' uid: blazor/debug</span></span>

* <span data-ttu-id="7414d-108">调试 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7414d-108">Debug ASP.NET Core Blazor WebAssembly</span></span>
* [<span data-ttu-id="7414d-109">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="7414d-109">Daniel Roth</span></span>](https://github.com/danroth27)
* <span data-ttu-id="7414d-110">可以使用基于 Chromium 的浏览器 (Microsoft Edge/Chrome) 中的浏览器开发工具调试 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-110">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>
* <span data-ttu-id="7414d-111">也可以使用 Visual Studio 或 Visual Studio Code 调试应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-111">Alternatively, you can debug your app using Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="7414d-112">可用方案包括：</span><span class="sxs-lookup"><span data-stu-id="7414d-112">Available scenarios include:</span></span>
* <span data-ttu-id="7414d-113">设置和删除断点。</span><span class="sxs-lookup"><span data-stu-id="7414d-113">Set and remove breakpoints.</span></span>

<span data-ttu-id="7414d-114">使用 Visual Studio 和 Visual Studio Code 中的调试支持来运行应用（<kbd>F5</kbd> 支持）。</span><span class="sxs-lookup"><span data-stu-id="7414d-114">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>

* <span data-ttu-id="7414d-115">单步执行代码 (<kbd>F10</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="7414d-115">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="7414d-116">在浏览器中使用 <kbd>F8</kbd> 恢复代码执行，在 Visual Studio 或 Visual Studio Code 中使用 <kbd>F5</kbd> 恢复代码执行。</span><span class="sxs-lookup"><span data-stu-id="7414d-116">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="7414d-117">在“局部变量”视图中，观察局部变量的值。</span><span class="sxs-lookup"><span data-stu-id="7414d-117">In the *Locals* display, observe the values of local variables.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="7414d-118">查看调用堆栈，包括从 JavaScript 到 .NET 以及从 .NET 到 JavaScript 的调用链。</span><span class="sxs-lookup"><span data-stu-id="7414d-118">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="7414d-119">目前，无法执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7414d-119">For now, you *can't*:</span></span>

* <span data-ttu-id="7414d-120">出现未经处理的异常时中断。</span><span class="sxs-lookup"><span data-stu-id="7414d-120">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="7414d-121">在应用启动期间命中断点。</span><span class="sxs-lookup"><span data-stu-id="7414d-121">Hit breakpoints during app startup.</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="7414d-122">在即将发布的版本中，我们将继续改进调试体验。</span><span class="sxs-lookup"><span data-stu-id="7414d-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

<span data-ttu-id="7414d-123">先决条件</span><span class="sxs-lookup"><span data-stu-id="7414d-123">Prerequisites</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="7414d-124">调试需要使用以下浏览器之一：</span><span class="sxs-lookup"><span data-stu-id="7414d-124">Debugging requires either of the following browsers:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="7414d-125">Microsoft Edge（版本 80 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="7414d-125">Microsoft Edge (version 80 or later)</span></span>

* <span data-ttu-id="7414d-126">Google Chrome（版本 70 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="7414d-126">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="7414d-127">在 Visual Studio 和 Visual Studio Code 中启用调试</span><span class="sxs-lookup"><span data-stu-id="7414d-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="7414d-128">若要为现有 Blazor WebAssembly 应用启用调试，请更新启动项目中的 launchSettings.json 文件，使每个启动配置文件包含以下 `inspectUri` 属性：</span><span class="sxs-lookup"><span data-stu-id="7414d-128">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

## <a name="visual-studio"></a><span data-ttu-id="7414d-129">更新后，launchSettings.json 文件应类似于以下示例：</span><span class="sxs-lookup"><span data-stu-id="7414d-129">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

<span data-ttu-id="7414d-130">`inspectUri` 属性具有以下作用：</span><span class="sxs-lookup"><span data-stu-id="7414d-130">The `inspectUri` property:</span></span>

1. <span data-ttu-id="7414d-131">使 IDE 能够检测到该应用为 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-131">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="7414d-132">指示脚本调试基础结构通过 Blazor 的调试代理连接到浏览器。</span><span class="sxs-lookup"><span data-stu-id="7414d-132">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>
1. <span data-ttu-id="7414d-133">已启动的浏览器 (`browserInspectUri`) 上 WebSocket 协议 (`wsProtocol`)、主机 (`url.hostname`)、端口 (`url.port`) 和检查器 URI 的占位符值由框架提供。</span><span class="sxs-lookup"><span data-stu-id="7414d-133">The placeholder values for the WebSockets protocol (`wsProtocol`), host (`url.hostname`), port (`url.port`), and inspector URI on the launched browser (`browserInspectUri`) are provided by the framework.</span></span>
1. <span data-ttu-id="7414d-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="7414d-134">Visual Studio</span></span>

   ![要在 Visual Studio 中调试 Blazor WebAssembly 应用，请按以下步骤执行：](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="7414d-136">创建一个由 ASP.NET Core 托管的新 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-136">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>

   ![按 <kbd>F5</kbd> 在调试器中运行应用。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="7414d-138">在 `IncrementCount` 方法的 Counter.razor 中设置一个断点。</span><span class="sxs-lookup"><span data-stu-id="7414d-138">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>

<span data-ttu-id="7414d-139">浏览到“计数器”选项卡，选择该按钮以命中断点：</span><span class="sxs-lookup"><span data-stu-id="7414d-139">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

1. <span data-ttu-id="7414d-140">调试计数器</span><span class="sxs-lookup"><span data-stu-id="7414d-140">Debug Counter</span></span>
1. <span data-ttu-id="7414d-141">查看局部变量窗口中 `currentCount` 字段的值：</span><span class="sxs-lookup"><span data-stu-id="7414d-141">Check out the value of the `currentCount` field in the locals window:</span></span>
1. <span data-ttu-id="7414d-142">查看局部变量</span><span class="sxs-lookup"><span data-stu-id="7414d-142">View locals</span></span>

   ![按 <kbd>F5</kbd> 继续执行。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="7414d-144">调试 Blazor WebAssembly 应用时，还可以调试服务器代码：</span><span class="sxs-lookup"><span data-stu-id="7414d-144">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

   ![在 <xref:Microsoft.AspNetCore.Components.ComponentBase.OnInitializedAsync%2A> 的 FetchData.razor 页面中设置一个断点。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="7414d-146">在 `Get` 操作方法的 `WeatherForecastController` 中设置一个断点。</span><span class="sxs-lookup"><span data-stu-id="7414d-146">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>

<a id="vscode"></a>

## <a name="visual-studio-code"></a><span data-ttu-id="7414d-147">浏览到“提取数据”选项卡，在 `FetchData` 组件中的首个断点向服务器发出 HTTP 请求前命中断点：</span><span class="sxs-lookup"><span data-stu-id="7414d-147">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

<span data-ttu-id="7414d-148">调试提取数据</span><span class="sxs-lookup"><span data-stu-id="7414d-148">Debug Fetch Data</span></span>
 
<span data-ttu-id="7414d-149">按 <kbd>F5</kbd> 继续执行，然后在服务器上命中 `WeatherForecastController` 中的断点：</span><span class="sxs-lookup"><span data-stu-id="7414d-149">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

![调试服务器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

![再次按 <kbd>F5</kbd> 继续执行，查看是否呈现天气预报表。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

### <a name="debug-standalone-blazor-webassembly"></a><span data-ttu-id="7414d-152">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="7414d-152">Visual Studio Code</span></span>

1. <span data-ttu-id="7414d-153">要在 Visual Studio Code 中调试 Blazor WebAssembly 应用，请按以下步骤执行：</span><span class="sxs-lookup"><span data-stu-id="7414d-153">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>

   <span data-ttu-id="7414d-154">安装 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)和 [JavaScript 调试程序（Nightly 版）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)，并将 `debug.javascript.usePreview` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="7414d-154">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>
   
   * <span data-ttu-id="7414d-155">扩展</span><span class="sxs-lookup"><span data-stu-id="7414d-155">Extensions</span></span>
   * <span data-ttu-id="7414d-156">JS 预览版调试程序</span><span class="sxs-lookup"><span data-stu-id="7414d-156">JS preview debugger</span></span>
   * <span data-ttu-id="7414d-157">调试独立 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7414d-157">Debug standalone Blazor WebAssembly</span></span>

   ![在 VS Code 中打开独立 Blazor WebAssembly 应用。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

1. <span data-ttu-id="7414d-159">如果收到下图所示的通知，指示还需要其他设置才能启用调试，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="7414d-159">If you receive the following notification that additional setup is required to enable debugging:</span></span>

1. <span data-ttu-id="7414d-160">确认已安装正确的扩展。</span><span class="sxs-lookup"><span data-stu-id="7414d-160">Confirm that you have the correct extensions installed.</span></span>

   ![确认已启用 JavaScript 预览调试。](index/_static/blazor-vscode-debugtypes.png)

1. <span data-ttu-id="7414d-162">重载窗口。</span><span class="sxs-lookup"><span data-stu-id="7414d-162">Reload the window.</span></span>

1. <span data-ttu-id="7414d-163">其他设置要求</span><span class="sxs-lookup"><span data-stu-id="7414d-163">Additional setup required</span></span>

   ![使用 <kbd>F5</kbd> 键盘快捷方式或菜单项启动调试。](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

### <a name="debug-hosted-blazor-webassembly"></a><span data-ttu-id="7414d-165">出现提示时，选择“Blazor WebAssembly 调试”选项以启动调试。</span><span class="sxs-lookup"><span data-stu-id="7414d-165">When prompted, select the **Blazor WebAssembly Debug** option to start debugging.</span></span>

1. <span data-ttu-id="7414d-166">可用调试选项列表</span><span class="sxs-lookup"><span data-stu-id="7414d-166">List of available debug options</span></span>

1. <span data-ttu-id="7414d-167">此时会启动独立应用，并打开调试浏览器。</span><span class="sxs-lookup"><span data-stu-id="7414d-167">The standalone app is launched, and a debugging browser is opened.</span></span> <span data-ttu-id="7414d-168">在 `Counter` 组件的 `IncrementCount` 方法中设置一个断点，然后选择该按钮命中该断点。</span><span class="sxs-lookup"><span data-stu-id="7414d-168">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code 中的调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="7414d-170">调试托管的 Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="7414d-170">Debug hosted Blazor WebAssembly</span></span>

<span data-ttu-id="7414d-171">在 VS Code 中打开托管的 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-171">Open the hosted Blazor WebAssembly app in VS Code.</span></span>

### <a name="attach-to-an-existing-debugging-session"></a><span data-ttu-id="7414d-172">如果没有为项目设置启动配置，将显示以下通知。</span><span class="sxs-lookup"><span data-stu-id="7414d-172">If there's no launch configuration set for the project, the following notification appears.</span></span>

<span data-ttu-id="7414d-173">选择 **“是”** 。</span><span class="sxs-lookup"><span data-stu-id="7414d-173">Select **Yes**.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach to Existing Blazor WebAssembly Application"
}
```

> [!NOTE]
> <span data-ttu-id="7414d-174">添加所需资产</span><span class="sxs-lookup"><span data-stu-id="7414d-174">Add required assets</span></span> <span data-ttu-id="7414d-175">在选择窗口中，选择托管解决方案中的“服务器”项目。</span><span class="sxs-lookup"><span data-stu-id="7414d-175">In the selection window, select the *Server* project within the hosted solution.</span></span>

### <a name="launch-configuration-options"></a><span data-ttu-id="7414d-176">这会生成一个 launch.json 文件，其中包含用于启动调试器的启动配置。</span><span class="sxs-lookup"><span data-stu-id="7414d-176">A *launch.json* file is generated with the launch configuration for launching the debugger.</span></span>

<span data-ttu-id="7414d-177">附加到现有的调试会话</span><span class="sxs-lookup"><span data-stu-id="7414d-177">Attach to an existing debugging session</span></span>

| <span data-ttu-id="7414d-178">若要附加到正在运行的 Blazor 应用，请创建一个具有以下配置的 launch.json 文件：</span><span class="sxs-lookup"><span data-stu-id="7414d-178">To attach to a running Blazor app, create a *launch.json* file with the following configuration:</span></span>    | <span data-ttu-id="7414d-179">只有独立应用才支持附加到调试会话。</span><span class="sxs-lookup"><span data-stu-id="7414d-179">Attaching to a debugging session is only supported for standalone apps.</span></span> |
| --------- | ----------- |
| `request` | <span data-ttu-id="7414d-180">若要使用完整堆栈调试，必须从 VS Code 启动应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-180">To use full-stack debugging, you must launch the app from VS Code.</span></span> |
| `url`     | <span data-ttu-id="7414d-181">启动配置选项</span><span class="sxs-lookup"><span data-stu-id="7414d-181">Launch configuration options</span></span> <span data-ttu-id="7414d-182">`blazorwasm` 调试类型支持以下启动配置选项。</span><span class="sxs-lookup"><span data-stu-id="7414d-182">The following launch configuration options are supported for the `blazorwasm` debug type.</span></span> |
| `browser` | <span data-ttu-id="7414d-183">选项</span><span class="sxs-lookup"><span data-stu-id="7414d-183">Option</span></span> <span data-ttu-id="7414d-184">描述</span><span class="sxs-lookup"><span data-stu-id="7414d-184">Description</span></span> <span data-ttu-id="7414d-185">使用 `launch` 启动调试会话并将其附加到 Blazor WebAssembly 应用，或使用 `attach` 将调试会话附加到已运行的应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-185">Use `launch` to launch and attach a debugging session to a Blazor WebAssembly app or `attach` to attach a debugging session to an already-running app.</span></span> |
| `trace`   | <span data-ttu-id="7414d-186">调试时要在浏览器中打开的 URL。</span><span class="sxs-lookup"><span data-stu-id="7414d-186">The URL to open in the browser when debugging.</span></span> <span data-ttu-id="7414d-187">默认为 `https://localhost:5001`。</span><span class="sxs-lookup"><span data-stu-id="7414d-187">Defaults to `https://localhost:5001`.</span></span> |
| `hosted`  | <span data-ttu-id="7414d-188">要为调试会话启动的浏览器。</span><span class="sxs-lookup"><span data-stu-id="7414d-188">The browser to launch for the debugging session.</span></span> |
| `webRoot` | <span data-ttu-id="7414d-189">设置为 `edge` 或 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="7414d-189">Set to `edge` or `chrome`.</span></span> <span data-ttu-id="7414d-190">默认为 `chrome`。</span><span class="sxs-lookup"><span data-stu-id="7414d-190">Defaults to `chrome`.</span></span> |
| `timeout` | <span data-ttu-id="7414d-191">用于从 JS 调试程序生成日志。</span><span class="sxs-lookup"><span data-stu-id="7414d-191">Used to generate logs from the JS debugger.</span></span> <span data-ttu-id="7414d-192">设置为 `true` 即可生成日志。</span><span class="sxs-lookup"><span data-stu-id="7414d-192">Set to `true` to generate logs.</span></span> |
| `program` | <span data-ttu-id="7414d-193">如果要启动和调试托管的 Blazor WebAssembly 应用，则必须设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="7414d-193">Must be set to `true` if launching and debugging a hosted Blazor WebAssembly app.</span></span> <span data-ttu-id="7414d-194">指定 Web 服务器的绝对路径。</span><span class="sxs-lookup"><span data-stu-id="7414d-194">Specifies the absolute path of the web server.</span></span> |
| `cwd`     | <span data-ttu-id="7414d-195">如果应用是从子路由中提供的，则应设置它。</span><span class="sxs-lookup"><span data-stu-id="7414d-195">Should be set if an app is served from a sub-route.</span></span> <span data-ttu-id="7414d-196">等待调试会话附加完成的毫秒数。</span><span class="sxs-lookup"><span data-stu-id="7414d-196">The number of milliseconds to wait for the debugging session to attach.</span></span> |
| `env`     | <span data-ttu-id="7414d-197">默认值为 30,000 毫秒（30 秒）。</span><span class="sxs-lookup"><span data-stu-id="7414d-197">Defaults to 30,000 milliseconds (30 seconds).</span></span> <span data-ttu-id="7414d-198">为运行托管应用的服务器而对可执行文件进行的引用。</span><span class="sxs-lookup"><span data-stu-id="7414d-198">A reference to the executable to run the server of the hosted app.</span></span> |

### <a name="example-launch-configurations"></a><span data-ttu-id="7414d-199">如果 `hosted` 是 `true`，则必须设置它。</span><span class="sxs-lookup"><span data-stu-id="7414d-199">Must be set if `hosted` is `true`.</span></span>

#### <a name="launch-and-debug-a-standalone-blazor-webassembly-app"></a><span data-ttu-id="7414d-200">要在其中启动应用的工作目录。</span><span class="sxs-lookup"><span data-stu-id="7414d-200">The working directory to launch the app under.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug"
}
```

#### <a name="attach-to-a-running-app-at-a-specified-url"></a><span data-ttu-id="7414d-201">如果 `hosted` 是 `true`，则必须设置它。</span><span class="sxs-lookup"><span data-stu-id="7414d-201">Must be set if `hosted` is `true`.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "attach",
  "name": "Attach and Debug",
  "url": "http://localhost:5000"
}
```

#### <a name="launch-and-debug-a-hosted-blazor-webassembly-app"></a><span data-ttu-id="7414d-202">要提供给已启动进程的环境变量。</span><span class="sxs-lookup"><span data-stu-id="7414d-202">The environment variables to provide to the launched process.</span></span>

```json
{
  "type": "blazorwasm",
  "request": "launch",
  "name": "Launch and Debug Hosted App",
  "program": "${workspaceFolder}/Server/bin/Debug/netcoreapp3.1/MyHostedApp.Server.dll",
  "cwd": "${workspaceFolder}"
}
```

## <a name="debug-in-the-browser"></a><span data-ttu-id="7414d-203">仅当 `hosted` 设置为 `true` 时才适用。</span><span class="sxs-lookup"><span data-stu-id="7414d-203">Only applicable if `hosted` is set to `true`.</span></span>

1. <span data-ttu-id="7414d-204">启动配置示例</span><span class="sxs-lookup"><span data-stu-id="7414d-204">Example launch configurations</span></span>

1. <span data-ttu-id="7414d-205">启动并调试独立 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="7414d-205">Launch and debug a standalone Blazor WebAssembly app</span></span>

1. <span data-ttu-id="7414d-206">在指定的 URL 附加到正在运行的应用</span><span class="sxs-lookup"><span data-stu-id="7414d-206">Attach to a running app at a specified URL</span></span> <span data-ttu-id="7414d-207">启动并调试托管的 Blazor WebAssembly 应用</span><span class="sxs-lookup"><span data-stu-id="7414d-207">Launch and debug a hosted Blazor WebAssembly app</span></span> <span data-ttu-id="7414d-208">在浏览器中调试</span><span class="sxs-lookup"><span data-stu-id="7414d-208">Debug in the browser</span></span> <span data-ttu-id="7414d-209">在开发环境中运行该应用的调试版本。</span><span class="sxs-lookup"><span data-stu-id="7414d-209">Run a Debug build of the app in the Development environment.</span></span>

<span data-ttu-id="7414d-210">按 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>。</span><span class="sxs-lookup"><span data-stu-id="7414d-210">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span> <span data-ttu-id="7414d-211">浏览器必须在启用远程调试的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="7414d-211">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="7414d-212">如果远程调试未启用，则系统会生成“无法找到可调试的浏览器标签页”错误页面。</span><span class="sxs-lookup"><span data-stu-id="7414d-212">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="7414d-213">该错误页面包含有关一些说明，指示应在调试端口处于打开状态的情况下运行浏览器以便 Blazor 调试代理可以连接到应用。</span><span class="sxs-lookup"><span data-stu-id="7414d-213">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span>

<span data-ttu-id="7414d-214">请关闭所有浏览器实例，并按照说明重启浏览器。</span><span class="sxs-lookup"><span data-stu-id="7414d-214">*Close all browser instances* and restart the browser as instructed.</span></span> <span data-ttu-id="7414d-215">在启用远程调试的情况下运行浏览器后，按调试键盘快捷方式会打开新的调试程序标签页。片刻后，“源”选项卡显示应用中的 .NET 程序集的列表。</span><span class="sxs-lookup"><span data-stu-id="7414d-215">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="7414d-216">展开每个程序集并找到可用于调试的 *.cs*/ *.razor* 源文件。</span><span class="sxs-lookup"><span data-stu-id="7414d-216">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="7414d-217">设置断点，然后切换回应用的选项卡，断点在代码执行时被命中。</span><span class="sxs-lookup"><span data-stu-id="7414d-217">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span>

<span data-ttu-id="7414d-218">命中断点后，正常单步执行代码 (<kbd>F10</kbd>) 或恢复代码执行 (<kbd>F8</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="7414d-218">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span> Blazor<span data-ttu-id="7414d-219"> 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。</span><span class="sxs-lookup"><span data-stu-id="7414d-219"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="7414d-220">按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。</span><span class="sxs-lookup"><span data-stu-id="7414d-220">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="7414d-221">代理连接到要调试的浏览器窗口（因此需要启用远程调试）。</span><span class="sxs-lookup"><span data-stu-id="7414d-221">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

<span data-ttu-id="7414d-222">浏览器源映射</span><span class="sxs-lookup"><span data-stu-id="7414d-222">Browser source maps</span></span>

* <span data-ttu-id="7414d-223">浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。</span><span class="sxs-lookup"><span data-stu-id="7414d-223">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="7414d-224">但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="7414d-224">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span>
* <span data-ttu-id="7414d-225">相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。</span><span class="sxs-lookup"><span data-stu-id="7414d-225">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span> <span data-ttu-id="7414d-226">疑难解答</span><span class="sxs-lookup"><span data-stu-id="7414d-226">Troubleshoot</span></span>
