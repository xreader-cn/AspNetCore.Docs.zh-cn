---
title: 调试 ASP.NET Core Blazor WebAssembly
author: guardrex
description: 了解如何调试 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 03/26/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: eaa67d63f6d15249885d78d3de197ae53e73f072
ms.sourcegitcommit: f7886fd2e219db9d7ce27b16c0dc5901e658d64e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/06/2020
ms.locfileid: "80381873"
---
# <a name="debug-aspnet-core-opno-locblazor-webassembly"></a><span data-ttu-id="792c2-103">调试 ASP.NET Core Blazor WebAssembly</span><span class="sxs-lookup"><span data-stu-id="792c2-103">Debug ASP.NET Core Blazor WebAssembly</span></span>

[<span data-ttu-id="792c2-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="792c2-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="792c2-105">可以使用基于 Chromium 的浏览器 (Microsoft Edge/Chrome) 中的浏览器开发工具调试 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-105">Blazor WebAssembly apps can be debugged using the browser dev tools in Chromium-based browsers (Edge/Chrome).</span></span>  <span data-ttu-id="792c2-106">也可以使用 Visual Studio 或 Visual Studio Code 调试应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-106">Alternatively you can debug your app using Visual Studio or Visual Studio Code.</span></span>

<span data-ttu-id="792c2-107">可用方案包括：</span><span class="sxs-lookup"><span data-stu-id="792c2-107">Available scenarios include:</span></span>

* <span data-ttu-id="792c2-108">设置和删除断点。</span><span class="sxs-lookup"><span data-stu-id="792c2-108">Set and remove breakpoints.</span></span>
* <span data-ttu-id="792c2-109">使用 Visual Studio 和 Visual Studio Code 中的调试支持来运行应用（<kbd>F5</kbd> 支持）。</span><span class="sxs-lookup"><span data-stu-id="792c2-109">Run the app with debugging support in Visual Studio and Visual Studio Code (<kbd>F5</kbd> support).</span></span>
* <span data-ttu-id="792c2-110">单步执行代码 (<kbd>F10</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="792c2-110">Single-step (<kbd>F10</kbd>) through the code.</span></span>
* <span data-ttu-id="792c2-111">在浏览器中使用 <kbd>F8</kbd> 恢复代码执行，在 Visual Studio 或 Visual Studio Code 中使用 <kbd>F5</kbd> 恢复代码执行。</span><span class="sxs-lookup"><span data-stu-id="792c2-111">Resume code execution with <kbd>F8</kbd> in a browser or <kbd>F5</kbd> in Visual Studio or Visual Studio Code.</span></span>
* <span data-ttu-id="792c2-112">在“局部变量”视图中，观察局部变量的值  。</span><span class="sxs-lookup"><span data-stu-id="792c2-112">In the *Locals* display, observe the values of local variables.</span></span>
* <span data-ttu-id="792c2-113">查看调用堆栈，包括从 JavaScript 到 .NET 以及从 .NET 到 JavaScript 的调用链。</span><span class="sxs-lookup"><span data-stu-id="792c2-113">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="792c2-114">目前，无法执行以下操作  ：</span><span class="sxs-lookup"><span data-stu-id="792c2-114">For now, you *can't*:</span></span>

* <span data-ttu-id="792c2-115">检查数组。</span><span class="sxs-lookup"><span data-stu-id="792c2-115">Inspect arrays.</span></span>
* <span data-ttu-id="792c2-116">悬停以检查成员。</span><span class="sxs-lookup"><span data-stu-id="792c2-116">Hover to inspect members.</span></span>
* <span data-ttu-id="792c2-117">逐步调试托管代码。</span><span class="sxs-lookup"><span data-stu-id="792c2-117">Step debug into or out of managed code.</span></span>
* <span data-ttu-id="792c2-118">对检查值类型提供完全支持。</span><span class="sxs-lookup"><span data-stu-id="792c2-118">Have full support for inspecting value types.</span></span>
* <span data-ttu-id="792c2-119">出现未经处理的异常时中断。</span><span class="sxs-lookup"><span data-stu-id="792c2-119">Break on unhandled exceptions.</span></span>
* <span data-ttu-id="792c2-120">在应用启动期间命中断点。</span><span class="sxs-lookup"><span data-stu-id="792c2-120">Hit breakpoints during app startup.</span></span>
* <span data-ttu-id="792c2-121">使用服务工作进程调试应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-121">Debug an app with a service worker.</span></span>

<span data-ttu-id="792c2-122">在即将发布的版本中，我们将继续改进调试体验。</span><span class="sxs-lookup"><span data-stu-id="792c2-122">We will continue to improve the debugging experience in upcoming releases.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="792c2-123">先决条件</span><span class="sxs-lookup"><span data-stu-id="792c2-123">Prerequisites</span></span>

<span data-ttu-id="792c2-124">调试需要使用以下浏览器之一：</span><span class="sxs-lookup"><span data-stu-id="792c2-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="792c2-125">Microsoft Edge（版本 80 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="792c2-125">Microsoft Edge (version 80 or later)</span></span>
* <span data-ttu-id="792c2-126">Google Chrome（版本 70 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="792c2-126">Google Chrome (version 70 or later)</span></span>

## <a name="enable-debugging-for-visual-studio-and-visual-studio-code"></a><span data-ttu-id="792c2-127">在 Visual Studio 和 Visual Studio Code 中启用调试</span><span class="sxs-lookup"><span data-stu-id="792c2-127">Enable debugging for Visual Studio and Visual Studio Code</span></span>

<span data-ttu-id="792c2-128">对于使用 ASP.NET Core 3.2 预览版 3 或更高版本 Blazor WebAssembly 项目模板创建的新项目，调试功能已自动启用。</span><span class="sxs-lookup"><span data-stu-id="792c2-128">Debugging is enabled automatically for new projects that are created using the ASP.NET Core 3.2 Preview 3 or later Blazor WebAssembly project template.</span></span>

<span data-ttu-id="792c2-129">若要为现有 Blazor WebAssembly 应用启用调试，请更新启动项目中的 launchSettings.json 文件，使每个启动配置文件包含以下 `inspectUri` 属性  ：</span><span class="sxs-lookup"><span data-stu-id="792c2-129">To enable debugging for an existing Blazor WebAssembly app, update the *launchSettings.json* file in the startup project to include the following `inspectUri` property in each launch profile:</span></span>

```json
"inspectUri": "{wsProtocol}://{url.hostname}:{url.port}/_framework/debug/ws-proxy?browser={browserInspectUri}"
```

<span data-ttu-id="792c2-130">更新后，launchSettings.json 文件应类似于以下示例  ：</span><span class="sxs-lookup"><span data-stu-id="792c2-130">Once updated, the *launchSettings.json* file should look similar to the following example:</span></span>

[!code-json[](debug/launchSettings.json?highlight=14,22)]

<span data-ttu-id="792c2-131">`inspectUri` 属性具有以下作用：</span><span class="sxs-lookup"><span data-stu-id="792c2-131">The `inspectUri` property:</span></span>

* <span data-ttu-id="792c2-132">使 IDE 能够检测到该应用为 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-132">Enables the IDE to detect that the app is a Blazor WebAssembly app.</span></span>
* <span data-ttu-id="792c2-133">指示脚本调试基础结构通过 Blazor 的调试代理连接到浏览器。</span><span class="sxs-lookup"><span data-stu-id="792c2-133">Instructs the script debugging infrastructure to connect to the browser through Blazor's debugging proxy.</span></span>

## <a name="visual-studio"></a><span data-ttu-id="792c2-134">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="792c2-134">Visual Studio</span></span>

<span data-ttu-id="792c2-135">要在 Visual Studio 中调试 Blazor WebAssembly 应用，请按以下步骤执行：</span><span class="sxs-lookup"><span data-stu-id="792c2-135">To debug a Blazor WebAssembly app in Visual Studio:</span></span>

1. <span data-ttu-id="792c2-136">确保已[安装 Visual Studio 2019 16.6 的最新预览版本](https://visualstudio.com/preview)（预览版 2 或更高版本）。</span><span class="sxs-lookup"><span data-stu-id="792c2-136">Ensure you have [installed the latest preview release of Visual Studio 2019 16.6](https://visualstudio.com/preview) (Preview 2 or later).</span></span>
1. <span data-ttu-id="792c2-137">创建一个由 ASP.NET Core 托管的新 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-137">Create a new ASP.NET Core hosted Blazor WebAssembly app.</span></span>
1. <span data-ttu-id="792c2-138">按 <kbd>F5</kbd> 在调试器中运行应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-138">Press <kbd>F5</kbd> to run the app in the debugger.</span></span>
1. <span data-ttu-id="792c2-139">在 `IncrementCount` 方法的 Counter.razor 中设置一个断点  。</span><span class="sxs-lookup"><span data-stu-id="792c2-139">Set a breakpoint in *Counter.razor* in the `IncrementCount` method.</span></span>
1. <span data-ttu-id="792c2-140">浏览到“计数器”选项卡，选择该按钮以命中断点  ：</span><span class="sxs-lookup"><span data-stu-id="792c2-140">Browse to the **Counter** tab and select the button to hit the breakpoint:</span></span>

   ![调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-counter.png)

1. <span data-ttu-id="792c2-142">查看局部变量窗口中 `currentCount` 字段的值：</span><span class="sxs-lookup"><span data-stu-id="792c2-142">Check out the value of the `currentCount` field in the locals window:</span></span>

   ![查看局部变量](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-locals.png)

1. <span data-ttu-id="792c2-144">按 <kbd>F5</kbd> 继续执行。</span><span class="sxs-lookup"><span data-stu-id="792c2-144">Press <kbd>F5</kbd> to continue execution.</span></span>

<span data-ttu-id="792c2-145">调试 Blazor WebAssembly 应用时，还可以调试服务器代码：</span><span class="sxs-lookup"><span data-stu-id="792c2-145">While debugging your Blazor WebAssembly app, you can also debug your server code:</span></span>

1. <span data-ttu-id="792c2-146">在 `OnInitializedAsync` 的 FetchData.razor 页面中设置一个断点  。</span><span class="sxs-lookup"><span data-stu-id="792c2-146">Set a breakpoint in the *FetchData.razor* page in `OnInitializedAsync`.</span></span>
1. <span data-ttu-id="792c2-147">在 `Get` 操作方法的 `WeatherForecastController` 中设置一个断点。</span><span class="sxs-lookup"><span data-stu-id="792c2-147">Set a breakpoint in the `WeatherForecastController` in the `Get` action method.</span></span>
1. <span data-ttu-id="792c2-148">浏览到“提取数据”选项卡，在 `FetchData` 组件中的首个断点向服务器发出 HTTP 请求前命中断点  ：</span><span class="sxs-lookup"><span data-stu-id="792c2-148">Browse to the **Fetch Data** tab to hit the first breakpoint in the `FetchData` component just before it issues an HTTP request to the server:</span></span>

   ![调试提取数据](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-fetch-data.png)

1. <span data-ttu-id="792c2-150">按 <kbd>F5</kbd> 继续执行，然后在服务器上命中 `WeatherForecastController` 中的断点：</span><span class="sxs-lookup"><span data-stu-id="792c2-150">Press <kbd>F5</kbd> to continue execution and then hit the breakpoint on the server in the `WeatherForecastController`:</span></span>

   ![调试服务器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vs-debug-server.png)

1. <span data-ttu-id="792c2-152">再次按 <kbd>F5</kbd> 继续执行，查看是否呈现天气预报表。</span><span class="sxs-lookup"><span data-stu-id="792c2-152">Press <kbd>F5</kbd> again to let execution continue and see the weather forecast table rendered.</span></span>

## <a name="visual-studio-code"></a><span data-ttu-id="792c2-153">Visual Studio Code</span><span class="sxs-lookup"><span data-stu-id="792c2-153">Visual Studio Code</span></span>

<span data-ttu-id="792c2-154">要在 Visual Studio Code 中调试 Blazor WebAssembly 应用，请按以下步骤执行：</span><span class="sxs-lookup"><span data-stu-id="792c2-154">To debug a Blazor WebAssembly app in Visual Studio Code:</span></span>
 
1. <span data-ttu-id="792c2-155">安装 [C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)和 [JavaScript 调试程序（Nightly 版）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)，并将 `debug.javascript.usePreview` 设置为 `true`。</span><span class="sxs-lookup"><span data-stu-id="792c2-155">Install the [C# extension](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) and the [JavaScript Debugger (Nightly)](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly) extension with `debug.javascript.usePreview` set to `true`.</span></span>

   ![扩展](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-extensions.png)

   ![JS 预览版调试程序](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-js-use-preview.png)

1. <span data-ttu-id="792c2-158">打开已启用调试的现有 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-158">Open an existing Blazor WebAssembly app with debugging enabled.</span></span>

   * <span data-ttu-id="792c2-159">如果收到以下通知告知你需要其他设置才能启用调试，请确认是否已安装正确的扩展并已启用 JavaScript 预览版调试，然后重新加载此窗口：</span><span class="sxs-lookup"><span data-stu-id="792c2-159">If you get the following notification that additional setup is required to enable debugging, confirm that you have the correct extensions installed and JavaScript preview debugging enabled and then reload the window:</span></span>

     ![需要其他设置](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-additional-setup.png)

   * <span data-ttu-id="792c2-161">系统显示一个通知，询问是否将所需资产添加到应用以进行构建和调试。</span><span class="sxs-lookup"><span data-stu-id="792c2-161">A notification offers to add the required assets to the app for building and debugging.</span></span> <span data-ttu-id="792c2-162">选择“是”  ：</span><span class="sxs-lookup"><span data-stu-id="792c2-162">Select **Yes**:</span></span>

     ![添加所需资产](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-required-assets.png)

1. <span data-ttu-id="792c2-164">按照以下两个步骤在调试程序中启动应用：</span><span class="sxs-lookup"><span data-stu-id="792c2-164">Starting the app in the debugger is a two-step process:</span></span>

   <span data-ttu-id="792c2-165">1\.</span><span class="sxs-lookup"><span data-stu-id="792c2-165">1\.</span></span> <span data-ttu-id="792c2-166">首先，使用“.NET Core 启动 (Blazor 独立版)”启动配置启动应用   。</span><span class="sxs-lookup"><span data-stu-id="792c2-166">**First**, start the app using the **.NET Core Launch (Blazor Standalone)** launch configuration.</span></span>

   <span data-ttu-id="792c2-167">2\.</span><span class="sxs-lookup"><span data-stu-id="792c2-167">2\.</span></span> <span data-ttu-id="792c2-168">应用启动后，使用“Chrome 中的 .NET Core 调试 Blazor Web 程序集”启动配置来启动浏览器（需要使用 Chrome）   。</span><span class="sxs-lookup"><span data-stu-id="792c2-168">**After the app has started**, start the browser using the **.NET Core Debug Blazor Web Assembly in Chrome** launch configuration (requires Chrome).</span></span> <span data-ttu-id="792c2-169">若要使用 Microsoft Edge 而不是 Chrome，请将 .vscode/launch.json 中的启动配置的 `type` 从 `pwa-chrome` 更改为 `pwa-msedge`  。</span><span class="sxs-lookup"><span data-stu-id="792c2-169">To use Edge instead of Chrome, change the `type` of the launch configuration in *.vscode/launch.json* from `pwa-chrome` to `pwa-msedge`.</span></span>

1. <span data-ttu-id="792c2-170">在 `Counter` 组件的 `IncrementCount` 方法中设置一个断点，然后选择该按钮命中该断点。</span><span class="sxs-lookup"><span data-stu-id="792c2-170">Set a breakpoint in the `IncrementCount` method in the `Counter` component and then select the button to hit the breakpoint:</span></span>

   ![VS Code 中的调试计数器](https://devblogs.microsoft.com/aspnet/wp-content/uploads/sites/16/2020/03/vscode-debug-counter.png)

## <a name="debug-in-the-browser"></a><span data-ttu-id="792c2-172">在浏览器中调试</span><span class="sxs-lookup"><span data-stu-id="792c2-172">Debug in the browser</span></span>

1. <span data-ttu-id="792c2-173">在开发环境中运行该应用的调试版本。</span><span class="sxs-lookup"><span data-stu-id="792c2-173">Run a Debug build of the app in the Development environment.</span></span>

1. <span data-ttu-id="792c2-174">按 <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>。</span><span class="sxs-lookup"><span data-stu-id="792c2-174">Press <kbd>Shift</kbd>+<kbd>Alt</kbd>+<kbd>D</kbd>.</span></span>

1. <span data-ttu-id="792c2-175">浏览器必须在启用远程调试的情况下运行。</span><span class="sxs-lookup"><span data-stu-id="792c2-175">The browser must be run with remote debugging enabled.</span></span> <span data-ttu-id="792c2-176">如果远程调试未启用，则系统会生成“无法找到可调试的浏览器标签页”错误页面  。</span><span class="sxs-lookup"><span data-stu-id="792c2-176">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated.</span></span> <span data-ttu-id="792c2-177">该错误页面包含有关一些说明，指示应在调试端口处于打开状态的情况下运行浏览器以便 Blazor 调试代理可以连接到应用。</span><span class="sxs-lookup"><span data-stu-id="792c2-177">The error page contains instructions for running the browser with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="792c2-178">请关闭所有浏览器实例，并按照说明重启浏览器  。</span><span class="sxs-lookup"><span data-stu-id="792c2-178">*Close all browser instances* and restart the browser as instructed.</span></span>

<span data-ttu-id="792c2-179">在启用远程调试的情况下运行浏览器后，按调试键盘快捷方式会打开新的调试程序标签页。片刻后，“源”选项卡显示应用中的 .NET 程序集的列表  。</span><span class="sxs-lookup"><span data-stu-id="792c2-179">Once the browser is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="792c2-180">展开每个程序集并找到可用于调试的 *.cs*/ *.razor* 源文件。</span><span class="sxs-lookup"><span data-stu-id="792c2-180">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="792c2-181">设置断点，然后切换回应用的选项卡，断点在代码执行时被命中。</span><span class="sxs-lookup"><span data-stu-id="792c2-181">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="792c2-182">命中断点后，正常单步执行代码 (<kbd>F10</kbd>) 或恢复代码执行 (<kbd>F8</kbd>)。</span><span class="sxs-lookup"><span data-stu-id="792c2-182">After a breakpoint is hit, single-step (<kbd>F10</kbd>) through the code or resume (<kbd>F8</kbd>) code execution normally.</span></span>

Blazor<span data-ttu-id="792c2-183"> 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。</span><span class="sxs-lookup"><span data-stu-id="792c2-183"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="792c2-184">按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。</span><span class="sxs-lookup"><span data-stu-id="792c2-184">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="792c2-185">代理连接到要调试的浏览器窗口（因此需要启用远程调试）。</span><span class="sxs-lookup"><span data-stu-id="792c2-185">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="792c2-186">浏览器源映射</span><span class="sxs-lookup"><span data-stu-id="792c2-186">Browser source maps</span></span>

<span data-ttu-id="792c2-187">浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。</span><span class="sxs-lookup"><span data-stu-id="792c2-187">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="792c2-188">但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="792c2-188">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="792c2-189">相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。</span><span class="sxs-lookup"><span data-stu-id="792c2-189">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="792c2-190">疑难解答</span><span class="sxs-lookup"><span data-stu-id="792c2-190">Troubleshoot</span></span>

<span data-ttu-id="792c2-191">如果遇到错误，以下提示可能有所帮助：</span><span class="sxs-lookup"><span data-stu-id="792c2-191">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="792c2-192">在“调试程序”选项卡中，在浏览器中打开开发人员工具  。</span><span class="sxs-lookup"><span data-stu-id="792c2-192">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="792c2-193">在控制台中，执行 `localStorage.clear()` 以删除所有断点。</span><span class="sxs-lookup"><span data-stu-id="792c2-193">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
