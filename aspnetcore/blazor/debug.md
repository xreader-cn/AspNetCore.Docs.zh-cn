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
ms.sourcegitcommit: 9ee99300a48c810ca6fd4f7700cd95c3ccb85972
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/17/2020
ms.locfileid: "76159984"
---
# <a name="debug-aspnet-core-opno-locblazor"></a><span data-ttu-id="2a5c2-103">调试 ASP.NET Core [!OP.NO-LOC(Blazor)]</span><span class="sxs-lookup"><span data-stu-id="2a5c2-103">Debug ASP.NET Core [!OP.NO-LOC(Blazor)]</span></span>

[<span data-ttu-id="2a5c2-104">Daniel Roth</span><span class="sxs-lookup"><span data-stu-id="2a5c2-104">Daniel Roth</span></span>](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

<span data-ttu-id="2a5c2-105">对于使用基于 Chromium 的浏览器中的浏览器开发工具（Chrome/Microsoft Edge）调试 [!OP.NO-LOC(Blazor)] WebAssembly，存在*早期*支持。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-105">*Early* support exists for debugging [!OP.NO-LOC(Blazor)] WebAssembly using the browser dev tools in Chromium-based browsers (Chrome/Edge).</span></span> <span data-ttu-id="2a5c2-106">正在进行的工作：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-106">Work is in progress to:</span></span>

* <span data-ttu-id="2a5c2-107">在 Visual Studio 中完全启用调试。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-107">Fully enable debugging in Visual Studio.</span></span>
* <span data-ttu-id="2a5c2-108">在 Visual Studio Code 中启用调试。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-108">Enable debugging in Visual Studio Code.</span></span>

<span data-ttu-id="2a5c2-109">调试器功能受到限制。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-109">Debugger capabilities are limited.</span></span> <span data-ttu-id="2a5c2-110">可用方案包括：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-110">Available scenarios include:</span></span>

* <span data-ttu-id="2a5c2-111">设置和删除断点。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-111">Set and remove breakpoints.</span></span>
* <span data-ttu-id="2a5c2-112">通过代码执行的单步（`F10`）或继续（`F8`）代码执行。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-112">Single-step (`F10`) through the code or resume (`F8`) code execution.</span></span>
* <span data-ttu-id="2a5c2-113">在 "*局部变量*" 显示中，观察类型为 `int`、`string`和 `bool`的任何本地变量的值。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-113">In the *Locals* display, observe the values of any local variables of type `int`, `string`, and `bool`.</span></span>
* <span data-ttu-id="2a5c2-114">请参阅调用堆栈，其中包括从 JavaScript 到 .NET 和从 .NET 到 JavaScript 的调用链。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-114">See the call stack, including call chains that go from JavaScript into .NET and from .NET to JavaScript.</span></span>

<span data-ttu-id="2a5c2-115">*不能*：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-115">You *can't*:</span></span>

* <span data-ttu-id="2a5c2-116">观察不是 `int`、`string`或 `bool`的任何局部变量的值。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-116">Observe the values of any locals that aren't an `int`, `string`, or `bool`.</span></span>
* <span data-ttu-id="2a5c2-117">观察任何类属性或字段的值。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-117">Observe the values of any class properties or fields.</span></span>
* <span data-ttu-id="2a5c2-118">将鼠标悬停在变量上以查看其值。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-118">Hover over variables to see their values.</span></span>
* <span data-ttu-id="2a5c2-119">在控制台中计算表达式的值。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-119">Evaluate expressions in the console.</span></span>
* <span data-ttu-id="2a5c2-120">跨异步调用单步执行。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-120">Step across async calls.</span></span>
* <span data-ttu-id="2a5c2-121">执行大多数其他普通调试方案。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-121">Perform most other ordinary debugging scenarios.</span></span>

<span data-ttu-id="2a5c2-122">开发进一步的调试方案是工程团队不断关注的重点。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-122">Development of further debugging scenarios is an on-going focus of the engineering team.</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2a5c2-123">先决条件</span><span class="sxs-lookup"><span data-stu-id="2a5c2-123">Prerequisites</span></span>

<span data-ttu-id="2a5c2-124">调试需要以下任一浏览器：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-124">Debugging requires either of the following browsers:</span></span>

* <span data-ttu-id="2a5c2-125">Google Chrome （版本70或更高版本）</span><span class="sxs-lookup"><span data-stu-id="2a5c2-125">Google Chrome (version 70 or later)</span></span>
* <span data-ttu-id="2a5c2-126">Microsoft Edge 预览（[边缘开发渠道](https://www.microsoftedgeinsider.com)）</span><span class="sxs-lookup"><span data-stu-id="2a5c2-126">Microsoft Edge preview ([Edge Dev Channel](https://www.microsoftedgeinsider.com))</span></span>

## <a name="procedure"></a><span data-ttu-id="2a5c2-127">过程</span><span class="sxs-lookup"><span data-stu-id="2a5c2-127">Procedure</span></span>

# <a name="visual-studiotabvisual-studio"></a>[<span data-ttu-id="2a5c2-128">Visual Studio</span><span class="sxs-lookup"><span data-stu-id="2a5c2-128">Visual Studio</span></span>](#tab/visual-studio)

> [!WARNING]
> <span data-ttu-id="2a5c2-129">Visual Studio 中的调试支持在开发的早期阶段。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-129">Debugging support in Visual Studio is at an early stage of development.</span></span> <span data-ttu-id="2a5c2-130">当前不支持**F5**调试。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-130">**F5** debugging isn't currently supported.</span></span>

1. <span data-ttu-id="2a5c2-131">在不进行调试的情况下，在 `Debug` 配置中运行 [!OP.NO-LOC(Blazor)] WebAssembly 应用（**Ctrl**+**f5** ，而不是按**f5**）。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-131">Run a [!OP.NO-LOC(Blazor)] WebAssembly app in `Debug` configuration without debugging (**Ctrl**+**F5** instead of **F5**).</span></span>
1. <span data-ttu-id="2a5c2-132">打开应用程序的 "调试" 属性（"**调试**" 菜单中的 "最后一项"），然后复制 "HTTP**应用程序 URL**"。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-132">Open the Debug properties of the app (last entry in the **Debug** menu) and copy the HTTP **App URL**.</span></span> <span data-ttu-id="2a5c2-133">使用基于 Chromium 的浏览器（边缘 Beta 版或 Chrome）浏览到应用的 HTTP 地址（而非 HTTPS 地址）。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-133">Browse to the HTTP address (not the HTTPS address) of the app using a Chromium-based browser (Edge Beta or Chrome).</span></span>
1. <span data-ttu-id="2a5c2-134">将键盘焦点置于浏览器窗口中，而不是开发人员工具面板中。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-134">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span> <span data-ttu-id="2a5c2-135">最好将此过程的 "开发人员工具" 面板保持为关闭状态。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-135">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="2a5c2-136">调试开始后，你可以重新打开 "开发人员工具" 面板。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-136">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="2a5c2-137">选择以下特定于 [!OP.NO-LOC(Blazor)]的键盘快捷方式：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-137">Select the following [!OP.NO-LOC(Blazor)]-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="2a5c2-138">Windows 上的 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="2a5c2-138">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="2a5c2-139">macOS 上的 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="2a5c2-139">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="2a5c2-140">如果收到 "找**不到可调试的浏览器" 选项卡**，请参阅[启用远程调试](#enable-remote-debugging)。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-140">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="2a5c2-141">启用远程调试后：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-141">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="2a5c2-142">1 \。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-142">1\.</span></span> <span data-ttu-id="2a5c2-143">此时将打开一个新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-143">A new browser window opens.</span></span> <span data-ttu-id="2a5c2-144">关闭前面的窗口。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-144">Close the prior window.</span></span>

   <span data-ttu-id="2a5c2-145">2。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-145">2\.</span></span> <span data-ttu-id="2a5c2-146">将键盘焦点置于浏览器窗口中的应用程序上。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-146">Place the keyboard focus on the app in the browser window.</span></span>

   <span data-ttu-id="2a5c2-147">3。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-147">3\.</span></span> <span data-ttu-id="2a5c2-148">在新的浏览器窗口中选择特定于 [!OP.NO-LOC(Blazor)]的键盘快捷方式： `Shift+Alt+D` 在 Windows 上，或在 macOS 上的 "`Shift+Cmd+D`"。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-148">Select the [!OP.NO-LOC(Blazor)]-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

   <span data-ttu-id="2a5c2-149">4 \。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-149">4\.</span></span> <span data-ttu-id="2a5c2-150">" **DevTools** " 选项卡将在浏览器中打开。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-150">The **DevTools** tab opens in the browser.</span></span> <span data-ttu-id="2a5c2-151">**在浏览器窗口中重新选择应用的选项卡。**</span><span class="sxs-lookup"><span data-stu-id="2a5c2-151">**Reselect the app's tab in the browser window.**</span></span>

   <span data-ttu-id="2a5c2-152">若要将应用附加到 Visual Studio，请参阅[Visual studio 中的 "附加到进程](#attach-to-process-in-visual-studio)" 部分。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-152">To attach the app to Visual Studio, see the [Attach to process in Visual Studio](#attach-to-process-in-visual-studio) section.</span></span>

# <a name="net-core-clitabnetcore-cli"></a>[<span data-ttu-id="2a5c2-153">.NET Core CLI</span><span class="sxs-lookup"><span data-stu-id="2a5c2-153">.NET Core CLI</span></span>](#tab/netcore-cli/)

1. <span data-ttu-id="2a5c2-154">通过将 `--configuration Debug` 选项传递到[dotnet run](/dotnet/core/tools/dotnet-run)命令，在 `Debug` 配置中运行 [!OP.NO-LOC(Blazor)] WebAssembly 应用： `dotnet run --configuration Debug`。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-154">Run a [!OP.NO-LOC(Blazor)] WebAssembly app in `Debug` configuration by passing the `--configuration Debug` option to the [dotnet run](/dotnet/core/tools/dotnet-run) command: `dotnet run --configuration Debug`.</span></span>
1. <span data-ttu-id="2a5c2-155">在 shell 窗口中显示的 HTTP URL 处导航到应用。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-155">Navigate to the app at the HTTP URL shown in the shell's window.</span></span>
1. <span data-ttu-id="2a5c2-156">将键盘焦点置于应用上，而不是开发人员工具面板上。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-156">Place the keyboard focus on the app, not the developer tools panel.</span></span> <span data-ttu-id="2a5c2-157">最好将此过程的 "开发人员工具" 面板保持为关闭状态。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-157">It's best to keep the developer tools panel closed for this procedure.</span></span> <span data-ttu-id="2a5c2-158">调试开始后，你可以重新打开 "开发人员工具" 面板。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-158">After debugging has started, you can re-open the developer tools panel.</span></span>
1. <span data-ttu-id="2a5c2-159">选择以下特定于 [!OP.NO-LOC(Blazor)]的键盘快捷方式：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-159">Select the following [!OP.NO-LOC(Blazor)]-specific keyboard shortcut:</span></span>

   * <span data-ttu-id="2a5c2-160">Windows 上的 `Shift+Alt+D`</span><span class="sxs-lookup"><span data-stu-id="2a5c2-160">`Shift+Alt+D` on Windows</span></span>
   * <span data-ttu-id="2a5c2-161">macOS 上的 `Shift+Cmd+D`</span><span class="sxs-lookup"><span data-stu-id="2a5c2-161">`Shift+Cmd+D` on macOS</span></span>

   <span data-ttu-id="2a5c2-162">如果收到 "找**不到可调试的浏览器" 选项卡**，请参阅[启用远程调试](#enable-remote-debugging)。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-162">If you receive the **Unable to find debuggable browser tab**, see [Enable remote debugging](#enable-remote-debugging).</span></span>
   
   <span data-ttu-id="2a5c2-163">启用远程调试后：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-163">After enabling remote debugging:</span></span>
   
   <span data-ttu-id="2a5c2-164">1 \。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-164">1\.</span></span> <span data-ttu-id="2a5c2-165">此时将打开一个新的浏览器窗口。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-165">A new browser window opens.</span></span> <span data-ttu-id="2a5c2-166">关闭前面的窗口。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-166">Close the prior window.</span></span>

   <span data-ttu-id="2a5c2-167">2。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-167">2\.</span></span> <span data-ttu-id="2a5c2-168">将键盘焦点置于浏览器窗口中，而不是开发人员工具面板中。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-168">Place the keyboard focus on the app in the browser window, not the developer tools panel.</span></span>

   <span data-ttu-id="2a5c2-169">3。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-169">3\.</span></span> <span data-ttu-id="2a5c2-170">在新的浏览器窗口中选择特定于 [!OP.NO-LOC(Blazor)]的键盘快捷方式： `Shift+Alt+D` 在 Windows 上，或在 macOS 上的 "`Shift+Cmd+D`"。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-170">Select the [!OP.NO-LOC(Blazor)]-specific keyboard shortcut in the new browser window: `Shift+Alt+D` on Windows or `Shift+Cmd+D` on macOS.</span></span>

---

## <a name="enable-remote-debugging"></a><span data-ttu-id="2a5c2-171">启用远程调试</span><span class="sxs-lookup"><span data-stu-id="2a5c2-171">Enable remote debugging</span></span>

<span data-ttu-id="2a5c2-172">如果远程调试处于禁用状态，则**无法查找可调试浏览器选项卡**错误页面由 Chrome 生成。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-172">If remote debugging is disabled, an **Unable to find debuggable browser tab** error page is generated by Chrome.</span></span> <span data-ttu-id="2a5c2-173">"错误" 页包含有关运行 Chrome 的说明，其中打开了调试端口，以便 Blazor 调试代理可以连接到应用程序。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-173">The error page contains instructions for running Chrome with the debugging port open so that the Blazor debugging proxy can connect to the app.</span></span> <span data-ttu-id="2a5c2-174">*关闭所有 Chrome 实例*，并按指示重新启动 chrome。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-174">*Close all Chrome instances* and restart Chrome as instructed.</span></span>

## <a name="debug-the-app"></a><span data-ttu-id="2a5c2-175">调试应用</span><span class="sxs-lookup"><span data-stu-id="2a5c2-175">Debug the app</span></span>

<span data-ttu-id="2a5c2-176">在启用了远程调试的情况下运行 Chrome 后，调试键盘快捷方式将打开一个新的 "调试器" 选项卡。一段时间后，"**源**" 选项卡显示应用程序中的 .net 程序集列表。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-176">Once Chrome is running with remote debugging enabled, the debugging keyboard shortcut opens a new debugger tab. After a moment, the **Sources** tab shows a list of the .NET assemblies in the app.</span></span> <span data-ttu-id="2a5c2-177">展开每个程序集，找到可用于调试的 *.cs*/*razor*源文件。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-177">Expand each assembly and find the *.cs*/*.razor* source files available for debugging.</span></span> <span data-ttu-id="2a5c2-178">设置断点，切换回应用的选项卡，并在代码执行时命中断点。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-178">Set breakpoints, switch back to the app's tab, and the breakpoints are hit when the code executes.</span></span> <span data-ttu-id="2a5c2-179">命中断点后，通过代码执行单步（`F10`）或正常执行（`F8`）代码执行。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-179">After a breakpoint is hit, single-step (`F10`) through the code or resume (`F8`) code execution normally.</span></span>

Blazor<span data-ttu-id="2a5c2-180"> 提供了一个调试代理，该代理实现[Chrome DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)，并使用对协议进行补充。特定于网络的信息。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-180"> provides a debugging proxy that implements the [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/) and augments the protocol with .NET-specific information.</span></span> <span data-ttu-id="2a5c2-181">按下调试键盘快捷方式时，Blazor 指向代理处的 Chrome DevTools。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-181">When debugging keyboard shortcut is pressed, Blazor points the Chrome DevTools at the proxy.</span></span> <span data-ttu-id="2a5c2-182">代理连接到要调试的浏览器窗口（因此需要启用远程调试）。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-182">The proxy connects to the browser window you're seeking to debug (hence the need to enable remote debugging).</span></span>

## <a name="attach-to-process-in-visual-studio"></a><span data-ttu-id="2a5c2-183">在 Visual Studio 中附加到进程</span><span class="sxs-lookup"><span data-stu-id="2a5c2-183">Attach to process in Visual Studio</span></span>

<span data-ttu-id="2a5c2-184">在 Visual Studio 中附加到应用程序的过程是一种用于 Blazor WebAssembly 的*临时*调试方案，而**F5**调试正在开发中。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-184">Attaching to the app's process in Visual Studio is a *temporary* debugging scenario for Blazor WebAssembly while **F5** debugging is in development.</span></span>

<span data-ttu-id="2a5c2-185">将正在运行的应用程序的进程附加到 Visual Studio：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-185">To attach the running app's process to Visual Studio:</span></span>

1. <span data-ttu-id="2a5c2-186">在 Visual Studio 中，选择 "**调试**" > "**附加到进程**"。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-186">In Visual Studio, select **Debug** > **Attach to Process**.</span></span>
1. <span data-ttu-id="2a5c2-187">对于 "**连接类型**"，请选择 " **Chrome devtools 协议 websocket （无身份验证）** "。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-187">For the **Connection type**, select **Chrome devtools protocol websocket (no authentication)**.</span></span>
1. <span data-ttu-id="2a5c2-188">对于**连接目标**，请粘贴应用的 HTTP 地址（而不是 HTTPS 地址）。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-188">For the **Connection target**, paste in the HTTP address (not the HTTPS address) of the app.</span></span>
1. <span data-ttu-id="2a5c2-189">选择 "**刷新**" 以刷新 "**可用进程**" 下的条目。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-189">Select **Refresh** to refresh the entries under **Available processes**.</span></span>
1. <span data-ttu-id="2a5c2-190">选择要调试的浏览器进程，然后选择 "**附加**"。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-190">Select the browser process to debug and select **Attach**.</span></span>
1. <span data-ttu-id="2a5c2-191">在 "**选择代码类型**" 对话框中，选择要附加到的特定浏览器（"边缘" 或 "Chrome"）的代码类型，然后选择 **"确定"** 。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-191">In the **Select Code Type** dialog, select the code type for the specific browser you're attaching to (Edge or Chrome) and then select **OK**.</span></span>

## <a name="browser-source-maps"></a><span data-ttu-id="2a5c2-192">浏览器源映射</span><span class="sxs-lookup"><span data-stu-id="2a5c2-192">Browser source maps</span></span>

<span data-ttu-id="2a5c2-193">浏览器源映射允许浏览器将已编译的文件映射回其原始源文件，并且通常用于客户端调试。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-193">Browser source maps allow the browser to map compiled files back to their original source files and are commonly used for client-side debugging.</span></span> <span data-ttu-id="2a5c2-194">但 Blazor 当前不会直接C#映射到 JAVASCRIPT/WASM。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-194">However, Blazor doesn't currently map C# directly to JavaScript/WASM.</span></span> <span data-ttu-id="2a5c2-195">相反，Blazor 在浏览器中执行 IL 解释，因此源映射不相关。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-195">Instead, Blazor does IL interpretation within the browser, so source maps aren't relevant.</span></span>

## <a name="troubleshoot"></a><span data-ttu-id="2a5c2-196">疑难解答</span><span class="sxs-lookup"><span data-stu-id="2a5c2-196">Troubleshoot</span></span>

<span data-ttu-id="2a5c2-197">如果遇到错误，下列提示可能会有所帮助：</span><span class="sxs-lookup"><span data-stu-id="2a5c2-197">If you're running into errors, the following tip may help:</span></span>

<span data-ttu-id="2a5c2-198">在 "**调试器**" 选项卡上，在浏览器中打开开发人员工具。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-198">In the **Debugger** tab, open the developer tools in your browser.</span></span> <span data-ttu-id="2a5c2-199">在控制台中，执行 `localStorage.clear()` 以删除任何断点。</span><span class="sxs-lookup"><span data-stu-id="2a5c2-199">In the console, execute `localStorage.clear()` to remove any breakpoints.</span></span>
