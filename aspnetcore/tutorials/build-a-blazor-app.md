---
title: 生成 Blazor 待办事项列表应用
author: guardrex
description: 逐步生成 Blazor 应用。
monikerRange: '>= aspnetcore-3.0'
ms.author: riande
ms.custom: mvc
ms.date: 11/24/2020
no-loc:
- appsettings.json
- ASP.NET Core Identity
- cookie
- Cookie
- Blazor
- Blazor Server
- Blazor WebAssembly
- Identity
- Let's Encrypt
- Razor
- SignalR
uid: tutorials/build-a-blazor-app
ms.openlocfilehash: a32655b8afedb73ad436f023d2f821b6920c2edd
ms.sourcegitcommit: 59d95a9106301d5ec5c9f612600903a69c4580ef
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/25/2020
ms.locfileid: "95870433"
---
# <a name="build-a-no-locblazor-todo-list-app"></a>生成 Blazor 待办事项列表应用

作者：[Daniel Roth](https://github.com/danroth27) 和 [Luke Latham](https://github.com/guardrex)

本教程演示如何生成和修改 Blazor 应用。 您将学习如何：

> [!div class="checklist"]
> * 创建待办事项列表 Blazor 应用项目
> * 修改 Razor 组件
> * 在组件中使用事件处理和数据绑定
> * 在 Blazor 应用中使用路由

在本教程结束时，你将拥有一个正常运行的待办事项列表应用。

## <a name="prerequisites"></a>必备条件

::: moniker range=">= aspnetcore-5.0"

[!INCLUDE[](~/includes/5.0-SDK.md)]

::: moniker-end

::: moniker range="< aspnetcore-5.0"

[!INCLUDE[](~/includes/3.1-SDK.md)]

::: moniker-end

## <a name="create-a-todo-list-no-locblazor-app"></a>创建待办事项列表 Blazor 应用

1. 在命令行界面中创建名为 `TodoList` 的新 Blazor 应用：

   ```dotnetcli
   dotnet new blazorserver -o TodoList
   ```

   上述命令将创建一个名为 `TodoList` 的文件夹来保存应用。 `TodoList` 文件夹是项目的根文件夹。 使用以下命令将目录切换到 `TodoList` 文件夹：

   ```dotnetcli
   cd TodoList
   ```

1. 使用以下命令向 `Pages` 文件夹中的应用添加一个新的 `Todo` Razor 组件：

   ```dotnetcli
   dotnet new razorcomponent -n Todo -o Pages
   ```

   > [!IMPORTANT]
   > Razor 组件文件名要求首字母大写。 打开 `Pages` 文件夹，确认 `Todo` 组件文件名以大写字母 `T` 开头。 文件名应为 `Todo.razor`。

1. 在 `Pages/Todo.razor` 中为组件提供初始标记：

   ```razor
   @page "/todo"

   <h3>Todo</h3>
   ```

   保存 `Pages/Todo.razor` 文件。

1. 将 `Todo` 组件添加到导航栏。

   `NavMenu` 组件 (`Shared/NavMenu.razor`) 用于应用的布局。 布局是可以避免应用中出现重复内容的组件。

   通过在 `Shared/NavMenu.razor` 文件中的现有列表项下添加以下列表项标记，为 `Todo` 组件添加一个 `<NavLink>` 元素：

   ```razor
   <li class="nav-item px-3">
       <NavLink class="nav-link" href="todo">
           <span class="oi oi-list-rich" aria-hidden="true"></span> Todo
       </NavLink>
   </li>
   ```

   保存 `Shared/NavMenu.razor` 文件。

1. 从 `TodoList` 文件夹，在命令行界面中执行 [`dotnet watch run`](/aspnet/core/tutorials/dotnet-watch) 命令，以生成并运行应用。 访问新的“待办事项”页面 (`https://localhost:5001/todo`)，确认指向 `Todo` 组件的边栏导航链接有效。

1. 向项目的根目录添加 `TodoItem.cs` 文件（`TodoList` 文件夹），以保存一个用于表示待办项的类。 为 `TodoItem` 类使用以下 C# 代码：

   [!code-csharp[](build-a-blazor-app/samples_snapshot/TodoItem.cs)]

1. 返回到 `Todo` 组件 (`Pages/Todo.razor`)：

   * 在 `@code` 块中为待办项添加一个字段。 `Todo` 组件使用此字段来维护待办项列表的状态。
   * 添加无序列表标记和 `foreach` 循环，以将每个待办项呈现为列表项 (`<li>`)。

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo2.razor?highlight=5-10,12-14)]

1. 该应用需要 UI 元素来将待办项添加到列表。 在未排序列表 (`<ul>...</ul>`) 下方添加一个文本输入 (`<input>`) 和一个按钮 (`<button>`)：

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo3.razor?highlight=12-13)]

1. 保存 `TodoItem.cs` 文件和更新的 `Pages/Todo.razor` 文件。 在命令行界面中，保存文件时，将自动重新生成应用。 浏览器会暂时断开与该应用的连接，并在重新建立连接后重新加载页面。

1. 选择“`Add todo`”按钮时没有任何反应，因为没有事件处理程序连接到该按钮。

1. 向 `Todo` 组件添加 `AddTodo` 方法，并使用 `@onclick` 属性注册该方法以选择按钮。 选择按钮时，会调用 `AddTodo` C# 方法：

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo4.razor?highlight=2,7-10)]

1. 要获得新待办项标题，请在 `@code` 块顶部添加 `newTodo` 字符串字段，并使用 `<input>` 元素中的 `bind` 属性将其绑定到文本输入的值：

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo5.razor?highlight=2)]

   ```razor
   <input placeholder="Something todo" @bind="newTodo" />
   ```

1. 更新 `AddTodo` 方法，将具有指定标题的 `TodoItem` 添加到列表。 通过将 `newTodo` 设置为空字符串来清除文本输入的值：

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo6.razor?highlight=19-26)]

1. 保存 `Pages/ToDo.razor` 文件。 应用程序会在命令行界面中自动重新生成。 浏览器重新连接到应用后，页面会在浏览器中重新加载。

1. 每个待办项的标题文本都可以编辑，复选框可以帮助用户跟踪已完成的项。 为每个待办项添加一个复选框输入，并将它的值绑定到 `IsDone` 属性。 将 `@todo.Title` 更改为绑定到 `@todo.Title` 的 `<input>` 元素：

   [!code-razor[](build-a-blazor-app/samples_snapshot/ToDo7.razor?highlight=5-6)]

1. 若要验证这些值是否已绑定，请更新 `<h3>` 标头以显示尚未完成的待办项计数（`IsDone` 是 `false`）。

   ```razor
   <h3>Todo (@todos.Count(todo => !todo.IsDone))</h3>
   ```

1. 已完成的 `Todo` 组件 (`Pages/Todo.razor`)：

   [!code-razor[](build-a-blazor-app/samples_snapshot/Todo1.razor)]

1. 保存 `Pages/ToDo.razor` 文件。 应用程序会在命令行界面中自动重新生成。 浏览器重新连接到应用后，页面会在浏览器中重新加载。

1. 添加待办项以测试新代码。

1. 完成后，请在命令行界面中关闭应用。 许多命令行界面接受键盘命令 <kbd>Ctrl</kbd>+<kbd>c</kbd> 来停止应用。

## <a name="next-steps"></a>后续步骤

在本教程中，你将了解：

> [!div class="checklist"]
> * 创建待办事项列表 Blazor 应用项目
> * 修改 Razor 组件
> * 在组件中使用事件处理和数据绑定
> * 在 Blazor 应用中使用路由

了解用于 ASP.NET Core Blazor 的工具：

> [!div class="nextstepaction"]
> <xref:blazor/tooling>
