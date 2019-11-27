---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 通过使用所选工具生成 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/25/2019
no-loc:
- Blazor
uid: blazor/get-started
ms.openlocfilehash: 7d495bddde3c01c743db9757204a5cf59d8b160b
ms.sourcegitcommit: 918d7000b48a2892750264b852bad9e96a1165a7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/27/2019
ms.locfileid: "74550318"
---
# <a name="get-started-with-aspnet-core-opno-locblazor"></a>ASP.NET Core Blazor 入门

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/blazorwasm-preview-notice.md)]

Blazor入门：

::: moniker range=">= aspnetcore-3.1"

1. 安装[.Net Core 3.1 预览版 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1)。

1. 通过在命令行界面中运行以下命令来安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板。 [AspNetCore.Blazor。模板](https://www.nuget.org/packages/Microsoft.AspNetCore.Blazor.Templates/)包具有预览版本，而 Blazor WebAssembly 处于预览阶段。

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. 按照所选工具的指导进行操作：

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \。 安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含**ASP.NET 和 web 开发**工作负荷。

   2。 创建新项目。

   3。 选择 **Blazor 应用**。 选择“下一步”。

   4 \。 在“项目名称”字段提供项目名称，或接受默认项目名称。 确认**位置**项正确或提供项目的位置。 选择“创建”。

   5 \。 Blazor WebAssembly 体验，请选择 **Blazor WebAssembly 应用程序**模板。 要获得 Blazor 服务器体验，请选择 **Blazor 服务器应用程序**模板。 选择“创建”。 有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   6 \。 按 Ctrl+F5 运行应用。

   > [!NOTE]
   > 如果安装了 ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本的 Blazor Visual Studio 扩展，则可以卸载该扩展。 现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \。 安装 [Visual Studio Code](https://code.visualstudio.com/)。

   2。 安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。

   3。 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   4 \。 在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。

   5 \。 对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。 选择 **“是”** 。

   6 \。 如果使用 Blazor Server 应用程序，请使用 Visual Studio Code 调试器运行该应用程序。 如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹中执行 `dotnet run`。

   7 \。 在浏览器中导航到 `https://localhost:5001`。

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1 \。 安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。 将[更新通道切换到 "预览](/visualstudio/mac/install-preview)"。

   2。 选择 "**文件**" > "**新建解决方案**" 或创建一个**新项目**。

   3。 在边栏中，选择 " **.Net Core** > **应用**"。

   4 \。 选择 **Blazor 服务器应用程序**模板。 此时 Visual Studio for Mac 中仅提供 Blazor 服务器模板。 Blazor WebAssembly 体验，请按照 **.NET Core CLI**选项卡上的说明进行操作。选择 Blazor 服务器模板之后，选择 "**下一步**"。 有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \。 如果安装了 3.1 Preview SDK，则**目标框架**默认为 " **.net core 3.0** " （或 " **.net core 3.1** "）。 选择框架，然后选择 "**下一步**"。

   6 \。 在 "**项目名称**" 字段中，将应用命名为 `WebApplication1`。 选择“创建”。

   7 \。 选择 "**运行**" > "运行**但不调试** *" 以在没有调试器的情况下*运行应用。 通过 "**启动调试**" 运行应用程序，以*通过调试器*运行该应用程序。

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   在浏览器中导航到 `https://localhost:5001`。

   ---

::: moniker-end

::: moniker range="< aspnetcore-3.1"

1. 安装最新的[.Net Core 3.0 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)版本。

1. 还可以通过安装[.Net Core 3.1 PREVIEW SDK](https://dotnet.microsoft.com/download/dotnet-core/3.1) ，然后在命令行界面中运行以下命令来安装[Blazor WebAssembly](xref:blazor/hosting-models#blazor-webassembly)模板：

   ```dotnetcli
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.1.0-preview3.19555.2
   ```

1. 按照所选工具的指导进行操作：

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \。 安装最新的[Visual Studio](https://visualstudio.com/vs/) **和 ASP.NET 和 web 开发**工作负荷。

   2。 可以选择安装[Visual Studio 16.4 Preview 2 或更高版本](https://visualstudio.microsoft.com/vs/preview/)，其中包含用于 Blazor WebAssembly 应用开发的**ASP.NET 和 web 开发**工作负荷。

   3。 创建新项目。

   4 \。 选择 **Blazor 应用**。 选择“下一步”。

   5 \。 在“项目名称”字段提供项目名称，或接受默认项目名称。 确认**位置**项正确或提供项目的位置。 选择“创建”。

   6 \。 Blazor WebAssembly 体验，请选择 **Blazor WebAssembly 应用程序**模板。 要获得 Blazor 服务器体验，请选择 **Blazor 服务器应用程序**模板。 选择“创建”。 有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   7 \。 按 F5 运行应用。

   > [!NOTE]
   > 如果安装了 ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本的 Blazor Visual Studio 扩展，则可以卸载该扩展。 现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \。 安装 [Visual Studio Code](https://code.visualstudio.com/)。

   2。 安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。

   3。 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

      ```dotnetcli
      dotnet new blazorwasm -o WebApplication1
      ```

      若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：

      ```dotnetcli
      dotnet new blazorserver -o WebApplication1
      ```

      有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   4 \。 在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。

   5 \。 对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。 选择 **“是”** 。

   6 \。 如果使用 Blazor Server 应用程序，请使用 Visual Studio Code 调试器运行该应用程序。 如果使用 Blazor WebAssembly 应用，请从应用的项目文件夹中执行 `dotnet run`。

   7 \。 在浏览器中导航到 `https://localhost:5001`。

   # <a name="visual-studio-for-mactabvisual-studio-mac"></a>[Visual Studio for Mac](#tab/visual-studio-mac)

   1 \。 安装[Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/)。 将[更新通道切换到 "预览](/visualstudio/mac/install-preview)"。

   2。 选择 "**文件**" > "**新建解决方案**" 或创建一个**新项目**。

   3。 在边栏中，选择 " **.Net Core** > **应用**"。

   4 \。 选择 **Blazor 服务器应用程序**模板。 此时 Visual Studio for Mac 中仅提供 Blazor 服务器模板。 Blazor WebAssembly 体验，请按照 **.NET Core CLI**选项卡上的说明进行操作。选择 Blazor 服务器模板之后，选择 "**下一步**"。 有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   <!-- For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. -->

   5 \。 如果安装了 3.1 Preview SDK，则**目标框架**默认为 " **.net core 3.0** " （或 " **.net core 3.1** "）。 选择框架，然后选择 "**下一步**"。

   6 \。 在 "**项目名称**" 字段中，将应用命名为 `WebApplication1`。 选择“创建”。

   7 \。 选择 "**运行**" > "运行**但不调试** *" 以在没有调试器的情况下*运行应用。 通过 "**启动调试**" 运行应用程序，以*通过调试器*运行该应用程序。

       If a prompt appears to trust the development certificate, trust the certificate and continue.

   # <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   若要获得 Blazor 服务器体验，请在命令行界面中执行以下命令：

   ```dotnetcli
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   有关这两个 Blazor 托管模型的信息，请 *Blazor Server*和 *Blazor WebAssembly*，请参阅 <xref:blazor/hosting-models>。

   在浏览器中导航到 `https://localhost:5001`。

   ---

::: moniker-end

边栏中的选项卡上提供了多个页面：

* 主页
* 计数器
* 提取数据

在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。 在网页中递增计数器通常需要编写 JavaScript，但 Blazor 可以使用C#。

*Pages/Counter.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

浏览器中 `/counter` 的请求由顶部的 `@page` 指令指定，导致 `Counter` 组件呈现其内容。 组件呈现为呈现树的内存中表示形式，然后可以使用它以一种灵活而高效的方式更新 UI。

每次选择 "**单击我**" 按钮时：

* 引发 `onclick` 事件。
* 调用 `IncrementCount` 方法。
* `currentCount` 递增。
* 再次呈现该组件。

运行时将新内容与以前的内容进行比较，并仅将更改的内容应用到文档对象模型（DOM）。

使用 HTML 语法将组件添加到其他组件。 例如，通过向 `Index` 组件添加 `<Counter />` 元素，将 `Counter` 组件添加到应用的主页。

*Pages/Index.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

运行应用程序。 主页有由 `Counter` 组件提供的自己的计数器。

使用特性或[子内容](xref:blazor/components#child-content)指定组件参数，这些参数允许你设置子组件的属性。 若要将参数添加到 `Counter` 组件，请更新组件的 `@code` 块：

* 使用 `[Parameter]` 属性为 `IncrementAmount` 添加公共属性。
* 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

*Pages/Counter.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

使用特性指定 `Index` 组件的 `<Counter>` 元素中的 `IncrementAmount`。

*Pages/Index.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

运行应用程序。 每次选择 "**单击我**" 按钮时，`Index` 组件都有其自己的计数器，每次增加10个。 `/counter` 的 `Counter` 组件（*Counter*）继续递增1。

## <a name="next-steps"></a>后续步骤

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>其他资源

* <xref:blazor/templates>
* <xref:signalr/introduction>
