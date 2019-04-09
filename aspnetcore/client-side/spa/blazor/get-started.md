---
title: 开始使用 Blazor
author: guardrex
description: 了解如何通过创建和修改 Blazor 项目来开始使用 Blazor。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: spa/blazor/get-started
ms.openlocfilehash: b3928c2812be6f34cdf2f17295a1251106f651e5
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59068230"
---
# <a name="get-started-with-blazor"></a>开始使用 Blazor

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

[!INCLUDE[](~/includes/razor-components-preview-notice.md)]

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

先决条件：

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

在 Visual Studio 中创建第一个 Blazor 项目：

1. 安装最新[.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)发布。
1. 启用 Visual Studio 以使用预览版 Sdk:
   1. 打开**工具** > **选项**菜单栏中。
   1. 打开**项目和解决方案**节点。 打开 **.NET Core**选项卡。
   1. 选中的复选框**使用的.NET Core SDK 预览**。 选择 **确定**。
1. 安装最新[Blazor 扩展](https://go.microsoft.com/fwlink/?linkid=870389)从 Visual Studio Marketplace。 此步骤中使 Blazor 模板可供 Visual Studio。
1. 请 Blazor 模板可用于使用.NET Core CLI 与通过在命令行界面中运行以下命令：

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```
1. 创建新项目。
1. 选择“ASP.NET Core Web 应用程序”。 选择“下一步”。
1. 中提供名称**项目名称**字段。 确认**位置**条目是否正确，或提供项目的位置。 选择“创建”。
1. 请确保 **.NET Core**并**ASP.NET Core 3.0**顶部选择。
1. 选择**Blazor**模板，然后选择**创建**。
1. 按 F5  运行应用。

祝贺你！ 只需运行第一个 Blazor 应用 ！

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Blazor project in Visual Studio Code:

1. Execute the following command in a command shell:

   ```console
   dotnet new blazor -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Visual Studio code offers to create assets to build and debug the app, which includes the *tasks.json* and *launch.json* files. Select **Yes** to add the assets.

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Blazor app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Blazor project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **Blazor** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Blazor app!
-->

# [<a name="net-core-cli"></a>.NET Core CLI](#tab/netcore-cli/)

先决条件：

* [.NET core SDK 3.0 预览](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. 通过在命令行界面中运行以下命令添加 Blazor 模板：

   ```console
   dotnet new -i Microsoft.AspNetCore.Blazor.Templates::0.9.0-preview3-19154-02
   ```

1. 在命令行界面中创建第一个 Blazor 项目：

   ```console
   dotnet new blazor -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. 在浏览器中导航到 `https://localhost:5001`。

祝贺你！ 只需运行第一个 Blazor 应用 ！

---

## <a name="blazor-project"></a>Blazor 项目

当应用运行时，多个页侧栏中的选项卡中有：

* 主页
* 计数器
* 提取数据

在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。 使计数器递增一网页中通常需要编写 JavaScript，但 Blazor 提供更好的方法使用C#。

*Pages/Counter.cshtml*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.cshtml)]

请求`/counter`中指定的浏览器，通过`@page`指令在顶部，会导致计数器组件来呈现其内容。 组件将使用灵活且高效的方式更新 UI 的呈现器树的内存中表示形式呈现。

每次**Click me**按钮处于选中状态：

* `onclick`触发事件。
* 调用 `IncrementCount` 方法。
* `currentCount`会递增。
* 再次呈现该组件。

在运行时比较至以前的内容的新内容，并仅将更改的内容应用到文档对象模型 (DOM)。

将组件添加到另一个组件使用类似于 HTML 的语法。 使用属性或子内容指定组件参数。 例如，计数器组件可以已添加到应用程序的主页添加`<Counter />`元素到索引组件。

在中*pages/Index.cshtml*，调查提示组件替换为计数器组件：

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.cshtml?highlight=7)]

运行应用。 主页都有它自己的计数器。

若要将参数添加到计数器组件，更新组件的`@functions`块：

* 添加的属性`IncrementAmount`修饰的`[Parameter]`属性。
* 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

*Pages/Counter.cshtml*：

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.cshtml?highlight=4,8)]

使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。

Pages/Index.cshtml：

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.cshtml)]

运行应用。 主页具有其自己计数器，它每次按 10 递增**Click me**按钮处于选中状态。

## <a name="next-steps"></a>后续步骤

<xref:tutorials/first-razor-components-app>
