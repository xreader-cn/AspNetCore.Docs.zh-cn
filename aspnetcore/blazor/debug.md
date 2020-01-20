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
# <a name="debug-aspnet-core-opno-locblazor"></a>调试 ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

对于使用基于 Chromium 的浏览器中的浏览器开发工具（Chrome/Edge）调试 Blazor WebAssembly，存在*早期*支持。 正在进行的工作：

* 在 Visual Studio 中完全启用调试。
* 在 Visual Studio Code 中启用调试。

调试器功能受到限制。 可用方案包括：

* 设置和删除断点。
* 通过代码执行的单步（`F10`）或继续（`F8`）代码执行。
* 在 "*局部变量*" 显示中，观察类型为 `int`、`string`和 `bool`的任何本地变量的值。
* 请参阅调用堆栈，其中包括从 JavaScript 到 .NET 和从 .NET 到 JavaScript 的调用链。

*不能*：

* 观察不是 `int`、`string`或 `bool`的任何局部变量的值。
* 观察任何类属性或字段的值。
* 将鼠标悬停在变量上以查看其值。
* 在控制台中计算表达式的值。
* 跨异步调用单步执行。
* 执行大多数其他普通调试方案。

开发进一步的调试方案是工程团队不断关注的重点。

## <a name="prerequisites"></a>先决条件

调试需要以下任一浏览器：

* Google Chrome （版本70或更高版本）
* Microsoft Edge 预览（[边缘开发渠道](https://www.microsoftedgeinsider.com)）

## <a name="procedure"></a>过程

# <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

> [!WARNING]
> Visual Studio 中的调试支持在开发的早期阶段。 当前不支持**F5**调试。

1. 在不进行调试的情况下，在 `Debug` 配置中运行 Blazor WebAssembly 应用（**Ctrl**+**f5** ，而不是按**f5**）。
1. 打开应用程序的 "调试" 属性（"**调试**" 菜单中的 "最后一项"），然后复制 "HTTP**应用程序 URL**"。 使用基于 Chromium 的浏览器（边缘 Beta 版或 Chrome）浏览到应用的 HTTP 地址（而非 HTTPS 地址）。
1. 将键盘焦点置于浏览器窗口中，而不是开发人员工具面板中。 最好将此过程的 "开发人员工具" 面板保持为关闭状态。 调试开始后，你可以重新打开 "开发人员工具" 面板。
1. 选择以下特定于 Blazor的键盘快捷方式：

   * Windows 上的 `Shift+Alt+D`
   * macOS 上的 `Shift+Cmd+D`

   如果收到 "找**不到可调试的浏览器" 选项卡**，请参阅[启用远程调试](#enable-remote-debugging)。
   
   启用远程调试后：
   
   1 \。 此时将打开一个新的浏览器窗口。 关闭前面的窗口。

   2。 将键盘焦点置于浏览器窗口中的应用程序上。

   3。 在新的浏览器窗口中选择特定于 Blazor的键盘快捷方式： `Shift+Alt+D` 在 Windows 上，或在 macOS 上的 "`Shift+Cmd+D`"。

   4 \。 " **DevTools** " 选项卡将在浏览器中打开。 **在浏览器窗口中重新选择应用的选项卡。**

   若要将应用附加到 Visual Studio，请参阅[Visual studio 中的 "附加到进程](#attach-to-process-in-visual-studio)" 部分。

# <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 通过将 `--configuration Debug` 选项传递到[dotnet run](/dotnet/core/tools/dotnet-run)命令，在 `Debug` 配置中运行 Blazor WebAssembly 应用： `dotnet run --configuration Debug`。
1. 在 shell 窗口中显示的 HTTP URL 处导航到应用。
1. 将键盘焦点置于应用上，而不是开发人员工具面板上。 最好将此过程的 "开发人员工具" 面板保持为关闭状态。 调试开始后，你可以重新打开 "开发人员工具" 面板。
1. 选择以下特定于 Blazor的键盘快捷方式：

   * Windows 上的 `Shift+Alt+D`
   * macOS 上的 `Shift+Cmd+D`

   如果收到 "找**不到可调试的浏览器" 选项卡**，请参阅[启用远程调试](#enable-remote-debugging)。
   
   启用远程调试后：
   
   1 \。 此时将打开一个新的浏览器窗口。 关闭前面的窗口。

   2。 将键盘焦点置于浏览器窗口中，而不是开发人员工具面板中。

   3。 在新的浏览器窗口中选择特定于 Blazor的键盘快捷方式： `Shift+Alt+D` 在 Windows 上，或在 macOS 上的 "`Shift+Cmd+D`"。

---

## <a name="enable-remote-debugging"></a>启用远程调试

如果远程调试处于禁用状态，则**无法查找可调试浏览器选项卡**错误页面由 Chrome 生成。 "错误" 页包含有关运行 Chrome 的说明，其中打开了调试端口，以便 Blazor 调试代理可以连接到应用程序。 *关闭所有 Chrome 实例*，并按指示重新启动 chrome。

## <a name="debug-the-app"></a>调试应用

在启用了远程调试的情况下运行 Chrome 后，调试键盘快捷方式将打开一个新的 "调试器" 选项卡。一段时间后，"**源**" 选项卡显示应用程序中的 .net 程序集列表。 展开每个程序集，找到可用于调试的 *.cs*/*razor*源文件。 设置断点，切换回应用的选项卡，并在代码执行时命中断点。 命中断点后，通过代码执行单步（`F10`）或正常执行（`F8`）代码执行。

Blazor 提供了一个调试代理，该代理实现[Chrome DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)，并使用对协议进行补充。特定于网络的信息。 按下调试键盘快捷方式时，Blazor 指向代理处的 Chrome DevTools。 代理连接到要调试的浏览器窗口（因此需要启用远程调试）。

## <a name="attach-to-process-in-visual-studio"></a>在 Visual Studio 中附加到进程

在 Visual Studio 中附加到应用程序的过程是一种用于 Blazor WebAssembly 的*临时*调试方案，而**F5**调试正在开发中。

将正在运行的应用程序的进程附加到 Visual Studio：

1. 在 Visual Studio 中，选择 "**调试**" > "**附加到进程**"。
1. 对于 "**连接类型**"，请选择 " **Chrome devtools 协议 websocket （无身份验证）** "。
1. 对于**连接目标**，请粘贴应用的 HTTP 地址（而不是 HTTPS 地址）。
1. 选择 "**刷新**" 以刷新 "**可用进程**" 下的条目。
1. 选择要调试的浏览器进程，然后选择 "**附加**"。
1. 在 "**选择代码类型**" 对话框中，选择要附加到的特定浏览器（"边缘" 或 "Chrome"）的代码类型，然后选择 **"确定"** 。

## <a name="browser-source-maps"></a>浏览器源映射

浏览器源映射允许浏览器将已编译的文件映射回其原始源文件，并且通常用于客户端调试。 但 Blazor 当前不会直接C#映射到 JAVASCRIPT/WASM。 相反，Blazor 在浏览器中执行 IL 解释，因此源映射不相关。

## <a name="troubleshoot"></a>疑难解答

如果遇到错误，下列提示可能会有所帮助：

在 "**调试器**" 选项卡上，在浏览器中打开开发人员工具。 在控制台中，执行 `localStorage.clear()` 以删除任何断点。
