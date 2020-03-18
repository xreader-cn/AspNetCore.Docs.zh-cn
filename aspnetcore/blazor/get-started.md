---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选的工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.1'
ms.author: riande
ms.custom: mvc
ms.date: 01/28/2019
no-loc:
- Blazor
- SignalR
uid: blazor/get-started
ms.openlocfilehash: bd33d874b3d6122f2ab820e9b147b0e62ba03a58
ms.sourcegitcommit: 9a129f5f3e31cc449742b164d5004894bfca90aa
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/06/2020
ms.locfileid: "78648630"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor 入门

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor 入门：

1. 安装 [.NET Core 3.1 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

1. （可选）安装 [Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly) 模板：
   * 安装 [.NET Core 3.1 或更高版本（预览版）SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。
   * 在命令行界面中运行以下命令。 当 Blazor WebAssembly 处于预览状态时，[ Microsoft.AspNetCore.Blazor.Templates ](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/) 包具有预览版本。

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.2.0-preview1.20073.1
   ```

1. 按照所选工具的指南进行操作：

   # <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

   1\. 安装 [Visual Studio 2019 版本 16.4 或更高版本](https://visualstudio.microsoft.com/vs/preview/)以及 **ASP.NET 和 Web 开发**工作负荷。

   2\. 创建新项目。

   3\. 选择“Blazor 应用”  。 选择“下一步”  。

   4\. 在“项目名称”字段提供项目名称，或接受默认项目名称  。 确认“位置”  条目正确无误或为项目提供位置。 选择“创建”  。

   5\. 若要获得 Blazor WebAssembly 体验，请选择“Blazor WebAssembly 应用”模板  。 若要获得 Blazor Server 体验，请选择“Blazor Server 应用”模板  。 选择“创建”  。 有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。

   6\. 按 Ctrl+F5 运行应用   。

   > [!NOTE]
   > 如果为 ASP.NET Core Blazor 之前的预览版本（预览版 6 或更早版本）安装了 Blazor Visual Studio 扩展，则可卸载该扩展。 现在，在命令行界面中安装 Blazor 模板即可在 Visual Studio 中显示这些模板。

   # <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1\. 安装 [Visual Studio Code](https://code.visualstudio.com/)。

   2\. 安装最新的 [C# for Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)。

   3\. 若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      若要获得 Blazor Server 体验，请在命令行界面中执行以下命令：

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。

   4\. 在 Visual Studio Code 中打开 *WebApplication1* 文件夹。

   5\. 对于 Blazor Server 项目，IDE 要求添加资产用于生成和调试项目。 选择 **“是”** 。

   6\. 如果使用 Blazor Server 应用，请使用 Visual Studio Code 调试程序运行该应用。 如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹执行 `dotnet run`。

   7\. 在浏览器中导航到 `https://localhost:5001`。

   # <a name="visual-studio-for-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1\. 安装 [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。

   2\. 选择“文件” > “新建解决方案”或创建“新项目”    。

   3\. 在边栏中选择“.NET Core” > “应用”   。

   4\. 选择“Blazor Server 应用”模板  。 目前，Visual Studio for Mac 中仅提供 Blazor Server 模板。 若要获得 Blazor WebAssembly 体验，请按照“.NET Core CLI”选项卡中的说明进行操作  。选择 Blazor Server 模板后，选择“下一步”  。 有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5\. 将“目标框架”设置为“.NET Core 3.1”，然后选择“下一步”    。

   6\. 在“项目名称”字段中，将应用命名为 `WebApplication1`  。 选择“创建”  。

   7\. 选择“运行” > “运行而不调试”以*不使用调试程序*运行应用   。 使用“开始调试”运行应用，以*使用调试程序*运行应用  。

   如果出现信任开发证书的提示，请信任证书并继续操作。

   # <a name="net-core-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   若要获得 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

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

   有关 *Blazor Server* 和 *Blazor WebAssembly* 这两个 Blazor 托管模型的信息，请参阅 <xref:blazor/hosting-models>。

   在浏览器中导航到 `https://localhost:5001`。

   ---

侧栏中的选项卡提供多个页面：

* Home
* 计数器
* 提取数据

在“计数器”页上，选择“单击我”  按钮，在不刷新页面的情况下增加计数器值。 递增网页的计数器值通常需要编写 JavaScript，但借助 Blazor，可使用 C#。

*Pages/Counter.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

浏览器中针对 `/counter` 的请求（由顶部的 `@page` 指令指定）会导致 `Counter` 组件呈现其内容。 组件呈现为呈现树的内存中表现形式，之后可使用它灵活高效地更新 UI。

每次选择“单击我”按钮时会出现以下情况  ：

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

* 使用 `[Parameter]` 特性为 `IncrementAmount` 添加公共属性。
* 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

*Pages/Counter.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

使用特性在 `Index` 组件的 `<Counter>` 元素中指定 `IncrementAmount`。

*Pages/Index.razor*：

[!code-razor[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

运行应用。 `Index` 组件拥有其自己的计数器，每次选择“单击我”按钮时，计数器值递增 10  。 `/counter` 处 `Counter` 组件 (*Counter.razor*) 的值继续递增 1。

## <a name="next-steps"></a>后续步骤

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>其他资源

* <xref:blazor/templates>
* <xref:signalr/introduction>
