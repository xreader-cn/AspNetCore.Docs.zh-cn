---
title: 调试 ASP.NET Core Blazor
author: guardrex
description: 了解如何调试 Blazor 应用程序。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 08/22/2019
uid: blazor/debug
ms.openlocfilehash: ebdd015a8d1138d98d8fcd1b1f18b8053248e2bd
ms.sourcegitcommit: e5a74f882c14eaa0e5639ff082355e130559ba83
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/20/2019
ms.locfileid: "71168114"
---
# <a name="debug-aspnet-core-blazor"></a>调试 ASP.NET Core Blazor

[Daniel Roth](https://github.com/danroth27)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

对于在 Chrome 上调试 WebAssembly 上运行的 Blazor WebAssembly 应用，存在*早期*支持。

调试器功能受到限制。 可用方案包括：

* 设置和删除断点。
* 单步执行（`F10`），通过代码或恢复（`F8`）代码执行。
* 在 "*局部变量*" 显示中，观察类型`int`为、 `string`和`bool`的任何本地变量的值。
* 请参阅调用堆栈，其中包括从 JavaScript 到 .NET 和从 .NET 到 JavaScript 的调用链。

*不能*：

* 观察所有不是`int`、 `string`或`bool`的局部变量的值。
* 观察任何类属性或字段的值。
* 将鼠标悬停在变量上以查看其值。
* 在控制台中计算表达式的值。
* 跨异步调用单步执行。
* 执行大多数其他普通调试方案。

开发进一步的调试方案是工程团队不断关注的重点。

## <a name="prerequisites"></a>系统必备

调试需要以下任一浏览器：

* Google Chrome （版本70或更高版本）
* Microsoft Edge 预览（[边缘开发渠道](https://www.microsoftedgeinsider.com)）

## <a name="procedure"></a>过程

1. 在配置中`Debug`运行 Blazor WebAssembly 应用。 将选项传递到[dotnet run](/dotnet/core/tools/dotnet-run)命令： `dotnet run --configuration Debug`。 `--configuration Debug`
1. 在浏览器中访问应用程序。
1. 将键盘焦点置于应用上，而不是开发人员工具面板上。 启动调试时可关闭 "开发人员工具" 面板。
1. 选择以下 Blazor 特定的键盘快捷方式：
   * `Shift+Alt+D`在 Windows/Linux 上
   * `Shift+Cmd+D`在 macOS 上
1. 按照屏幕上列出的步骤，在启用远程调试的情况重启浏览器。
1. 再次选择以下 Blazor 特定的键盘快捷方式，以启动调试会话：
   * `Shift+Alt+D`在 Windows/Linux 上
   * `Shift+Cmd+D`在 macOS 上

## <a name="enable-remote-debugging"></a>启用远程调试

如果远程调试处于禁用状态，则**无法查找可调试浏览器选项卡**错误页面由 Chrome 生成。 "错误" 页包含有关运行 Chrome 的说明，其中打开了调试端口，以便 Blazor 调试代理可以连接到应用。 *关闭所有 Chrome 实例*，并按指示重新启动 chrome。

## <a name="debug-the-app"></a>调试应用

在启用了远程调试的情况下运行 Chrome 后，调试键盘快捷方式将打开一个新的 "调试器" 选项卡。一段时间后，"**源**" 选项卡显示应用程序中的 .net 程序集列表。 展开每个程序集，找到可用于调试的 *.cs*/*文件。* 设置断点，切换回应用的选项卡，并在代码执行时命中断点。 命中断点后，通过代码或恢复（`F10``F8`）代码执行的单步执行（）。

Blazor 提供了一个实现[Chrome DevTools 协议](https://chromedevtools.github.io/devtools-protocol/)的调试代理，并使用对该协议进行了补充。特定于网络的信息。 按下调试键盘快捷方式时，Blazor 会将 Chrome DevTools 指向代理。 代理连接到要调试的浏览器窗口（因此需要启用远程调试）。

## <a name="browser-source-maps"></a>浏览器源映射

浏览器源映射允许浏览器将已编译的文件映射回其原始源文件，并且通常用于客户端调试。 但 Blazor 当前不会直接C#映射到 JAVASCRIPT/WASM。 相反，Blazor 会在浏览器中执行 IL 解释，因此源映射不相关。

## <a name="troubleshooting-tip"></a>故障排除提示

如果遇到错误，下列提示可能会有所帮助：

在 "**调试器**" 选项卡上，在浏览器中打开开发人员工具。 在控制台中，执行`localStorage.clear()`以删除所有断点。
