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
# <a name="debug-aspnet-core-blazor"></a>调试 ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

对于在基于 Chromium 的浏览器 (Chrome/Edge) 中使用浏览器开发工具调试 Blazor WebAssembly 提供*早期*支持。 以下工作正在进行：

* 在 Visual Studio 中完全启用调试。
* 在 Visual Studio Code 中启用调试。

调试程序功能有限。 可用方案包括：

* 设置和删除断点。
* 单步 (`F10`) 执行代码或恢复 (`F8`) 代码执行。
* 在“局部变量”视图中，观察类型为 `int`、`string` 和 `bool` 的所有局部变量的值  。
* 查看调用堆栈，包括从 JavaScript 到 .NET 以及从 .NET 到 JavaScript 的调用链。

你*不能*：

* 观察类型不是 `int`、`string` 或 `bool` 的任何局部变量的值。
* 观察任何类属性或字段的值。
* 将鼠标悬停在变量上以查看其值。
* 在控制台中计算表达式。
* 单步执行异步调用。
* 执行其他大部分普通调试方案。

开发进一步的调试方案是工程团队的长期工作重点。

## <a name="prerequisites"></a>先决条件

调试需要使用以下浏览器之一：

* Google Chrome（版本 70 或更高版本）
* Microsoft Edge 预览版（[Edge 开发通道](https://www.microsoftedgeinsider.com)）

## <a name="procedure"></a>过程

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Visual Studio 中的调试支持处于早期开发阶段。 当前不支持 **F5** 调试。

1. 在 `Debug` 配置中运行 Blazor WebAssembly 应用而不进行调试（**Ctrl**+**F5**，而不是 **F5**）。
1. 打开应用的调试属性（“调试”菜单中的最后一项），然后复制 HTTP“应用 URL”   。 使用基于 Chromium 的浏览器（Edge Beta 或 Chrome）浏览到应用的 HTTP 地址（而非 HTTPS 地址）。
1. 在浏览器窗口中将键盘焦点置于应用上，而不是开发人员工具面板上。 对于此过程，最好保持关闭开发人员工具面板。 调试开始后，可以重新打开开发人员工具面板。
1. 选择以下特定于 Blazor 的键盘快捷方式：

   * 在 Windows 上为 `Shift+Alt+D`
   * 在 macOS 上为 `Shift+Cmd+D`

   如果收到“无法找到可调试的浏览器选项卡”，请参阅[启用远程调试](#enable-remote-debugging)  。
   
   启用远程调试后：
   
   1\. 新的浏览器窗口将打开。 关闭之前的窗口。

   2\. 在浏览器窗口中将键盘焦点置于应用上。

   3\. 在新的浏览器窗口中选择特定于 Blazor 的键盘快捷方式：在 Windows 上为 `Shift+Alt+D`，在 macOS 上为 `Shift+Cmd+D`。

   4\. “开发者工具”选项卡在浏览器中打开  。 **在浏览器窗口中重新选择应用所在的选项卡。**

   若要将应用附加到 Visual Studio，请参阅[在 Visual Studio 中附加到进程](#attach-to-process-in-visual-studio)部分。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 通过将 `--configuration Debug` 选项传递给 [dotnet run](/dotnet/core/tools/dotnet-run) 命令 `dotnet run --configuration Debug`，在 `Debug` 配置中运行 Blazor WebAssembly 应用。
1. 在 shell 窗口中所示的 HTTP URL 中导航到应用。
1. 将键盘焦点置于应用上，而不是开发人员工具面板上。 对于此过程，最好保持关闭开发人员工具面板。 调试开始后，可以重新打开开发人员工具面板。
1. 选择以下特定于 Blazor 的键盘快捷方式：

   * 在 Windows 上为 `Shift+Alt+D`
   * 在 macOS 上为 `Shift+Cmd+D`

   如果收到“无法找到可调试的浏览器选项卡”，请参阅[启用远程调试](#enable-remote-debugging)  。
   
   启用远程调试后：
   
   1\. 新的浏览器窗口将打开。 关闭之前的窗口。

   2\. 在浏览器窗口中将键盘焦点置于应用上，而不是开发人员工具面板上。

   3\. 在新的浏览器窗口中选择特定于 Blazor 的键盘快捷方式：在 Windows 上为 `Shift+Alt+D`，在 macOS 上为 `Shift+Cmd+D`。

---

## <a name="enable-remote-debugging"></a>启用远程调试

如果禁用远程调试，则 Chrome 会生成“无法找到可调试的浏览器选项卡”错误页  。 错误页包含在打开调试端口的情况下运行 Chrome 的说明，以便 Blazor 调试代理可以连接到应用。 *关闭所有 Chrome 实例*，然后按照说明重启 Chrome。

## <a name="debug-the-app"></a>调试应用

Chrome 在启用远程调试的情况下运行后，调试键盘快捷方式会打开新的调试程序选项卡。片刻后，“源”选项卡显示应用中的 .NET 程序集的列表  。 展开每个程序集并找到可用于调试的 *.cs*/ *.razor* 源文件。 设置断点，然后切换回应用的选项卡，断点在代码执行时被命中。 命中断点后，正常单步执行 (`F10`) 代码或恢复 (`F8`) 代码执行。

Blazor 提供调试代理，该代理实现 [Chrome DevTools Protocol](https://chromedevtools.github.io/devtools-protocol/)，并使用特定于 .NET 的信息扩展该协议。 按下调试键盘快捷方式后，Blazor 会将 Chrome 开发者工具指向代理。 代理连接到要调试的浏览器窗口（因此需要启用远程调试）。

## <a name="attach-to-process-in-visual-studio"></a>在 Visual Studio 中附加到进程

在 **F5** 调试开发期间，在 Visual Studio 中附加到应用进程是适用于 Blazor WebAssembly 的*临时*调试方案。

若要将正在运行的应用的进程附加到 Visual Studio，请执行以下操作：

1. 在 Visual Studio 中，选择“调试” > “附加到进程”   。
1. 对于“连接类型”，选择“Chrome devtools protocol websocket (无身份验证)”   。
1. 对于“连接目标”，粘贴应用的 HTTP 地址（而非 HTTPS 地址）  。
1. 选择“刷新”以刷新“可用进程”下的条目   。
1. 选择要调试的浏览器进程，然后选择“附加”  。
1. 在“选择代码类型”对话框中，为要附加到的特定浏览器（Edge 或 Chrome）选择代码类型，然后选择“确定”   。

## <a name="browser-source-maps"></a>浏览器源映射

浏览器源映射允许浏览器将编译后的文件映射回其原始源文件，并且通常用于客户端调试。 但是，Blazor 当前不直接将 C# 映射到 JavaScript/WASM。 相反，Blazor 在浏览器中进行 IL 解释，因此源映射不相关。

## <a name="troubleshoot"></a>疑难解答

如果遇到错误，以下提示可能有所帮助：

在“调试程序”选项卡中，在浏览器中打开开发人员工具  。 在控制台中，执行 `localStorage.clear()` 以删除所有断点。
