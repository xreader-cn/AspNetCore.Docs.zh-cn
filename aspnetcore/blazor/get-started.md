---
title: ASP.NET Core Blazor 入门
author: guardrex
description: 使用所选工具构建 Blazor 应用，开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 09/05/2019
uid: blazor/get-started
ms.openlocfilehash: cce91b6332295f77c639f881fe342b625fee7fca
ms.sourcegitcommit: 92c901c7f32ee9efb335d99ec4c3add2cc9f3142
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/16/2019
ms.locfileid: "71025516"
---
# <a name="get-started-with-aspnet-core-blazor"></a>ASP.NET Core Blazor 入门

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

Blazor 入门：

1. 安装最新的[.Net Core 3.0 预览版 SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)版本。

1. 在命令 shell 中运行以下命令，安装 Blazor 模板：

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::3.0.0-preview9.19457.4
   ```

1. 按照所选工具的指导进行操作：

   # <a name="visual-studiotabvisual-studio"></a>[Visual Studio](#tab/visual-studio)

   1 \。 安装**ASP.NET 和 web 开发**工作负荷的最新[Visual Studio 预览版](https://visualstudio.com/vs/preview)。

   2。 创建新项目。

   3。 选择**Blazor 应用**。 选择“下一步”。

   4 \。 在“项目名称”字段提供项目名称，或接受默认项目名称。 确认**位置**项正确或提供项目的位置。 选择“创建”。

   5 \。 对于 "Blazor WebAssembly 体验"，请选择 " **Blazor WebAssembly 应用**" 模板。 对于 Blazor 服务器体验，请选择**Blazor 服务器应用程序**模板。 选择“创建”。 有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息， <xref:blazor/hosting-models>请参阅。

   6 \。 按 F5 运行应用。

   > [!NOTE]
   > 如果安装了 Blazor Visual Studio extension for the ASP.NET Core Blazor （预览版6或更早版本）的先前预览版本，则可以卸载该扩展。 现在，在命令外壳中安装 Blazor 模板足以在 Visual Studio 中显示模板。

   # <a name="visual-studio-codetabvisual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

   1 \。 安装 [Visual Studio Code](https://code.visualstudio.com/)。

   2。 安装[ C# Visual Studio Code 扩展](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp)的最新版本。

   3。 对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

      ```console
      dotnet new blazorwasm -o WebApplication1
      ```

      对于 Blazor 服务器体验，请在命令行界面中执行以下命令：

      ```console
      dotnet new blazorserver -o WebApplication1
      ```

      有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息， <xref:blazor/hosting-models>请参阅。

   4 \。 在 Visual Studio Code 中打开 " *WebApplication1* " 文件夹。

   5 \。 对于 Blazor 服务器项目，IDE 请求你添加资产以生成和调试项目。 选择 **“是”** 。

   6 \。 如果使用的是 Blazor 服务器应用，请使用 Visual Studio Code 调试程序运行该应用程序。 如果使用 Blazor WebAssembly 应用，请从`dotnet run`应用的项目文件夹执行。

   7 \。 在浏览器中导航到 `https://localhost:5001`。

   <!--

   # [Visual Studio for Mac](#tab/visual-studio-mac)

   1\. Install [Visual Studio for Mac](https://visualstudio.microsoft.com/vs/mac/). Switch the [Update channel to Preview](/visualstudio/mac/install-preview).

   2\. Select **File** > **New Solution** or **New Project**.

   3\. In the sidebar, select **.NET Core** > **App**.

   4\. For a Blazor Server experience, select the **Blazor Server App** template. For a Blazor WebAssembly experience, select the **Blazor WebAssembly App** template. Select **Next**. For information on the two Blazor hosting models, *Blazor Server* and *Blazor WebAssembly*, see <xref:blazor/hosting-models>.

   5\. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.

   6\. In the **Project Name** field, enter `WebApplication1`. Select **Create**.

   7\. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

   -->

   # <a name="net-core-clitabnetcore-cli"></a>[.NET Core CLI](#tab/netcore-cli/)

   对于 Blazor WebAssembly 体验，请在命令行界面中执行以下命令：

   ```console
   dotnet new blazorwasm -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   对于 Blazor 服务器体验，请在命令行界面中执行以下命令：

   ```console
   dotnet new blazorserver -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

   有关这两个 Blazor 托管模型、 *Blazor 服务器*和*Blazor WebAssembly*的信息， <xref:blazor/hosting-models>请参阅。

   在浏览器中导航到 `https://localhost:5001`。

   ---

边栏中的选项卡上提供了多个页面：

* 主页
* 计数器
* 提取数据

在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。 在网页中递增计数器通常需要编写 JavaScript，但 Razor 组件使用C#可以提供更好的方法。

*Pages/Counter.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor?highlight=7,12-15)]

浏览器`/counter`中`@page`由顶部指令指定的请求会使`Counter`组件呈现其内容。 组件呈现为呈现树的内存中表示形式，然后可以使用它以一种灵活而高效的方式更新 UI。

每次选择 "**单击我**" 按钮时：

* 触发`onclick`事件。
* 调用 `IncrementCount` 方法。
* `currentCount`递增。
* 再次呈现该组件。

运行时将新内容与以前的内容进行比较，并仅将更改的内容应用到文档对象模型（DOM）。

使用 HTML 语法将组件添加到其他组件。 例如，通过`Counter` `<Counter />`向`Index`组件添加元素，将该组件添加到应用的主页。

*Pages/Index.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

运行应用。 主页有由`Counter`组件提供的自己的计数器。

使用特性或[子内容](xref:blazor/components#child-content)指定组件参数，这些参数允许你设置子组件的属性。 若要向`Counter`组件添加参数，请更新组件的`@code`块：

* `IncrementAmount`使用特性添加的公共属性。`[Parameter]`
* 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

*Pages/Counter.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=12-13,17)]

使用特性`IncrementAmount`指定`Index`组件的`<Counter>`元素中的。

*Pages/Index.razor*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor?highlight=7)]

运行应用。 每次选择 "**单击我**" 按钮时，组件都有其自己的计数器，每次递增10。`Index` 中`Counter` 的`/counter`组件（*Counter*）继续递增1。

## <a name="next-steps"></a>后续步骤

<xref:tutorials/first-blazor-app>

## <a name="additional-resources"></a>其他资源

* <xref:signalr/introduction>
