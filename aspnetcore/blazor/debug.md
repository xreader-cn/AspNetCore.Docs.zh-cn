---
title: 调试 ASP.NET Core Blazor
author: guardrex
description: 了解如何调试 Blazor 应用。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/16/2020
no-loc:
- Blazor
- SignalR
uid: blazor/debug
ms.openlocfilehash: 1b0035af48b82807a6ae14835a41a1ecbef06bb6
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648312"
---
# <a name="debug-aspnet-core-blazor"></a><span data-ttu-id="3ef57-103">调试 ASP.NET Core Blazor</span><span class="sxs-lookup"><span data-stu-id="3ef57-103">Debug ASP.NET Core Blazor</span></span>

[<span data-ttu-id="3ef57-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="3ef57-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="3ef57-105">对于在基于 Chromium 的浏览器 (Chrome/Edge) 中使用浏览器开发工具调试 Blazor WebAssembly 提供*早期*支持。</span><span class="sxs-lookup"><span data-stu-id="3ef57-105">*Early* support exists for debugging Blazor WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="3ef57-106">以下工作正在进行：</span><span class="sxs-lookup"><span data-stu-id="3ef57-106">Work is in progress to:</span></span>

* <span data-ttu-id="3ef57-107">在 Visual Studio 中完全启用调试。</span><span class="sxs-lookup"><span data-stu-id="3ef57-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="3ef57-108">在 Visual Studio Code 中启用调试。</span><span class="sxs-lookup"><span data-stu-id="3ef57-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="3ef57-109">调试程序功能有限。</span><span class="sxs-lookup"><span data-stu-id="3ef57-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="3ef57-110">可用方案包括：</span><span class="sxs-lookup"><span data-stu-id="3ef57-110">Available scenarios include:</span></span>

* <span data-ttu-id="3ef57-111">设置和删除断点。</span><span class="sxs-lookup"><span data-stu-id="3ef57-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="3ef57-112">单步 (`F10`) 执行代码或恢复 (`F8`) 代码执行。</span><span class="sxs-lookup"><span data-stu-id="3ef57-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="3ef57-113">在“局部变量”视图中，观察类型为 `int`、`string` 和 `bool` 的所有局部变量的值  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="3ef57-114">查看调用堆栈，包括从 JavaScript 到 .NET 以及从 .NET 到 JavaScript 的调用链。</span><span class="sxs-lookup"><span data-stu-id="3ef57-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="3ef57-115">你*不能*：</span><span class="sxs-lookup"><span data-stu-id="3ef57-115">You *can't*:</span></span>

* <span data-ttu-id="3ef57-116">观察类型不是 `int`、`string` 或 `bool` 的任何局部变量的值。</span><span class="sxs-lookup"><span data-stu-id="3ef57-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="3ef57-117">观察任何类属性或字段的值。</span><span class="sxs-lookup"><span data-stu-id="3ef57-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="3ef57-118">将鼠标悬停在变量上以查看其值。</span><span class="sxs-lookup"><span data-stu-id="3ef57-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="3ef57-119">在控制台中计算表达式。</span><span class="sxs-lookup"><span data-stu-id="3ef57-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="3ef57-120">单步执行异步调用。</span><span class="sxs-lookup"><span data-stu-id="3ef57-120">Step across async calls.</span></span>
* <span data-ttu-id="3ef57-121">执行其他大部分普通调试方案。</span><span class="sxs-lookup"><span data-stu-id="3ef57-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="3ef57-122">开发进一步的调试方案是工程团队的长期工作重点。</span><span class="sxs-lookup"><span data-stu-id="3ef57-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="3ef57-123">先决条件</span><span class="sxs-lookup"><span data-stu-id="3ef57-123">Prerequisites</span></span>

<span data-ttu-id="3ef57-124">调试需要使用以下浏览器之一：</span><span class="sxs-lookup"><span data-stu-id="3ef57-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="3ef57-125">Google Chrome（版本 70 或更高版本）</span><span class="sxs-lookup"><span data-stu-id="3ef57-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="3ef57-126">Microsoft Edge 预览版（[Edge 开发通道](https://www.microsoftedgeinsider.com)）</span><span class="sxs-lookup"><span data-stu-id="3ef57-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="3ef57-127">过程</span><span class="sxs-lookup"><span data-stu-id="3ef57-127">Procedure</span></span>

# <a name="visual-studio"></a>[<span data-ttu-id="3ef57-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="3ef57-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="3ef57-129">Visual Studio 中的调试支持处于早期开发阶段。</span><span class="sxs-lookup"><span data-stu-id="3ef57-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="3ef57-130">当前不支持 **F5** 调试。</span><span class="sxs-lookup"><span data-stu-id="3ef57-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="3ef57-131">在 `Debug` 配置中运行 Blazor WebAssembly 应用而不进行调试（**Ctrl**+**F5**，而不是 **F5**）。</span><span class="sxs-lookup"><span data-stu-id="3ef57-131">Run a Blazor WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="3ef57-132">打开应用的调试属性（“调试”菜单中的最后一项），然后复制 HTTP“应用 URL”   。</span><span class="sxs-lookup"><span data-stu-id="3ef57-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="3ef57-133">使用基于 Chromium 的浏览器（Edge Beta 或 Chrome）浏览到应用的 HTTP 地址（而非 HTTPS 地址）。</span><span class="sxs-lookup"><span data-stu-id="3ef57-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="3ef57-134">在浏览器窗口中将键盘焦点置于应用上，而不是开发人员工具面板上。</span><span class="sxs-lookup"><span data-stu-id="3ef57-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="3ef57-135">对于此过程，最好保持关闭开发人员工具面板。</span><span class="sxs-lookup"><span data-stu-id="3ef57-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="3ef57-136">调试开始后，可以重新打开开发人员工具面板。</span><span class="sxs-lookup"><span data-stu-id="3ef57-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="3ef57-137">选择以下特定于 Blazor 的键盘快捷方式：</span><span class="sxs-lookup"><span data-stu-id="3ef57-137">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="3ef57-138">在 Windows 上为 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="3ef57-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="3ef57-139">在 macOS 上为 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="3ef57-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="3ef57-140">如果收到“无法找到可调试的浏览器选项卡”，请参阅[启用远程调试](#enable-remote-debugging)  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="3ef57-141">启用远程调试后：</span><span class="sxs-lookup"><span data-stu-id="3ef57-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="3ef57-142">1\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-142">1\.</span></span> <span data-ttu-id="3ef57-143">新的浏览器窗口将打开。</span><span class="sxs-lookup"><span data-stu-id="3ef57-143">A new browser window opens.</span></span> <span data-ttu-id="3ef57-144">关闭之前的窗口。</span><span class="sxs-lookup"><span data-stu-id="3ef57-144">Close the prior window.</span></span>

   <span data-ttu-id="3ef57-145">2\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-145">2\.</span></span> <span data-ttu-id="3ef57-146">在浏览器窗口中将键盘焦点置于应用上。</span><span class="sxs-lookup"><span data-stu-id="3ef57-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="3ef57-147">3\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-147">3\.</span></span> <span data-ttu-id="3ef57-148">在新的浏览器窗口中选择特定于 Blazor 的键盘快捷方式：在 Windows 上为 `Shift+Alt+D`，在 macOS 上为 `Shift+Cmd+D`。</span><span class="sxs-lookup"><span data-stu-id="3ef57-148">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="3ef57-149">4\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-149">4\.</span></span> <span data-ttu-id="3ef57-150">“开发者工具”选项卡在浏览器中打开  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="3ef57-151">**在浏览器窗口中重新选择应用所在的选项卡。**</span><span class="sxs-lookup"><span data-stu-id="3ef57-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="3ef57-152">若要将应用附加到 Visual Studio，请参阅[在 Visual Studio 中附加到进程](#attach-to-process-in-visual-studio)部分。</span><span class="sxs-lookup"><span data-stu-id="3ef57-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-cli"></a>[<span data-ttu-id="3ef57-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="3ef57-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="3ef57-154">通过将 `--configuration Debug` 选项传递给 [dotnet run](/dotnet/core/tools/dotnet-run) 命令 `dotnet run --configuration Debug`，在 `Debug` 配置中运行 Blazor WebAssembly 应用。</span><span class="sxs-lookup"><span data-stu-id="3ef57-154">Run a Blazor WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="3ef57-155">在 shell 窗口中所示的 HTTP URL 中导航到应用。</span><span class="sxs-lookup"><span data-stu-id="3ef57-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="3ef57-156">将键盘焦点置于应用上，而不是开发人员工具面板上。</span><span class="sxs-lookup"><span data-stu-id="3ef57-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="3ef57-157">对于此过程，最好保持关闭开发人员工具面板。</span><span class="sxs-lookup"><span data-stu-id="3ef57-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="3ef57-158">调试开始后，可以重新打开开发人员工具面板。</span><span class="sxs-lookup"><span data-stu-id="3ef57-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="3ef57-159">选择以下特定于 Blazor 的键盘快捷方式：</span><span class="sxs-lookup"><span data-stu-id="3ef57-159">Select the following Blazor-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="3ef57-160">在 Windows 上为 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="3ef57-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="3ef57-161">在 macOS 上为 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="3ef57-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="3ef57-162">如果收到“无法找到可调试的浏览器选项卡”，请参阅[启用远程调试](#enable-remote-debugging)  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="3ef57-163">启用远程调试后：</span><span class="sxs-lookup"><span data-stu-id="3ef57-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="3ef57-164">1\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-164">1\.</span></span> <span data-ttu-id="3ef57-165">新的浏览器窗口将打开。</span><span class="sxs-lookup"><span data-stu-id="3ef57-165">A new browser window opens.</span></span> <span data-ttu-id="3ef57-166">关闭之前的窗口。</span><span class="sxs-lookup"><span data-stu-id="3ef57-166">Close the prior window.</span></span>

   <span data-ttu-id="3ef57-167">2\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-167">2\.</span></span> <span data-ttu-id="3ef57-168">在浏览器窗口中将键盘焦点置于应用上，而不是开发人员工具面板上。</span><span class="sxs-lookup"><span data-stu-id="3ef57-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="3ef57-169">3\.</span><span class="sxs-lookup"><span data-stu-id="3ef57-169">3\.</span></span> <span data-ttu-id="3ef57-170">在新的浏览器窗口中选择特定于 Blazor 的键盘快捷方式：在 Windows 上为 `Shift+Alt+D`，在 macOS 上为 `Shift+Cmd+D`。</span><span class="sxs-lookup"><span data-stu-id="3ef57-170">Select the Blazor-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="3ef57-171">启用远程调试</span><span class="sxs-lookup"><span data-stu-id="3ef57-171">Enable remote debugging</span></span>

<span data-ttu-id="3ef57-172">如果禁用远程调试，则 Chrome 会生成“无法找到可调试的浏览器选项卡”错误页  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="3ef57-173">错误页包含在打开调试端口的情况下运行 Chrome 的说明，以便 Blazor 调试代理可以连接到应用。</span><span class="sxs-lookup"><span data-stu-id="3ef57-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="3ef57-174">*关闭所有 Chrome 实例*，然后按照说明重启 Chrome。</span><span class="sxs-lookup"><span data-stu-id="3ef57-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="3ef57-175">调试应用</span><span class="sxs-lookup"><span data-stu-id="3ef57-175">Debug the app</span></span>

<span data-ttu-id="3ef57-176">Chrome 在启用远程调试的情况下运行后，调试键盘快捷方式会打开新的调试程序选项卡。片刻后，“源”选项卡显示应用中的 .NET 程序集的列表  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="3ef57-177">展开每个程序集并找到可用于调试的 *.cs*/ *.razor* 源文件。</span><span class="sxs-lookup"><span data-stu-id="3ef57-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="3ef57-178">设置断点，然后切换回应用的选项卡，断点在代码执行时被命中。</span><span class="sxs-lookup"><span data-stu-id="3ef57-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="3ef57-179">命中断点后，正常单步执行 (`F10`) 代码或恢复 (`F8`) 代码执行。</span><span class="sxs-lookup"><span data-stu-id="3ef57-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="3ef57-180"> 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。</span><span class="sxs-lookup"><span data-stu-id="3ef57-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="3ef57-181">按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。</span><span class="sxs-lookup"><span data-stu-id="3ef57-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="3ef57-182">代理连接到要调试的浏览器窗口（因此需要启用远程调试）。</span><span class="sxs-lookup"><span data-stu-id="3ef57-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="3ef57-183">在 Visual Studio 中附加到进程</span><span class="sxs-lookup"><span data-stu-id="3ef57-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="3ef57-184">在 **F5** 调试开发期间，在 Visual Studio 中附加到应用进程是适用于 Blazor WebAssembly 的*临时*调试方案。</span><span class="sxs-lookup"><span data-stu-id="3ef57-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="3ef57-185">若要将正在运行的应用的进程附加到 Visual Studio，请执行以下操作：</span><span class="sxs-lookup"><span data-stu-id="3ef57-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="3ef57-186">在 Visual Studio 中，选择“调试” > “附加到进程”   。</span><span class="sxs-lookup"><span data-stu-id="3ef57-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="3ef57-187">对于“连接类型”，选择“Chrome devtools protocol websocket (无身份验证)”   。</span><span class="sxs-lookup"><span data-stu-id="3ef57-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="3ef57-188">对于“连接目标”，粘贴应用的 HTTP 地址（而非 HTTPS 地址）  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="3ef57-189">选择“刷新”以刷新“可用进程”下的条目   。</span><span class="sxs-lookup"><span data-stu-id="3ef57-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="3ef57-190">选择要调试的浏览器进程，然后选择“附加”  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="3ef57-191">在“选择代码类型”对话框中，为要附加到的特定浏览器（Edge 或 Chrome）选择代码类型，然后选择“确定”   。</span><span class="sxs-lookup"><span data-stu-id="3ef57-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="3ef57-192">浏览器源映射</span><span class="sxs-lookup"><span data-stu-id="3ef57-192">Browser source maps</span></span>

<span data-ttu-id="3ef57-193">浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。</span><span class="sxs-lookup"><span data-stu-id="3ef57-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="3ef57-194">但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。</span><span class="sxs-lookup"><span data-stu-id="3ef57-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="3ef57-195">相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。</span><span class="sxs-lookup"><span data-stu-id="3ef57-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="3ef57-196">疑难解答</span><span class="sxs-lookup"><span data-stu-id="3ef57-196">Troubleshoot</span></span>

<span data-ttu-id="3ef57-197">如果遇到错误，以下提示可能有所帮助：</span><span class="sxs-lookup"><span data-stu-id="3ef57-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="3ef57-198">在“调试程序”选项卡中，在浏览器中打开开发人员工具  。</span><span class="sxs-lookup"><span data-stu-id="3ef57-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="3ef57-199">在控制台中，执行 `localStorage.clear()` 以删除所有断点。</span><span class="sxs-lookup"><span data-stu-id="3ef57-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
