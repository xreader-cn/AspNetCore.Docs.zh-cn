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
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor 入门

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

若要开始使用 Blazor，请按照所选工具的指南进行操作：

# <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

1. 安装带有“ASP.NET 和 Web 开发”工作负载的最新版本 [Visual Studio 2019](https://visualstudio.microsoft.com/downloads/)。

1. 创建新项目。

1. 选择“Blazor 应用”。 选择“下一步”。

1. 在“项目名称”字段提供项目名称，或接受默认项目名称。 确认“位置”条目正确无误或为项目提供位置。 选择“创建”。

1. 若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。 若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。 选择“创建”。

   有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 

1. 按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

1. 安装最新版本的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：

   ```dotnetcli
   dotnet --version
   ```

1. 安装最新版本的 [Visual Studio Code](https://code.visualstudio.com/)。

1. 安装最新的[适用于 Visual Studio Code 的 C# 扩展](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp)和 [JavaScript 调试程序（Nightly 版）](https://marketplace.visualstudio.com/items?itemName=ms-vscode.js-debug-nightly)，并将 `debug.javascript.usePreview` 设置为 `true`。

  若要使用 VS Code UI 将 `debug.javascript.usePreview` 设置为 `true`，请打开“文件” > “首选项” > “设置”，并搜索 `debug javascript use preview`。   选中“为 Node.js 和 Chrome 使用新的预览版 JavaScript 调试器”复选框。

1. 若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   ```

   若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   ```

   有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 

1. 在 Visual Studio Code 中打开 *WebApplication1* 文件夹。

1. IDE 要求添加资产以用于生成和调试项目。 选择 **“是”** 。

1. 按 Ctrl+F5 运行应用<kbd></kbd><kbd></kbd>。

# <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

1. 安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。

1. 选择“文件” > “新建解决方案”或从“启动窗口”创建“新项目”   。

1. 在边栏中，选择“Web 和控制台” > “应用”。 

   若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板。 若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板。 选择“下一步”。

   有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 

1. 确认以下配置：

   * “目标框架”设置为“.NET Core 3.1” 。
   * “身份验证”设置为“无身份验证” 。
   
   选择“下一步”。

1. 在“项目名称”字段中，将应用命名为 `WebApplication1`。 选择“创建”。

1. 选择“运行” > “启动而不调试”以不使用调试程序运行应用 。 使用“运行” > “开始调试”运行应用，或者使用“运行 (&#9654;)”按钮通过调试程序运行应用。 

如果出现信任开发证书的提示，请信任证书并继续操作。 信任证书需要使用用户密码和密钥链密码。

# <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

1. 安装最新版本的 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。 如果以前安装了该 SDK，可以通过在命令行界面中执行以下命令来确定已安装的版本：

   ```dotnetcli
   dotnet --version
   ```

1. 若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   有关 Blazor WebAssembly 和 Blazor Server 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。 

1. 在浏览器中导航到 `https://localhost:5001`。

---

侧栏中的选项卡提供多个页面：

* Home
* 计数器
* 提取数据

在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。 递增网页的计数器值通常需要编写 JavaScript，但借助 Blazor，可使用 C#。

*Pages/Counter.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

浏览器中针对 `/counter` 的请求（由顶部的 `@page` 指令指定）会导致 `Counter` 组件呈现其内容。 组件呈现为呈现树的内存中表现形式，之后可使用它灵活高效地更新 UI。

每次选择“单击我”按钮时会出现以下情况：

* 触发 `onclick` 事件。
* 调用 `IncrementCount` 方法。
* 递增 `currentCount` 的值。
* 再次呈现组件。

运行时将新内容与之前内容进行比较，且仅将已更改内容应用于文档对象模型 (DOM)。

使用 HTML 语法将组件添加到另一个组件。 例如，通过将 `<Counter />` 元素添加到 `Index` 组件，可将 `Counter` 组件添加到应用的主页。

*Pages/Index.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

运行应用。 主页拥有其自己的计数器，该计数器由 `Counter` 组件提供。

组件参数使用特性或[子内容](xref:blazor/components#child-content)指定，允许在子组件上设置属性。 若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：

* 使用 [`[Parameter]`](xref:Microsoft.AspNetCore.Components.ParameterAttribute) 特性为 `IncrementAmount` 添加公共属性。
* 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

*Pages/Counter.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

使用特性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount`。

*Pages/Index.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

运行应用。 `Index` 组件拥有其自己的计数器，每次选择“单击我”按钮时，计数器值递增 10。 `/counter` 处 `Counter` 组件 (*Counter.razor*) 的值继续递增 1。

## <a name="next-steps"></a>后续步骤

逐步生成 Blazor 应用：

> [!div class="nextstepaction"]
> <xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>其他资源

* <xref:blazor/templates>
* <xref:signalr/introduction>
