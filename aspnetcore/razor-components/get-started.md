---
title: 开始使用 Razor 组件
author: guardrex
description: 了解如何开始使用 Razor 组件创建和修改 Razor 组件项目。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 04/07/2019
uid: razor-components/get-started
ms.openlocfilehash: 151e58497b0f22fa7c5a9bde1f665eeb73fd5dc3
ms.sourcegitcommit: 6bde1fdf686326c080a7518a6725e56e56d8886e
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/08/2019
ms.locfileid: "59515416"
---
# <a name="get-started-with-razor-components"></a>开始使用 Razor 组件

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

# [<a name="visual-studio"></a>Visual Studio](#tab/visual-studio)

先决条件：

[!INCLUDE[](~/includes/net-core-prereqs-vs-3.0.md)]

在 Visual Studio 中创建第一个 Razor 组件项目：

1. 安装最新[.NET Core 3.0 Preview SDK](https://dotnet.microsoft.com/download/dotnet-core/3.0)发布。
1. 启用 Visual Studio 以使用预览版 Sdk:
   1. 打开**工具** > **选项**菜单栏中。
   1. 打开**项目和解决方案**节点。 打开 **.NET Core**选项卡。
   1. 选中的复选框**使用的.NET Core SDK 预览**。 选择 **确定**。
1. 创建新项目。
1. 选择“ASP.NET Core Web 应用程序”。 选择“下一步”。
1. 中提供名称**项目名称**字段。 确认**位置**条目是否正确，或提供项目的位置。 选择“创建”。
1. 请确保 **.NET Core**并**ASP.NET Core 3.0**顶部选择。
1. 选择**Razor 组件**模板，然后选择**创建**。
1. 按 F5  运行应用。

祝贺你！ 只需运行第一个 Razor 组件应用 ！

<!--

# [Visual Studio Code](#tab/visual-studio-code)

Prerequisites:

[!INCLUDE[](~/includes/net-core-prereqs-vsc-3.0.md)]

To create your first Razor Components project in Visual Studio Code:

1. Execute the following command from a command shell:

   ```console
   dotnet new razorcomponents -o WebApplication1
   ```

1. Open the *WebApplication1* folder in Visual Studio Code.

1. Add a *.vscode* folder.

1. Add a *tasks.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/tasks.json)]

1. Add a *launch.json* file to the *.vscode* folder with the following content:

   [!code-json[](get-started/samples_snapshot/3.x/launch.json)]

1. Execute the app using the Visual Studio Code debugger.

1. In a browser, navigate to `https://localhost:5001`.

Congratulations! You just ran your first Razor Components app!

# [Visual Studio for Mac](#tab/visual-studio-mac)

.NET Core 3.0 will be supported with Visual Studio for Mac version 8.0 or later. Visual Studio for Mac version 8.0 Preview isn't available at this time.

Use the [.NET Core CLI version of this topic](xref:razor-components/get-started?tabs=netcore-cli) on macOS.

[!INCLUDE[](~/includes/net-core-prereqs-mac-3.0.md)]

To create your first project Razor Components project in Visual Studio for Mac:

1. Select **File** > **New Solution** or **New Project**.
1. In the sidebar, select **.NET Core** > **App**.
1. Select **ASP.NET Core Razor Components** and select **Next**.
1. The **Target Framework** defaults to **.NET Core 3.0**. Select **Next**.
1. In the **Project Name** field, enter `WebApplication1`. Select **Create**.
1. Select **Run** > **Run Without Debugging** to run the app *without the debugger*. Running with the debugger isn't supported at this time.

Congratulations! You just ran your first Razor Components app!
-->

# [<a name="net-core-cli"></a>.NET Core CLI](#tab/netcore-cli/)

先决条件：

* [.NET core SDK 3.0 预览](https://dotnet.microsoft.com/download/dotnet-core/3.0)

1. 若要从命令行界面创建第一个 Razor 组件项目：

   ```console
   dotnet new razorcomponents -o WebApplication1
   cd WebApplication1
   dotnet run
   ```

1. 在浏览器中导航到 `https://localhost:5001`。

祝贺你！ 只需运行第一个 Razor 组件应用 ！

---

## <a name="razor-components-project"></a>Razor 组件项目

Razor 组件使用 Razor 语法编写的但不同于 Razor 页面和 MVC 视图编译。 *.Razor*文件扩展名用于指定 Razor 组件。 Razor 页面和 MVC 视图继续使用 *.cshtml*文件扩展名。

> [!NOTE]
> 可以使用创作 razor 组件 *.cshtml*文件扩展名，只要这些文件被标识为 Razor 组件文件使用`_RazorComponentInclude`MSBuild 属性。 例如，使用 Razor 组件模板创建的应用指定的所有 *.cshtml*文件下*组件*文件夹应视为 Razor 组件：
>
> ```xml
> <_RazorComponentInclude>Components\**\*.cshtml</_RazorComponentInclude>
> ```

当应用运行时，多个页侧栏中的选项卡中有：

* 主页
* 计数器
* 提取数据

在“计数器”页上，选择“单击我”按钮，在不刷新页面的情况下增加计数器值。 增加网页中的计数器值通常需要编写 JavaScript，但 Razor 组件使用 C# 提供了更好的方法。

*WebApplication1/Components/Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter1.razor)]

请求`/counter`中指定的浏览器，通过`@page`指令在顶部，会导致计数器组件来呈现其内容。 组件将使用灵活且高效的方式更新 UI 的呈现器树的内存中表示形式呈现。

每次**Click me**按钮处于选中状态：

* `onclick`触发事件。
* 调用 `IncrementCount` 方法。
* `currentCount`会递增。
* 再次呈现该组件。

在运行时比较至以前的内容的新内容，并仅将更改的内容应用到文档对象模型 (DOM)。

将组件添加到另一个组件使用类似于 HTML 的语法。 使用属性或子内容指定组件参数。 例如，计数器组件可以已添加到应用程序的主页添加`<Counter />`元素到索引组件。

*WebApplication1/Components/Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index1.razor?highlight=7)]

运行应用。 主页都有它自己的计数器。

若要将参数添加到计数器组件，更新组件的`@functions`块：

* 添加的属性`IncrementAmount`修饰的`[Parameter]`属性。
* 增加 `currentCount` 的值时，更改 `IncrementCount` 方法以使用 `IncrementAmount`。

*WebApplication1/Components/Pages/Counter.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Counter2.razor?highlight=4,8)]

使用属性在 Home 组件的 `<Counter>` 元素中指定 `IncrementAmount` 参数。

*WebApplication1/Components/Pages/Index.razor*:

[!code-cshtml[](get-started/samples_snapshot/3.x/Index2.razor)]

运行应用。 主页具有其自己计数器，它每次按 10 递增**Click me**按钮处于选中状态。

## <a name="next-steps"></a>后续步骤

<xref:tutorials/first-razor-components-app>
